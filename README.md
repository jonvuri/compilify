# compilify

This package allows you to easily create a new [Browserify](https://github.com/substack/node-browserify) transform for a compiler, where "compiler" means any tool that takes in a file and transforms it somehow. Compilify will wrap your compiler, passing it the raw file and then packing the compiled result into a Browserify module to be require()'d. This allows you to bypass all the streaming boilerplate when you are using a framework that only operates synchronously on a whole file.

The compiler can take in an options object, and a set of default options can be specified when making the compilify transform. Compilify will handle two options for you, `extensions` and `excludeExtensions`, to restrict or allow the types of files your compiler will act on.


## API

```
function compilify( compilerFunction, defaultOptions ) {

}
```

`compilerFunction` should be a function of this form:

```
function compilerFunction( file, options ) {
	
	// Perform transformation

	return transformedFile

}
```

`options` will be options that are passed in when the Browserify transform is invoked. If an option is not set via Browserify, it will be populated from `defaultOptions`. If there are no options or default options, `options` will be an empty object.

## Usage

### Creating a compiler transform

```
// package 'foobarify'

var compilify = require( 'compilify' )

function foobarCompiler( file ) {

	return file.replace( 'foo', 'bar' )

}

module.exports = compilify( myCompiler )
```

### Creating a transform with options

```
// package 'foobarify'

var compilify = require( 'compilify' )

function foobarCompiler( file, options ) {

	return file.replace( 'foo', options.replacement )

}

module.exports = compilify( foobarCompiler, { replacement: 'bar' } )
```

### Using the transform

#### On the command line 

```
$ browserify -t foobarify main.js
```

#### With the Browserify API

```
var foobarify = require( 'foobarify' )

var b = browserify( )
b.add( 'main.js' )
b.transform( foobarify )
b.bundle( ).pipe( process.stdout )
```

#### Passing options

```
$ browserify -t [ foobarify --replacement "baz" ] main.js
```

```
var foobarify = require( 'foobarify' )

var b = browserify( )
b.add( 'main.js' )
b.transform( { replacement: 'baz' }, foobarify )
b.bundle( ).pipe( process.stdout )
```
