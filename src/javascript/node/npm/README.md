# npm

tags: javascript, node, npm

When developing an npm module, you often want to test thing out before publishing.
There are two main ways to do this.

If you want to pull a local copy of an npm in, this can be done in two steps
using [`npm link`](https://docs.npmjs.com/cli/link).

1. Go to the module you are testing and run `npm link`
2. Go the project you want to pull the module into and run `npm link <module-name`.

If you're not quite ready to even create a separate module in an app, then
you can make use of [`local paths`](https://docs.npmjs.com/files/package.json#local-paths).
Local paths are module written into your app that get copied into `node_modules` when
you run and npm install of the for `file:...`.

If you are testing a module that has local modules, you will not be able to
install the test module without copying your local modules directories over.

For example if you are doing `npm install test-module` and your test-module
has local paths you will need to

```
mkdir -p node_modules/test-module
cp -R /path/to/test-module/local_modules node_modules/test-module/local_modules
```

This is because `npm install` only write to `node_modules` after it succeeds,
so during the install it would not be able to find an local modules.

## package.json

The scripts in ``package.json` can access the locally install `.bin` files.
For example after `npm install --save-dev jshint` you can run jshint via
`npm test` using the following addition to your `package.json` even if you do not
have jshint installed globally:

```json
  "scripts": {
    "test": "jshint app",
  },
```

## One-liners
Install package and save to `package.json` `dependecies`
```sh
npm install --save package-name
```

Install package and save to `package.json` `devDependecies`
```sh
npm install --save-dev package-name
```

Get latest version of package
```sh
npm view package-name version
```

Get version of current package, npm, node, etc.
```sh
npm version
```

Bump current package version and commit it
```sh
npm version [<newversion> | major | minor | patch | premajor | preminor | prepatch | prerelease | from-git]
```
Semver is `major.minor.patch[-pre]`. Each subcommand bumps the corresponding number. The `pre`-variant subcommands bump the corresponding number and append `-0`, if there is currently no `pre` number. If there is a prenumber, use `prerelease` to bump the `pre number. When you go to actually release, use the standard variant to remove the `pre` number.
