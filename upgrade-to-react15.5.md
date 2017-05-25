# Update to React 15.5

## Pre Upgrade Steps
1) Run a gulp build and take note of the bundle sizes.
2) Make sure your dependencies/components are using component-archetype version13 and above.


## React Code Changes

1) Replace the following in your application/components:
- import ReactDOM into your code.
- React.render is now ReactDOM.render.

2) Please go to `pacakge.json` and update the following dependencies

```
"react": "15.5.3",
"react-dom": "15.5.3",
"react-addons-test-utils": "15.5.1",
"react-addons-pure-render-mixin": "15.5.2"
```

3) After you did the updates, please install the dependencies and run the app:

```
$ rm -rf npm-shrinkwrap.json
$ npm install
$ HOST=dev.walmart.com gulp hot
```

After running above, you might get issue:

```
Unhandled rejection Error: Cannot find module 'react/lib/ReactCompositeComponent'
    at Function.Module._resolveFilename (module.js:469:15)
    at Function.Module._load (module.js:417:25)
    at Function.Module._load (/Users/s0d00px/collections-test/node_modules/isomorphic-loader/lib/extend-require.js:81:29)
    at Module.require (module.js:497:17)
    at require (internal/module.js:20:19)
    at Object.<anonymous> (/Users/s0d00px/collections-test/node_modules/@walmart/electrode-react-ssr-profiler/lib/ssr-profiler.js:5:3
3)
    at Module._compile (module.js:570:32)
    at Module._extensions..js (module.js:579:10)
    at require.extensions.(anonymous function) (/Users/s0d00px/collections-test/node_modules/babel-register/lib/node.js:152:7)
    at Object.require.extensions.(anonymous function) [as .js] (/Users/s0d00px/collections-test/node_modules/@walmart/babel-register/
lib/node.js:152:7)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at Function.Module._load (/Users/s0d00px/collections-test/node_modules/isomorphic-loader/lib/extend-require.js:81:29)
    at Module.require (module.js:497:17)
    at require (internal/module.js:20:19)
```

This is because of the dependeny `electrode-react-ssr-profiler`, in file `electrode-react-ssr-profiler/lib/ssr-profiler.js` line `const ReactCompositeComponent = require("react-dom/lib/ReactCompositeComponent”); `, there is no longer Mixins object under ReactCompositeComponent.

Next, check where `electrode-react-ssr-profiler` dependency comin from by
```
npm ls @walmart/electrode-react-ssr-profiler
```

You shall see something similar as:
```
m-c02sl0gsg8wm:collections-test s0d00px$ npm ls @walmart/electrode-react-ssr-profiler
collection-app@31.4.1 /Users/s0d00px/collections-test
└─┬ @walmart/electrode-wml-ssr-cache-config@0.1.3
  ├── @walmart/electrode-react-ssr-profiler@0.1.4
  └── UNMET PEER DEPENDENCY react@0.14.9
```

So we have two solutions for resolving this:
- Update the internal `@walmart/electrode-react-ssr-profiler` to make it compatible with React 15.5
- Update the `@walmart/electrode-wml-ssr-cache-config` and replace internal `@walmart/electrode-react-ssr-profiler` by the oss one `electrode-react-ssr-caching`.

We picked the second one as the solution, PR is [here](https://gecgithub01.walmart.com/electrode/electrode-wml-ssr-cache-config/pull/3), released as `0.2.0-beta`.

4) Go to your `pakage.json` again and update `@walmart/electrode-wml-ssr-cache-config` to `"0.2.0-beta`. Re-install your dependencies and re-run your app.

```
$ npm install
$ HOST=dev.walmart.com gulp hot
```

You may see issues in `@walmart/electrode-react-esi` below:

```
└─┬ @walmart/electrode-react-esi@1.1.1
  ├── UNMET PEER DEPENDENCY react@^0.14.8
  └── react-addons-pure-render-mixin@0.14.8

npm WARN react-addons-pure-render-mixin@0.14.8 requires a peer of react@^0.14.8 but none was installed.
```
Go ahead and update the dependency `@walmart/electrode-react-esi`, PR is [here](https://gecgithub01.walmart.com/electrode/electrode-react-esi/pull/6), released as `2.0.0`.

5) Go to your `pakage.json` again and update `@walmart/electrode-react-esi` to `"2.0.0`. Re-install your dependencies and re-run your app.

```
$ npm install
$ HOST=dev.walmart.com gulp hot
```

Congratulations there is no more errors in the terminal!

Now go to `http://dev.walmart.com:3000/collection/47206334`, check whether the website is loading properly or browser console has any issues.

6) Commit all your changes and create a PR for your updates. It will test by travis CI.
You might get the travis CI errors from functional test as:

```
15:32:35  ✖ Selector '[data-automation-id='ProductTile-1'] [data-automation-id='Price-ProductOffer'],[data-tl-id='ProductTile-1'] [data-tl-id='Price-ProductOffer'],[data-tl-id='ProductTile-1'] [data-automation-id='Price-ProductOffer']' was not visible after 60084 millisecon
ds.  - expected "visible" but got: not visible
15:32:35     at F.BaseElCommand.fail (/Users/s0d00px/collections/node_modules/testarmada-nightwatch-extra/lib/commands/base/baseElCommand
.js:117:15)
15:32:35     at F.<anonymous> (/Users/s0d00px/collections/node_modules/testarmada-nightwatch-extra/lib/commands/getEl.js:53:16)
15:32:35     at Object.<anonymous> (/Users/s0d00px/collections/node_modules/testarmada-nightwatch-extra/lib/commands/base/baseElCommand.j
s:62:18)
15:32:35     at HttpRequest.<anonymous> (/Users/s0d00px/collections/node_modules/nightwatch/lib/index.js:339:20)
15:32:35     at emitTwo (events.js:106:13)
15:32:35     at HttpRequest.emit (events.js:191:7)
15:32:35     at HttpRequest.<anonymous> (/Users/s0d00px/collections/node_modules/nightwatch/lib/index.js:367:15)
15:32:35     at emitThree (events.js:116:13)
15:32:35     at HttpRequest.emit (events.js:194:7)
15:32:35     at IncomingMessage.<anonymous> (/Users/s0d00px/collections/node_modules/nightwatch/lib/http/request.js:155:16)
15:32:35     at emitNone (events.js:91:20)
15:32:35     at IncomingMessage.emit (events.js:185:7)
15:32:35     at endReadableNT (_stream_readable.js:974:12)
15:32:35     at _combinedTickCallback (internal/process/next_tick.js:80:11)
15:32:35     at process._tickDomainCallback (internal/process/next_tick.js:128:9)
15:32:35 FAILED:  1 assertions failed and 1 passed (1m 0s / 60516ms)
15:32:35 ----------------------------------------------------
15:32:35 TEST FAILURE: 1 assertions failed, 2 passed (1m 6s)
15:32:35  ✖ price/regular-item-price-without-discount
15:32:35    - Verify prices
15:32:35      Selector '[data-automation-id='ProductTile-1'] [data-automation-id='Price-ProductOffer'],[data-tl-id='ProductTile-1'] [data
-tl-id='Price-ProductOffer'],[data-tl-id='ProductTile-1'] [data-automation-id='Price-ProductOffer']' was not visible after 60084 millisec
onds. - Expected "visible" but got: "not visible"
15:32:35 Stopping app server locally
15:32:35 Application server STOPPED with process id: 83122
```

This is because the test is looking for the element by selector:
```
[data-automation-id='ProductTile-1'] [data-automation-id='Price-ProductOffer'],
[data-tl-id='ProductTile-1'] [data-tl-id='Price-ProductOffer'],
[data-tl-id='ProductTile-1'] [data-automation-id='Price-ProductOffer']
```

However, if you go to `http://dev.walmart.com:3000/collection/47206334` and take a look a the element, you will see `[data-tl-id='Price-ProductOffer']` or `[data-automation-id='Price-ProductOffer']` is missing.
It is related to react/product-offers [here](https://gecgithub01.walmart.com/react/product-offers)

Please go to your npmshrink-wrap.json and revert all the versions related to `wmreact-product*` back. And re-commit your changes.

After above get fixed and Travis CI passed, you are all set!
