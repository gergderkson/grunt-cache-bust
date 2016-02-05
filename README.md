# grunt-cache-bust

[![npm version](https://badge.fury.io/js/grunt-cache-bust.svg)](http://badge.fury.io/js/grunt-cache-bust)
[![Build Status](https://travis-ci.org/hollandben/grunt-cache-bust.png?branch=master)](https://travis-ci.org/hollandben/grunt-cache-bust)
[![Dependency Status](https://david-dm.org/hollandben/grunt-cache-bust.svg)](https://david-dm.org/hollandben/grunt-cache-bust)
[![Join the chat at https://gitter.im/hollandben/grunt-cache-bust](https://badges.gitter.im/hollandben/grunt-cache-bust.svg)](https://gitter.im/hollandben/grunt-cache-bust?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

> Bust static assets from the cache using content hashing

* [Getting Started](#getting-started)
* [Introduction](#the-cachebust-task)
* [How it works](#how-it-works)
* [Options](#options)
* [Usage Examples](#usage-examples)
* [CDNs](#cdns)
* [Change Log](#change-log)

## PLEASE READ
This plugin recently upgraded to `v1.0.0`!! There was a big change in the way the plugin works. You can read me about the changes in issue [#147](https://github.com/hollandben/grunt-cache-bust/issues/147). Please let me know if you have any questions on the changes via [Gitter](https://gitter.im/hollandben/grunt-cache-bust) or [Twitter](https://twitter.com/hollandben)

## Getting Started
_If you haven't used [grunt][] before, be sure to check out the [Getting Started][] guide._

From the same directory as your project's [Gruntfile][Getting Started] and [package.json][], install this plugin with the following command:

```bash
npm install grunt-cache-bust --save-dev
```

[grunt]: http://gruntjs.com/
[Getting Started]: https://github.com/gruntjs/grunt/blob/devel/docs/getting_started.md
[package.json]: https://npmjs.org/doc/json.html

## The "cacheBust" task

Use the `cacheBust` task for cache busting static files in your application. This allows the assets to have a large expiry time in the browsers cache and will only be forced to use an updated file when the contents of it changes. This is a good practice.

Tell the `cacheBust` task where your static assets are and the files that reference them and let it work it's magic.

### Supported file types
All of them!!

### How it works
In your project's Gruntfile, add a new task called `cacheBust`.

This is the most basic configuration you can have:

```js
cacheBust: {
    taskName: {
        options: {
            assets: ['assets/**']
        },
        src: ['index.html']
    }
}
```

These are the two mandatory fields you need to supply:

The `assets` option that is passed to the plugin tells it what types of files you want to hash, i.e. `css` and `js` files. You must also provide the location for these files. In the example above, they live in the `assets` folder.

The `src` part of the configuration you should have seen before as it's used by pretty much every Grunt plugin. We use this to tell the plugin which files contain references to assets we're going to be adding a hash to. You can [use the `expand` configuration option here as well](http://gruntjs.com/configuring-tasks#building-the-files-object-dynamically)

**To summarise, the above configuration will hash all the files in the `assets` directory and replace any references to those files in the `index.html` file.**

### Options

#### Summary

```
// Here is a short summary of the options and some of their 
defaults. Extra details are below.
{
    algorithm: 'md5',                             // Algoirthm used for hashing files
    assets: ['css/*', 'js/*']                     // File patterns for the assets you wish to hash
    baseDir: './',                                // The base directory for all assets
    createCopies: true,                           // Create hashed copies of files
    deleteOriginals: false,                       // Delete the original file after hashing
    encoding: 'utf8',                             // The encoding used when reading/writing files
    hash: '9ef00db36970718e',                     // A user defined hash for every file. Not recommended.
    jsonOutput: false,                            // Output the original => new URLs to a JSON file
    jsonOutputFilename: 'grunt-cache-bust.json',  // The file path and name of the exported JSON. Is relative to baseDir
    length: 16,                                   // The length of the hash value
    separator: '.'                                // The separator between the original file name and hash
}
```

#### options.algorithm
Type: `String`  
Default value: `'md5'`

`algorithm` is dependent on the available algorithms supported by the version of OpenSSL on the platform. Examples are `'sha1'`, `'md5'`, `'sha256'`, `'sha512'`

#### options.assets
Type: `Array`

`assets` contains the file patterns for where all your assets live. This should point towards all the assets you wish to have busted. It uses the same glob pattern for matching files as Grunt.


#### options.baseDir
Type: `String`  
Default value: `false`

When set, `cachebust` will try to find the assets using the baseDir as base path.

```js
assets: {
    options: {
        baseDir: 'public/',
    },
    files: [{   
        expand: true,
        cwd: 'public/',
        src: ['modules/**/*.html']
    }]
}   
```

#### options.createCopies
Type: `Boolean`
Default value: `true`

When set to `false`, `cachebust` will not create hashed copies of the files. Useful if you use server rewrites to serve your files.

#### options.deleteOriginals
Type: `Boolean`  
Default value: `false`

When set, `cachebust` will delete the original versions of the files that have been copied. For example, `style.css` will be deleted after being copied to `style.dcf1d324cb50a1f9.css`. Will not delete original files with `options.createCopies` set to `false`.

#### options.encoding
Type: `String`  
Default value: `'utf8'`

The encoding of the file contents.

#### options.hash
Type: `String`

A user defined value to be used as the hash value for all files. For a more beneficial caching strategy, it's advised not to supply a hash value for all files.

#### options.jsonOutput
Type: `Boolean`  
Default value: `false`

When set as `true`, `cachbust` will create a json file with an object inside that contains key value pairs of the original file name, and the renamed md5 hash name for each file.

Output format looks like this:
```
{
  '/scripts/app.js' : '/scripts/app.23e6f7ac5623e96f.js',
  '/scripts/vendor.js': '/scripts/vendor.h421fwaj124bfaf5.js'
}
```

#### options.jsonOutputFilename
Type: `String`  
Default value: `grunt-cache-bust.json`

The file path and name of the exported JSON. It is exported relative to `baseDir`.

#### options.length
Type: `Number`  
Default value: `16`

The number of characters of the file content hash to prefix the file name with.

#### options.separator
Type: `String`  
Default value: `.`

The separator between the original file name and hash

### Usage Examples

#### The most basic setup
```js
cacheBust: {
    taskName: {
        options: {
            assets: ['assets/**']
        },
        src: ['index.html']
    }
}
```

#### Bust all assets and update references in all templates and assets
```js
cacheBust: {
    options: {
        assets: ['assets/**/*'],
        baseDir: './public/'
    },
    taskName: {
        files: [{   
            expand: true,
            cwd: 'public/',
            src: ['templates/**/*.html', 'assets/**/*']
        }]
    }
}
```

#### Inherited options for multiple tasks
```js
cacheBust: {
    options: {
        assets: ['assets/**'],
        baseDir: './public/'
    },
    staging: {
        options: {
            jsonOutput: true
        },
        src: ['index.html']
    },
    production: {
        options: {
            jsonOutput: false
        },
        src: ['index.html']
    }
}
```

### Change Log

**v1.0.0**
* Fundamental breaking changes - see issue [#147](https://github.com/hollandben/grunt-cache-bust/issues/147) for more details
* Re-wrote the way the plugin functions. Instead of finding assets in files, the plugin now goes through a given assets folder and builds an object based on the original and hashed file name. Read more about the changes in [#147](https://github.com/hollandben/grunt-cache-bust/issues/147)
* Remove string option for `jsonOutput`, enforcing the use of `jsonOutputFilename`
* Sorting and reversing to collection of assets - fixes #176
* Updated documentation

**v0.6.1**
* Support cache busting for meta tags
* Support cache busting for all favicons

**v0.6.0**
* Support cache busting for video tag
* Fix CSS processing for media queries with comments
* Use passed in grunt when registering

**v0.5.1**
* Reading files to be hashed as a buffer rather than string

**v0.5.0** - 2015-08-09
* Using Node's path module to help with getting the correct paths to assets

**v0.4.13** - 2015-02-27
* Fixes issue with deleting the original files when referenced in more than one source file.
* Fixed issue with hashe in the url of assets when referenced in CSS

**v0.4.12** - 2015-02-26 
* Fixed tests and implementation when deleting original files.

**v0.4.12** - 2015-02-25
* Ignoring data-images when parsing CSS.

**v0.4.12** - 2015-02-20
* Added support for Windows 8.1 and IE titles browser config file.

**v0.4.2** - 2015-02-19
* Tidied up tests. Improved README readability.

**v0.4.2** - 2015-02-18
* Improved detection of remote resources

**v0.4.2** - 2015-02-18
* Fix for working with relative paths

**v0.4.2** - 2015-02-15
* Added options to remove frag hints and use a local CDN. Busting multiple values in CSS files. Bust SVG xlink:href path. Override `baseDir` on a per file basis.
