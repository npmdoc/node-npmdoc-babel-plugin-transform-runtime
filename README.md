# api documentation for  babel-plugin-transform-runtime (v6.23.0)  [![npm package](https://img.shields.io/npm/v/npmdoc-babel-plugin-transform-runtime.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-babel-plugin-transform-runtime) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-babel-plugin-transform-runtime.svg)](https://travis-ci.org/npmdoc/node-npmdoc-babel-plugin-transform-runtime)
#### Externalise references to helpers and builtins, automatically polyfilling your code without polluting globals

[![NPM](https://nodei.co/npm/babel-plugin-transform-runtime.png?downloads=true)](https://www.npmjs.com/package/babel-plugin-transform-runtime)

[![apidoc](https://npmdoc.github.io/node-npmdoc-babel-plugin-transform-runtime/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-babel-plugin-transform-runtime_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-babel-plugin-transform-runtime/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-babel-plugin-transform-runtime/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-babel-plugin-transform-runtime/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "dependencies": {
        "babel-runtime": "^6.22.0"
    },
    "description": "Externalise references to helpers and builtins, automatically polyfilling your code without polluting globals",
    "devDependencies": {
        "babel-helper-plugin-test-runner": "^6.22.0"
    },
    "directories": {},
    "dist": {
        "shasum": "88490d446502ea9b8e7efb0fe09ec4d99479b1ee",
        "tarball": "https://registry.npmjs.org/babel-plugin-transform-runtime/-/babel-plugin-transform-runtime-6.23.0.tgz"
    },
    "keywords": [
        "babel-plugin"
    ],
    "license": "MIT",
    "main": "lib/index.js",
    "maintainers": [
        {
            "name": "amasad",
            "email": "amjad.masad@gmail.com"
        },
        {
            "name": "hzoo",
            "email": "hi@henryzoo.com"
        },
        {
            "name": "jmm",
            "email": "npm-public@jessemccarthy.net"
        },
        {
            "name": "loganfsmyth",
            "email": "loganfsmyth@gmail.com"
        },
        {
            "name": "sebmck",
            "email": "sebmck@gmail.com"
        },
        {
            "name": "thejameskyle",
            "email": "me@thejameskyle.com"
        }
    ],
    "name": "babel-plugin-transform-runtime",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "https://github.com/babel/babel/tree/master/packages/babel-plugin-transform-runtime"
    },
    "scripts": {},
    "version": "6.23.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module babel-plugin-transform-runtime](#apidoc.module.babel-plugin-transform-runtime)
1.  boolean <span class="apidocSignatureSpan">babel-plugin-transform-runtime.</span>__esModule
1.  [function <span class="apidocSignatureSpan">babel-plugin-transform-runtime.</span>default (_ref)](#apidoc.element.babel-plugin-transform-runtime.default)
1.  object <span class="apidocSignatureSpan">babel-plugin-transform-runtime.</span>definitions



# <a name="apidoc.module.babel-plugin-transform-runtime"></a>[module babel-plugin-transform-runtime](#apidoc.module.babel-plugin-transform-runtime)

#### <a name="apidoc.element.babel-plugin-transform-runtime.default"></a>[function <span class="apidocSignatureSpan">babel-plugin-transform-runtime.</span>default (_ref)](#apidoc.element.babel-plugin-transform-runtime.default)
- description and source-code
```javascript
default = function (_ref) {
  var t = _ref.types;

  function getRuntimeModuleName(opts) {
    return opts.moduleName || "babel-runtime";
  }

  function has(obj, key) {
    return Object.prototype.hasOwnProperty.call(obj, key);
  }

  var HELPER_BLACKLIST = ["interopRequireWildcard", "interopRequireDefault"];

  return {
    pre: function pre(file) {
      var moduleName = getRuntimeModuleName(this.opts);

      if (this.opts.helpers !== false) {
        file.set("helperGenerator", function (name) {
          if (HELPER_BLACKLIST.indexOf(name) < 0) {
            return file.addImport(moduleName + "/helpers/" + name, "default", name);
          }
        });
      }

      this.setDynamic("regeneratorIdentifier", function () {
        return file.addImport(moduleName + "/regenerator", "default", "regeneratorRuntime");
      });
    },


    visitor: {
      ReferencedIdentifier: function ReferencedIdentifier(path, state) {
        var node = path.node,
            parent = path.parent,
            scope = path.scope;


        if (node.name === "regeneratorRuntime" && state.opts.regenerator !== false) {
          path.replaceWith(state.get("regeneratorIdentifier"));
          return;
        }

        if (state.opts.polyfill === false) return;

        if (t.isMemberExpression(parent)) return;
        if (!has(_definitions2.default.builtins, node.name)) return;
        if (scope.getBindingIdentifier(node.name)) return;

        var moduleName = getRuntimeModuleName(state.opts);
        path.replaceWith(state.addImport(moduleName + "/core-js/" + _definitions2.default.builtins[node.name], "default", node.name
));
      },
      CallExpression: function CallExpression(path, state) {
        if (state.opts.polyfill === false) return;

        if (path.node.arguments.length) return;

        var callee = path.node.callee;
        if (!t.isMemberExpression(callee)) return;
        if (!callee.computed) return;
        if (!path.get("callee.property").matchesPattern("Symbol.iterator")) return;

        var moduleName = getRuntimeModuleName(state.opts);
        path.replaceWith(t.callExpression(state.addImport(moduleName + "/core-js/get-iterator", "default", "getIterator"), [callee
.object]));
      },
      BinaryExpression: function BinaryExpression(path, state) {
        if (state.opts.polyfill === false) return;

        if (path.node.operator !== "in") return;
        if (!path.get("left").matchesPattern("Symbol.iterator")) return;

        var moduleName = getRuntimeModuleName(state.opts);
        path.replaceWith(t.callExpression(state.addImport(moduleName + "/core-js/is-iterable", "default", "isIterable"), [path.node
.right]));
      },

      MemberExpression: {
        enter: function enter(path, state) {
          if (state.opts.polyfill === false) return;
          if (!path.isReferenced()) return;

          var node = path.node;

          var obj = node.object;
          var prop = node.property;

          if (!t.isReferenced(obj, node)) return;
          if (node.computed) return;
          if (!has(_definitions2.default.methods, obj.name)) return;

          var methods = _definitions2.default.methods[obj.name];
          if (!has(methods, prop.name)) return;

          if (path.scope.getBindingIdentifier(obj.name)) return;

          if (obj.name === "Object" && prop.name === "defineProperty" && path.parentPath.isCallExpression()) {
            var call = path.parentPath.node;
            if (call.arguments.length === 3 && t.isLiteral(call.arguments[1])) return;
          }

          var moduleName = getRuntimeModuleName(state.opts);
          path.replaceWith(state.addImport(moduleName + "/core-js/" + methods[prop.name], "default", obj.name + "$" + prop.name));
        },
        exit: function exit(path, state) {
          if (state.opts.polyfill === false) return;
          if (!path.isReferenced()) return;

          var node = path.node;

          var obj = node.object;

          if (!has(_definitions2.default.builtins, obj.name)) return;
          if (path.scope.getBindingIdentifier(obj.name)) return; ...
```
- example usage
```shell
...

var _symbol2 = _interopRequireDefault(_symbol);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var sym = (0, _symbol2.default)();

var promise = new _promise2.default();

console.log((0, _getIterator3.default)(arr));
'''

This means is that you can seamlessly use these native built-ins and static methods
without worrying about where they come from.
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
