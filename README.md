Fork of WDIO JSON Reporter with dependencies updated
===================

> A WebdriverIO plugin. Report results in json format. With updates to WDIO@v7

## WDIO Version Compatibility

This package is only made for wdio@v7, please see https://github.com/fijijavis/wdio-json-reporter/ for compatibility with other wdio versions

*Note:* the changes from this fork are also made or going to be made back to https://github.com/fijijavis/wdio-json-reporter/, I recommend to use https://github.com/fijijavis/wdio-json-reporter/ first

| WDIO Json Reporter | WDIO |
| ------------------ | ---- |
| ^3.0.0             | v7   |

## Installation

* NPM
```bash
npm install @wyh.michael/wdio-json-reporter --save-dev
```

* Yarn
```bash
yarn add @wyh.michael/wdio-json-reporter --dev
```

## Configuration

### Results to STDOUT
```js
reporters: [
  'dot',
  ['@wyh.michael/wdio-json-reporter',{ stdout: true }]
],
```

### Results to File
```js
reporters: [
  'dot',
  ['@wyh.michael/wdio-json-reporter',{
      outputDir: './Results'
  }]
],
```

### Results to File with custom file name
```js
reporters: [
  'dot',
  ['@wyh.michael/wdio-json-reporter',{
    outputDir: './Results',
    outputFileFormat: function(opts) {
        return `results-${opts.cid}.${opts.capabilities}.json`
    }
  }]
],
```

## Result Files
Starting with WDIO v5, reporting has moved from a centralized process to one that is handled by each of the "sessions" spun up for parallel test execution.
This change helped reduce the amount of chatter during WDIO test execution and thus improved performance.  The downside is we are no longer able
to get a single report for ALL test execution.  Consider the following:

2 suites of tests configured to run in 2 browsers:

* WDIO v4: 1 json file with execution results
* WDIO v5: 4 json files with execution results


`wdio-json-reporter` provides a utility function to merge the multiple json files into a single file.  Follow the steps below to take advantage of the utility.

1) Create a small node script
```javascript
const mergeResults = require('wdio-json-reporter/mergeResults')
mergeResults('./Results', 'wdio-json-*', 'wdio-custom-filename.json')
```

*Note:* `wdio-custom-filename.json` is optional, if the parameter is not provided the default value is `wdio-merged.json`

2) Call node script from command line and pass 2 arguments

* <RESULTS_DIR>: Directory where results files are written
* <FILE_REGEX>: Regex pattern for finding `wdio-json-reporter` result files in <RESULTS_DIR>.  This is necessary because multiple reporters produce `json` result files

Example:
```bash
node mergeResults.json ./Results "wdio-json-*"
```

3) As part of a wdio hook

```js
// Located in your wdio.conf.js file
onComplete: function (exitCode, config, capabilities, results) {
  const mergeResults = require('wdio-json-reporter/mergeResults')
  mergeResults('./Results', 'results-*', 'wdio-custom-filename.json')
}
```

Upon completion, the merge script will output a single json file named `wdio-merged.json` in the provided <RESULTS_DIR>


## Configuration

### Standard
Following code shows the default wdio test runner configuration. Just add `'json'` as reporter
to the array. To get some output during the test you can run the [WDIO Dot Reporter](https://github.com/webdriverio/wdio-dot-reporter) and the WDIO JSON Reporter at the same time:

```js
// wdio.conf.js
module.exports = {
  // ...
  reporters: ['dot', '@wyh.michael/wdio-json-reporter'],
  reporterOptions: {
    outputDir: './Results'
  },
  // ...
};
```

### Single Results File

```js
// wdio.conf.js
module.exports = {
  // ...
  reporters: ['dot', '@wyh.michael/wdio-json-reporter'],
  reporterOptions: {
    outputDir: './',
    combined: true
  },
  // ...
};
```

### Custom File Name

```js
// wdio.conf.js
module.exports = {
  // ...
  reporters: ['dot', '@wyh.michael/wdio-json-reporter'],
  reporterOptions: {
    outputDir: './',
    filename: 'wdio-results'
  },
  // ...
};
```

### STDOUT

```js
// wdio.conf.js
module.exports = {
  // ...
  reporters: ['@wyh.michael/wdio-json-reporter'],
  reporterOptions: {
    useStdout: true
  },
  // ...
};
```

If you do not want to print out the mocha epilogue (i.e. `1 passing (5.2s)`), you can suppress it:

```js
// wdio.conf.js
module.exports = {
  // ...
  reporters: ['@wyh.michael/wdio-json-reporter'],
  reporterOptions: {
    suppressEpilogue: true
  },
  // ...
};
```

For more information on WebdriverIO see the [homepage](http://webdriver.io).
