node-lessify 
============
Version 0.0.10

[![Build Status](https://travis-ci.org/wilson428/node-lessify.png)](https://travis-ci.org/wilson428/node-lessify)

LESS 2.0 precompiler and CSS plugin for Browserify v2. Inspired by [node-underscorify](https://github.com/maxparm/node-underscorify).

When bundling an app using [Browserify](http://browserify.org/), it's often convenient to be able to include your CSS as a script that appends the style declarations to the head. This is particularly relevant for self-assembling apps that attach themselves to a page but otherwise have reserved real-estate on the DOM.

This small script allows you to `require()` your CSS or LESS files as you would any other script.

## Installation

```
npm install node-lessify
```

## Usage
Write your LESS or CSS files as you normally would, and put them somewhere where your script can find it.

Then simply require them as you might anything else:

```
require('./styles.less');
require('./mysite.css');
```

To compile the stylesheets, pass this module to browserify as a transformation on the command line.

```
browserify -t node-lessify script.js > bundle.js
```

## How it works

The stylesheets are compiled (in the case of LESS), minified, and bundle into a function that creates a new `<style>` element and appends it to the `<head>` using [native Javascript](http://stackoverflow.com/questions/524696/how-to-create-a-style-tag-with-javascript).

## Imports
LESS allows one to ```@import``` other LESS files. This module synchronously imports those dependencies at the time of the bundling. It looks for the imported files in both the directory of the parent file and the folder where the module itself lives, so it should work so long as the paths in the ```@import``` commands are correct relative to the importing file, as usual. It is not currently tested for recursive importing.

## Options

### Text Mode
[As requested](https://github.com/wilson428/node-lessify/issues/1), it is now possible to ask the transformation not to auto-append the css but merely to compile it into a string and assign it to a variable. This is accomplished by adding a `package.json` file in the directory from which browserify is run with the following properties:

    "browserify": {
        "transform": [
            [ "node-lessify", {"textMode": true } ]
        ]
    }

See the dummy app in the [test directory](/test) for an example of this in action.

### Plugins
You can pass a `plugins` argument to get less plugins like [autoprefix](https://www.npmjs.com/package/less-plugin-autoprefix):

For example (from [test.js](test/test.js)):

	var LessPluginAutoPrefix = require('less-plugin-autoprefix');
	var autoprefix= new LessPluginAutoPrefix({ browsers: ["last 2 versions"] });

	var b = browserify(sampleLESS);
	b.transform(lessify, {plugins: [autoprefix] });

Note: This does not currently work via `package.json` arguments, since the plugins need to be required separately, but we're working on it.

## Changes
**v0.0.10**: Supports backslashes in CSS. Thx, @BernieSumption!

**v0.0.9b**: README fixes

**v0.0.9a**: Allow for less plugins. Thx @henriklundgren!

**v0.0.9**: Read options from package.json the correct way, now that [Browserify allows for it](https://github.com/substack/node-browserify#btransformtr-opts).

**v0.0.8**: More useful error statements with line and column numbers

**v0.0.7**: Now throws an error instead of failing silently if there's bad LESS, per [Issue #8](https://github.com/wilson428/node-lessify/issues/8)
