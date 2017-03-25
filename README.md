# api documentation for  [rimraf (v2.6.1)](https://github.com/isaacs/rimraf#readme)  [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-rimraf.svg)](https://travis-ci.org/npmdoc/node-npmdoc-rimraf)
#### A deep deletion module for node (like `rm -rf`)

[![NPM](https://nodei.co/npm/rimraf.png?downloads=true)](https://www.npmjs.com/package/rimraf)

[![apidoc](https://npmdoc.github.io/node-npmdoc-rimraf/build/screen-capture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-rimraf_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-rimraf/build..beta..travis-ci.org/apidoc.html)

![package-listing](https://npmdoc.github.io/node-npmdoc-rimraf/build/screen-capture.npmPackageListing.svg)



# package.json

```json

{
    "author": {
        "name": "Isaac Z. Schlueter",
        "email": "i@izs.me",
        "url": "http://blog.izs.me/"
    },
    "bin": {
        "rimraf": "./bin.js"
    },
    "bugs": {
        "url": "https://github.com/isaacs/rimraf/issues"
    },
    "dependencies": {
        "glob": "^7.0.5"
    },
    "description": "A deep deletion module for node (like 'rm -rf')",
    "devDependencies": {
        "mkdirp": "^0.5.1",
        "tap": "^10.1.2"
    },
    "directories": {},
    "dist": {
        "shasum": "c2338ec643df7a1b7fe5c54fa86f57428a55f33d",
        "tarball": "https://registry.npmjs.org/rimraf/-/rimraf-2.6.1.tgz"
    },
    "files": [
        "LICENSE",
        "README.md",
        "bin.js",
        "rimraf.js"
    ],
    "gitHead": "d84fe2cc6646d30a401baadcee22ae105a2d4909",
    "homepage": "https://github.com/isaacs/rimraf#readme",
    "license": "ISC",
    "main": "rimraf.js",
    "maintainers": [
        {
            "name": "isaacs",
            "email": "i@izs.me"
        }
    ],
    "name": "rimraf",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/isaacs/rimraf.git"
    },
    "scripts": {
        "test": "tap test/*.js"
    },
    "version": "2.6.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module rimraf](#apidoc.module.rimraf)
1.  [function <span class="apidocSignatureSpan">rimraf.</span>sync (p, options)](#apidoc.element.rimraf.sync)



# <a name="apidoc.module.rimraf"></a>[module rimraf](#apidoc.module.rimraf)

#### <a name="apidoc.element.rimraf.sync"></a>[function <span class="apidocSignatureSpan">rimraf.</span>sync (p, options)](#apidoc.element.rimraf.sync)
- description and source-code
```javascript
function rimrafSync(p, options) {
  options = options || {}
  defaults(options)

  assert(p, 'rimraf: missing path')
  assert.equal(typeof p, 'string', 'rimraf: path should be a string')
  assert(options, 'rimraf: missing options')
  assert.equal(typeof options, 'object', 'rimraf: options should be object')

  var results

  if (options.disableGlob || !glob.hasMagic(p)) {
    results = [p]
  } else {
    try {
      options.lstatSync(p)
      results = [p]
    } catch (er) {
      results = glob.sync(p, options.glob)
    }
  }

  if (!results.length)
    return

  for (var i = 0; i < results.length; i++) {
    var p = results[i]

    try {
      var st = options.lstatSync(p)
    } catch (er) {
      if (er.code === "ENOENT")
        return

      // Windows can EPERM on stat.  Life is suffering.
      if (er.code === "EPERM" && isWindows)
        fixWinEPERMSync(p, options, er)
    }

    try {
      // sunos lets the root user unlink directories, which is... weird.
      if (st && st.isDirectory())
        rmdirSync(p, options, null)
      else
        options.unlinkSync(p)
    } catch (er) {
      if (er.code === "ENOENT")
        return
      if (er.code === "EPERM")
        return isWindows ? fixWinEPERMSync(p, options, er) : rmdirSync(p, options, er)
      if (er.code !== "EISDIR")
        throw er

      rmdirSync(p, options, er)
    }
  }
}
```
- example usage
```shell
...
In order to use a custom file system library, you can override
specific fs functions on the options object.

If any of these functions are present on the options object, then
the supplied function will be used instead of the default fs
method.

Sync methods are only relevant for 'rimraf.sync()', of course.

For example:

'''javascript
var myCustomFS = require('some-custom-fs')

rimraf('some-thing', myCustomFS, callback)
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
