# Angular CLI ng-packagr Template

## Features

- Angular CLI enabled
- Live-reload of root app during lib development
- Algular CLI Karma & Jasmine tests: runs unit tests for the bundled library module the demo app
- Library folder accessible to Angular CLI for `ng generate` commands
- Travis CI support with Chromium Browser
- Build output to `dist/app` and `dist/lib` for easy upload to *GitHub Pages* and *npm publish*

## lib folder

The `lib/app` folder contains all of the files for our library

All of these files (module and files) are exported from the `public_api.ts` file. Only non-external imports reachable from this file are included in the library build. All other imports need to be listed as *peerDependencies* in the `lib/package.json` file

### .angular-cli.json

The `apps` array includes a second app called `lib`. This is a copy of `apps[0]` json, and allows us to use the *Angular CLI* when generating components/modules within the 'lib' folder

```
ng generate module new-mod --flat=true --app=lib
```

The above command would generate a `new-mod.module.ts` file in `lib/app`

## Main Application - Development Mode

While developing the library (within the `lib/app` folder), we can reference and import the files/module directly in `app.module.ts`. This means that changes being made to the library will *live reload* the demo app. This demo app can also be built and added to the repo using GitHub Pages 

```bash
npm i -g angular-cli-ghpages

ng build --prod --base-href "https://<user-name>.github.io/<repo-name>/"
ngh --dir "dist/app" --message "Deploy Demo App"
```

## Build the Library

Run either of the following commands with `npm run`

```json
"build:lib": "rimraf dist && ng-packagr -p lib/package.json",
"test:lib": "npm run build:lib && ng test"
```

### Testing

The tests are contained in the `lib/test` folder. The files to be tested are imported from the `dist/lib` folder, so we are testing the bundled library

```typescript
import { LibaryModule } from '../../dist/lib';
```

The test files are located outside of the root `src` folder and required the following changes to the test setup:

`tsconfig.spec.json`
```json
"include": [
    "../lib/**/*.spec.ts",
    "**/*.spec.ts",
    "**/*.d.ts"
  ]
```
`tests.ts`
```javascript 
const context_lib = require.context('../lib', true, /\.spec\.ts$/);
context_lib.keys().map(context_lib);
```

Run the tests with `ng test`, or `npm run test:lib` to do a build and test

## Continuous Integration Testing

A `.travis.yml` config file controls the CI when commits are made. This builds the library, and runs the tests against the bundled library. For E2E tests, uncomment the last line of the `.travis.yml` file

## License

This project is licensed under the terms of the MIT license
