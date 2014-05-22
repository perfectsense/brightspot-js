# RequireJS Usage Example

Assumptions:

- All files are in the `/path-to-js/` directory.
- `foo.js` is a non-AMD library that sets `Foo` globally.
- `jquery.bar.js` is a non-AMD jQuery plugin.
- `qux.js` is an AMD module.
- `main.js` contains the initialization code common to all pages.

## More Asynchronous

This is the recommended approach for most projects. Note that if you use this method, you shouldn't have any other `<script>` in the page.

### HTML

    <script type="text/javascript" data-main="/path-to-js/main.js" src="/path-to-js/require.js"></script>

### `main.js`

    // Configure RequireJS for both runtime and optimizer.
    requirejs.config({
        'shim': {
            'foo': { exports: 'Foo' },
            'jquery.bar': { deps: [ 'jquery' ] }
        }
    });

    require([ 'jquery', 'foo', 'qux', 'jquery.bar' ], function($, Foo, qux) {
    });

## Less Asynchronous

You many need to use this approach if you have to expose globals like `$` from jQuery to scripts that don't use RequireJS.

### HTML

    <!-- Synchronously load libraries that expose globals. -->
    <script type="text/javascript" src="/path-to-js/jquery.js"></script>
    <script type="text/javascript" src="/path-to-js/foo.js"></script>
    <script type="text/javascript" src="/path-to-js/jquery.bar.js"></script>

    <!-- Include RequireJS. -->
    <script type="text/javascript" src="/path-to-js/require.js"></script>

    <!-- Configure RequireJS for runtime only. -->
    <script type="text/javascript">
        requirejs.config({ 'baseUrl': '/path-to-js/' });
    </script>

    <!-- Include common initialization code. -->
    <script type="text/javascript" src="/path-to-js/main.js"</script>

### `main.js`

    // Map globals to be usable by RequireJS.
    define('jquery', function() { return $; });
    define('foo', function() { return Foo; });
    define('jquery.bar', function() { });

    // Configure RequireJS for both runtime and optimizer.
    requirejs.config({
        'shim': {
            'jquery.bar': [ 'jquery' ]
        }
    });

    require([ 'jquery', 'foo', 'qux', 'jquery.bar' ], function($, Foo, qux) {
    });

## FAQ

**Why don't you use the `data-main` attribute in the more asynchronous example?**

I think there are lots of subtle issues when you use the `data-main` attribute if you have to configure RequireJS in any way and include more scripts. Take the following HTML for example:

    <!doctype>
    <html>
        <head>
            <script type="text/javascript" data-main="/path-to-js/main" src="/path-to-js/require.js"></script>
        </head>
        <body>
            <script type="text/javascript">
                require([ 'jquery', 'jquery.bar' ], function($) {
                    // Use the jquery.bar plugin.
                });
            </script>
        </body>
    </html>

The above will actually fail randomly, even though it looks like a very straight-forward use of RequireJS. That's because the `data-main` attribute is roughly same as:

    requirejs.config({ 'baseUrl': /path-to-js/' });
    require([ 'main' ]);

And that loads the `main` module asynchronously along with `jquery` and `jquery.bar` modules required within the `<body>`. That allows for following combinations of loading order:

1. `main` &rarr; `jquery` &rarr; `jquery.bar`
2. `jquery` &rarr; `main` &rarr; `jquery.bar`
3. `jquery` &rarr; `jquery.bar` &rarr; `main`
4. `jquery.bar` &rarr; `main` &rarr; `jquery`
5. `jquery.bar` &rarr; `jquery` &rarr; `main`

\#1 is the only one that will function correctly. #2 to #5 is possible, because `main` defines the `shim` that sets all the non-AMD module dependencies, which aren't necessarily read in time due to the asynchronous loading of all the modules. There are other ways to fix this issue, but they have their own set of problems.

You could, for example, move the entire `requirejs.config` call from `main` module to the inline `<script>` block above that defines the `baseUrl`. But that would break the RequireJS optimizer, since it expects a file that contains the config.

You could separate the config into its own file and change all `require` calls to something like:

    require([ 'config' ], function() {
        require([ 'jquery', 'jquery.bar' ], function($) {
        });
    });

But that also somewhat breaks the RequireJS optimizer, since it doesn't flatten nested `require` calls by default. You could force it to, but that has its own problems and so forth.

Based on trying various methods, I think the pattern outlined in this example behaves in the least surprising and most intuitive manner while performing the best.
