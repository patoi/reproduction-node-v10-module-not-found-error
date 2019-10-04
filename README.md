# Reproduction Node.js v10.16.3 and npm 6.11.3 MODULE_NOT_FOUND error

This repo is a MODULE_NOT_FOUND bug reproduction for Node.js v10.16.3 and npm v6.11.3

## Goal

I want to release on a CI server (or locally) with https://github.com/semantic-release/semantic-release module.

On CI server (or locally) I run `npm ci`: it doesn't change package-lock.json, and all dependencies' version exactly same as described in package-lock.json (using `npm install` on CI server is not a good practice): https://docs.npmjs.com/cli/ci.html

## Problems

**Sometimes** I've got an error when it's running on Node.js v10.16.3 and npm v6.11.3. But there is no error on Node.js v12.11.0 and npm v6.11.3.

```
> reproduction-node-v10-module-not-found-error@0.0.0 prerelease /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci

npm WARN prepare removing existing node_modules/ before installation
npm ERR! code MODULE_NOT_FOUND
npm ERR! Cannot find module 'validate-npm-package-name'

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/xxx/.npm/_logs/2019-10-04T07_44_26_930Z-debug.log
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! reproduction-node-v10-module-not-found-error@0.0.0 prerelease: `npm ci`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the reproduction-node-v10-module-not-found-error@0.0.0 prerelease script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
npm WARN Local package.json exists, but node_modules missing, did you mean to install?

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/xxx/.npm/_logs/2019-10-04T07_44_27_174Z-debug.log
npm ERR! Test failed.  See above for more details.
```

`/Users/xxx/.npm/_logs/2019-10-04T07_44_27_174Z-debug.log` :

```
0 info it worked if it ends with ok
1 verbose cli [ '/Users/xxx/.nvm/versions/node/v10.16.3/bin/node',
1 verbose cli   '/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/.bin/npm',
1 verbose cli   'ci' ]
2 info using npm@6.11.3
3 info using node@v10.16.3
4 info prepare initializing installer
5 verbose prepare starting workers
6 verbose prepare installation prefix: /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
7 verbose prepare using package-lock.json
8 warn prepare removing existing node_modules/ before installation
9 verbose checkLock verifying package-lock data
10 verbose teardown shutting down workers.
11 info teardown Done in 0.001s
12 verbose stack Error: Cannot find module 'validate-npm-package-name'
12 verbose stack     at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
12 verbose stack     at Function.Module._load (internal/modules/cjs/loader.js:562:25)
12 verbose stack     at Module.require (internal/modules/cjs/loader.js:692:17)
12 verbose stack     at require (internal/modules/cjs/helpers.js:25:18)
12 verbose stack     at Result.setName (/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/npm/node_modules/npm-package-arg/npa.js:118:51)
12 verbose stack     at new Result (/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/npm/node_modules/npm-package-arg/npa.js:111:23)
12 verbose stack     at Function.resolve (/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/npm/node_modules/npm-package-arg/npa.js:58:15)
12 verbose stack     at Object.keys.forEach.name (/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/npm/node_modules/lock-verify/index.js:26:26)
12 verbose stack     at Array.forEach (<anonymous>)
12 verbose stack     at Promise.all.then.result (/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/npm/node_modules/lock-verify/index.js:25:25)
13 verbose cwd /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
14 verbose Darwin 18.7.0
15 verbose argv "/Users/xxx/.nvm/versions/node/v10.16.3/bin/node" "/Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error/node_modules/.bin/npm" "ci"
16 verbose node v10.16.3
17 verbose npm  v6.11.3
18 error code MODULE_NOT_FOUND
19 error Cannot find module 'validate-npm-package-name'
20 verbose exit [ 1, true ]
```

After errors I run `npm cache verify`  and `npm cache clean --force` : 

```
➜ npm cache verify                                              
Cache verified and compressed (~/.npm/_cacache):
Content verified: 584 (14542326 bytes)
Content garbage-collected: 1 (1702418 bytes)
Index entries: 892
Finished in 0.839s
```

## Reproduction

Clone this repo, run `npm install` and `npm test`.

Run `npm test` as many times as you need to get an error. `npm test` contains several `npm ci` for more test.

My environments is: MacOS Mojave v10.14.6 (all patch applied, Oct 4 2019), Terminal with ohmyzsh.

## There is no error on Node.js v12.11.0 and npm v6.11.3

```
➜ nvm use v12
Now using node v12.11.0 (npm v6.11.3)

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 
➜ npm test               

> reproduction-node-v10-module-not-found-error@0.0.0 test /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci && npm ci && npm ci && npm ci && npm ci

added 315 packages in 2.587s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.49s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.398s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.17s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.184s

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 took 18s 
➜ npm test

> reproduction-node-v10-module-not-found-error@0.0.0 test /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci && npm ci && npm ci && npm ci && npm ci

npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.27s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.303s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.327s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.24s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.309s

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 took 19s 
➜ npm test

> reproduction-node-v10-module-not-found-error@0.0.0 test /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci && npm ci && npm ci && npm ci && npm ci

npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.647s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.302s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.426s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.263s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.39s

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 took 20s 
➜ npm test

> reproduction-node-v10-module-not-found-error@0.0.0 test /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci && npm ci && npm ci && npm ci && npm ci

npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.256s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.338s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.638s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.362s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.358s

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 took 19s 
➜ npm test

> reproduction-node-v10-module-not-found-error@0.0.0 test /Users/xxx/work/git_local/xxx/reproduction-node-v10-module-not-found-error
> npm ci && npm ci && npm ci && npm ci && npm ci

npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.444s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.368s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.287s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.612s
npm WARN prepare removing existing node_modules/ before installation
added 315 packages in 3.311s

git_local/xxx/reproduction-node-v10-module-not-found-error on  master [!] is 📦  v0.0.0 via ⬢ v12.11.0 took 19s 
➜ 

```
