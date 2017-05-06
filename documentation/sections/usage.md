## Usage

Once installed, you should have access to the `coffee` command, which can execute scripts, compile `.coffee` files into `.js`, and provide an interactive REPL. The `coffee` command takes the following options:

| Option | Description |
| --- | --- |
| `-c, --compile` | Compile a `.coffee` script into a `.js` JavaScript file of the same name. |
| `-m, --map` | Generate source maps alongside the compiled JavaScript files. Adds `sourceMappingURL` directives to the JavaScript as well. |
| `-M, --inline-map` | Just like `--map`, but include the source map directly in the compiled JavaScript files, rather than in a separate file. |
| `-i, --interactive` | Launch an interactive CoffeeScript session to try short snippets. Identical to calling `coffee` with no arguments. |
| `-o, --output [DIR]` | Write out all compiled JavaScript files into the specified directory. Use in conjunction with `--compile` or `--watch`. |
| `-w, --watch` | Watch files for changes, rerunning the specified command when any file is updated. |
| `-p, --print` | Instead of writing out the JavaScript as a file, print it directly to **stdout**. |
| `-s, --stdio` | Pipe in CoffeeScript to STDIN and get back JavaScript over STDOUT. Good for use with processes written in other languages. An example:<br>`cat src/cake.coffee | coffee -sc` |
| `-l, --literate` | Parses the code as Literate CoffeeScript. You only need to specify this when passing in code directly over **stdio**, or using some sort of extension-less file name. |
| `-e, --eval` | Compile and print a little snippet of CoffeeScript directly from the command line. For example:<br>`coffee -e "console.log num for num in [10..1]"` |
| `-r, --require [MODULE]`&emsp; | `require()` the given module before starting the REPL or evaluating the code given with the `--eval` flag. |
| `-b, --bare` | Compile the JavaScript without the [top-level function safety wrapper](#lexical-scope). |
| `-t, --tokens` | Instead of parsing the CoffeeScript, just lex it, and print out the token stream. Used for debugging the compiler. |
| `-n, --nodes` | Instead of compiling the CoffeeScript, just lex and parse it, and print out the parse tree. Used for debugging the compiler. |
| `--nodejs` | The `node` executable has some useful options you can set, such as `--debug`, `--debug-brk`, `--max-stack-size`, and `--expose-gc`. Use this flag to forward options directly to Node.js. To pass multiple flags, use `--nodejs` multiple times. |
| `--no-header` | Suppress the “Generated by CoffeeScript” header. |

### Examples:

*   Compile a directory tree of `.coffee` files in `src` into a parallel tree of `.js` files in `lib`:<br>
    `coffee --compile --output lib/ src/`
*   Watch a file for changes, and recompile it every time the file is saved:<br>
    `coffee --watch --compile experimental.coffee`
*   Concatenate a list of files into a single script:<br>
    `coffee --join project.js --compile src/*.coffee`
*   Print out the compiled JS from a one-liner:<br>
    `coffee -bpe "alert i for i in [0..10]"`
*   All together now, watch and recompile an entire project as you work on it:<br>
    `coffee -o lib/ -cw src/`
*   Start the CoffeeScript REPL (`Ctrl-D` to exit, `Ctrl-V`for multi-line):<br>
    `coffee`

### ES2015+ Output

CoffeeScript 2 outputs the latest ES2015+ syntax. If you’re looking for a single tool that takes CoffeeScript input and generates JavaScript output that runs in any JavaScript runtime, assuming you opt out of certain newer features, stick to [CoffeeScript 1.x](/v1/). CoffeeScript 2 [breaks compatibility](#breaking-changes) with certain CoffeeScript 1.x features in order to conform with the ES2015+ specifications, and generate more idiomatic output (a CoffeeScript `=>` becomes an ES `=>`; a CoffeeScript `class` becomes an ES `class`; and so on).

Since the CoffeeScript 2 compiler outputs ES2015+ syntax, it is your responsibility to either ensure that your target JavaScript runtime(s) support all these features, or that you pass the output through another transpiler like [Babel](http://babeljs.io/), [Rollup](https://github.com/rollup/rollup) or [Traceur Compiler](https://github.com/google/traceur-compiler). In general, [CoffeeScript 2’s output is supported as is by Node.js 7.6+](http://node.green/), except for modules which require transpilation.

There are many great task runners for setting up JavaScript build chains, such as [Gulp](http://gulpjs.com/), [Webpack](https://webpack.github.io/), [Grunt](https://gruntjs.com/) and [Broccoli](http://broccolijs.com/). If you’re looking for a very minimal solution to get started, you can use [babel-preset-env](https://babeljs.io/docs/plugins/preset-env/) and the command line:

```bash
npm install --global coffeescript@next
npm install --save-dev coffeescript@next babel-cli babel-preset-env
coffee -p *.coffee | babel --presets env > app.js
```

### Node.js

If you’d like to use Node.js’ CommonJS to `require` CoffeeScript files, e.g. `require './app.coffee'`, you must first “register” CoffeeScript as an extension:

```coffee
require 'coffeescript/register'

App = require './app' # The .coffee extension is optional
```

If you want to use the compiler’s API, for example to make an app that compiles strings of CoffeeScript on the fly, you can `require` the full module:

```coffee
CoffeeScript = require 'coffeescript'

eval CoffeeScript.compile 'console.log "Mmmmm, I could really go for some #{Math.pi}"'
```

The `compile` method has the signature `compile(code, options)` where `code` is a string of CoffeeScript code, and the optional `options` is an object with some or all of the following properties:

* `options.sourceMap`, boolean: if true, a source map will be generated; and instead of returning a string, `compile` will return an object of the form `{js, v3SourceMap, sourceMap}`.
* `options.inlineMap`, boolean: if true, output the source map as a base64-encoded string in a comment at the bottom.
* `options.filename`, string: the filename to use for the source map.
* `options.bare`, boolean: if true, output without the [top-level function safety wrapper](#lexical-scope).
* `options.header`, boolean: if true, output the `Generated by CoffeeScript` header.