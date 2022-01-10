# Heroku Buildpack for Scoped Private Github Registry

This is a Heroku buildpack that enables authenticated npm operations
within a Heroku dyno to a Github Package rEgistry

It detects an `NPM_AUTH_TOKEN` and `NPM_SCOPE_PACKAGE` environment variable and creates a `.npmrc` file that authenticates packages with `NPM_SCOPE_PACKAGE` as the scope.

It is a fork from [NPM Authenticated Buildpack](https://github.com/nice-registry/npm-buildpack)

## Usage

Save the token in your Heroku app config:

```sh
heroku config:set NPM_AUTH_TOKEN=GITHUB_TOKEN
heroku config:set NPM_SCOPE_PACKAGE=SCOPE
```

Configure your app to use this buildpack:

```sh
heroku buildpacks:add --index 1 gosynthschool/github-registry-scoped-buildpack
```

The next time you push your app to Heroku, this buildpack will create a
`.npmrc` file containing your npm token in the base directory of the app:

```sh
heroku run bash
cat .npmrc

@yourscope:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=00000000-0000-0000-0000-000000000000
```

Now you can perform authenticated npm operations on the dyno, including
`npm publish`!

## Tips

Tip: If you ever change the token, you'll need to redeploy the app to
ensure a new .netrc file is created:

```sh
heroku config:set NPM_AUTH_TOKEN=NEW_TOKEN
git commit --allow-empty -m "update dat npm token"
git push heroku master
```

Tip: Heroku's node buildpack will install `dependencies` from package.json
by default. If your app needs `devDependencies` to be installed too,
set the following in your app environment:

```sh
heroku config:set NPM_CONFIG_PRODUCTION=false
```
