# EngOps NPM Modules

[![NPM Modules Build](https://teamcity.sencha.com/app/rest/builds/buildType:EngineeringOperations_NodeModulesTest/statusIcon "Sencha Deploy Status")](https://teamcity.sencha.com/viewType.html?buildTypeId=EngineeringOperations_NodeModulesTest&guest=1)

This is a monorepo that uses [lerna](https://www.npmjs.com/package/lerna) to
bootstrap the seperate packages together. This allows for developing on multiple
packages at the same time much more efficient than keeping them separate.

## Installation

All that is needed is a simple `npm install` command executed from the root of
this branch. This will install lerna locally and bootstrap all the packages.

## Bootstrapping

Boostrapping will go through each package and install any dependencies locally to that package.
If a package requires another package located in this monorepo, the required package
will be symlinked into the `node_modules` including the nested scoped packages. This means
if you make a change in a required package, it is available to any other package that
required it.

## Deploy

Since there are many packages in this monorepo, publishing them one by one would take a while.
Instead, there is a deploy script that, after logging into the npm server, will go through
each package and run `npm publish` on them. **NOTE** Make sure you update the `version` in the
`package.json` in the package(s) you want to publish.

To deploy to `https://test.npm.sencha.com`, run `npm run deploy`. For `https://npm.sencha.com/`,
run `npm run deploy prod`. The script will log you out of the other registry (if logged in already),
after filling out the steps to log into the appropriate registry it will then iterate through the
`packages` directory and run the `npm publish` in each.

**NOTE** There are a couple packages that are not ready to be published. In the `scripts/deploy.sh`
script there is an `IGNORE_PKGS` array that holds these packages.

Of course if you just want to deploy one or two, you can just `npm publish` in that package's directory
which would be faster than this script going through it all.

## Tests

The specs for the packages are located in the `test` directory within the individual packages.
However, to run tests, you execute `npm test` from the root directory. This will run all specs
from all packages in case a change in one package may break another package.

If you need to step through a spec's execution, set a `debugger;` statement and instead of
using the `npm test` command, use the `npm run testBrk` command. This will break before anything
executes and will say the debugger is listening. Depending on your node version, this may present
a link starting with `chrome_devtools` protocol (likely node v7-) or it may use the `ws` protocol
(likely v8+). If it's the `chrome devtools` protocol, you can copy the url provided and paste it
into chrome's address bar. If it's the `ws` protocol, open chrome dev tools and there should be
the green node.js icon in the upper-left corner. If you click that icon it will open a new
dev tools window. Either way, the debugger will be stopped so you can play it and it should then
break on your `debugger;` statement.

To only run tests on a single package, append the package name to `npm test`. For example, if you
want to run tests for the `sencha-core` package only, you would run `npm test sencha-core`.

## Test Coverage

In order to run tests and produce test coverage using [istanbul](https://istanbul.js.org/),
we use a tool called [nyc](https://github.com/istanbuljs/nyc). To run this, simple execute
`npm run cov` instead of the normal `npm test`. This will run all tests and then output the test
coverage in the cli and also create the lcov data in the `coverage` directory. Anything under 80%
coverage is marked as bad, 80%-89% is ok and 90%+ is good to promote getting above 90% coverage.

If you need to step through a test run, you should use th eabove `npm run testBrk` as you likely
don't need test coverage also. However, you can run `npm run covBrk` just like the `testBrk`
script.

To only run tests with coverage on a single package, append the package name to `npm run cov`.
For example, if you want to run tests for the `sencha-core` package only, you would run
`npm run cov sencha-core`.
