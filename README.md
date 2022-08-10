Creating a package in the GitHub registry is very simple.

Firstly `package.json` needs to be updated; so that name, repository and publishConfig does not mismatch.

```
  "name": "@<owner>/<repo-name>",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com/@<owner>"
  },
  "repository":"https://github.com/<owner>/<repo-name>.git",

```

Secondly, [create a `GITHUB_TOKEN` ](https://docs.github.com/en/enterprise-server@3.4/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)which would be used in verification.
 
The package can be published by: 
a) GitHub action or 
b) manually in terminal

**a) To publish with GitHub action**
add a workflow in `.github/workflows/<workflow-name>.yml`
```
name: Node.js Package

on:
  push:
    branches: ["<branch-name>"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 12
      - run: npm ci
      - run: npm test

  publish-gpr:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 12
          registry-url: https://npm.pkg.github.com/
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}


```

**b) publish package manually in terminal**

Firstly, create a `.npmrc` file at the root of the directory
```
@<owner>:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=<GITHUB_TOKEN>
```
Secondly, login to npm 
`npm login --scope=@<owner> --registry=https://npm.pkg.github.com`

Finally, publish the package.
`npm publish`




**Useful links:**
1. [quickstart](https://docs.github.com/en/packages/quickstart)
2. [working-with-the-npm-registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry) 
