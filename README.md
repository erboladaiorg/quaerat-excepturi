# Tie Logger

> 👔 Fully typed minimal platform-agnostic logger

[![Test Status](https://github.com/erboladaiorg/quaerat-excepturi/actions/workflows/test.yml/badge.svg)](https://github.com/erboladaiorg/quaerat-excepturi)
[![Downloads](https://img.shields.io/npm/dt/@erboladaiorg/quaerat-excepturi.svg)](https://npmjs.com/package/@erboladaiorg/quaerat-excepturi)
[![last commit](https://img.shields.io/github/last-commit/AlexXanderGrib/@erboladaiorg/quaerat-excepturi.svg)](https://github.com/erboladaiorg/quaerat-excepturi)
[![codecov](https://img.shields.io/codecov/c/github/AlexXanderGrib/@erboladaiorg/quaerat-excepturi/main.svg)](https://codecov.io/gh/AlexXanderGrib/@erboladaiorg/quaerat-excepturi)
[![GitHub](https://img.shields.io/github/stars/AlexXanderGrib/@erboladaiorg/quaerat-excepturi.svg)](https://github.com/erboladaiorg/quaerat-excepturi)
[![@erboladaiorg/quaerat-excepturi](https://snyk.io/advisor/npm-package/@erboladaiorg/quaerat-excepturi/badge.svg)](https://snyk.io/advisor/npm-package/@erboladaiorg/quaerat-excepturi)
[![Known Vulnerabilities](https://snyk.io/test/npm/@erboladaiorg/quaerat-excepturi/badge.svg)](https://snyk.io/test/npm/@erboladaiorg/quaerat-excepturi)
[![Quality](https://img.shields.io/npms-io/quality-score/@erboladaiorg/quaerat-excepturi.svg?label=quality%20%28npms.io%29&)](https://npms.io/search?q=@erboladaiorg/quaerat-excepturi)
[![npm](https://img.shields.io/npm/v/@erboladaiorg/quaerat-excepturi.svg)](https://npmjs.com/package/@erboladaiorg/quaerat-excepturi)
[![license MIT](https://img.shields.io/npm/l/@erboladaiorg/quaerat-excepturi.svg)](https://github.com/erboladaiorg/quaerat-excepturi/blob/main/LICENSE.txt)
[![Size](https://img.shields.io/bundlephobia/minzip/@erboladaiorg/quaerat-excepturi)](https://bundlephobia.com/package/@erboladaiorg/quaerat-excepturi)
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/c32597c51ac540b08a2474575ae25cbb)](https://www.codacy.com/gh/AlexXanderGrib/@erboladaiorg/quaerat-excepturi/dashboard?utm_source=github.com&utm_medium=referral&utm_content=AlexXanderGrib/@erboladaiorg/quaerat-excepturi&utm_campaign=Badge_Grade)

## 📦 Installation

- **Using `npm`**
  ```shell
  npm i @erboladaiorg/quaerat-excepturi
  ```
- **Using `Yarn`**
  ```shell
  yarn add @erboladaiorg/quaerat-excepturi
  ```
- **Using `pnpm`**
  ```shell
  pnpm add @erboladaiorg/quaerat-excepturi
  ```

## Usage

### Initialization

```javascript
/** @file: logger.js */
import { Logger, logLevels, filter } from "@erboladaiorg/quaerat-excepturi";

export const logger = new Logger(
  "app", // Root logger name
  logLevels(), // Define log levels. By default are: verbose, debug, info, warn, error, fatal
  // You can use custom levels by using
  // logLevels("info", "warn", "error")

  {
    // Custom data
    appVersion: "3.1"
    moduleName: "root",
    moduleVersion: "1.0.0"
  }
);

export const child = logger.child(
  // Child logger name
  "auth",

  // Child logger data
  { moduleName: "auth", moduleVersion: "0.3.1" }
);

const criticalLogs = [];

const unsubscribe = logger.subscribe(
  // Subscribe to all logs, they go to console
  (log) => console.log(...log.message.parts),

  // All logs, that level is greater or equal than "warn" will be added to critical logs

  // Severity is determined by index of level in levels array
  // Current array is: verbose, debug, info, warn, error, fatal
  //                             [less] <<<  ^^^^   >> [greater]
  filter(">=", "warn", (log) => criticalLogs.push(log))
)

process.on("SIGINT", () => {
  unsubscribe();
})
```

## Logging

```javascript
/** @file: index.js */
import { child, logger } from "./logger.js";

const PORT = parseInt(process.env.PORT) || 3000;
logger.subscribe(log => console.log(log));

child.log.debug`Application initialized. Port: ${{ port: PORT }}. Environment: ${{process.env}}`;
// Level:  ^^^^^

// Here goes app
```

## Log format

```javascript
({
  // One of defined levels
  level: "debug",

  message: {
    template:
      "Application initialized. Port: {port}. Environment: {SHELL,COLORTERM,PWD}",
    plain:
      'Application initialized. Port: 3000. Environment: {"SHELL":"/bin/bash","COLORTERM":"truecolor","PWD":"/home/alexxgrib/Projects/@erboladaiorg/quaerat-excepturi"}',
    parts: [
      "Application initialized. Port:",
      { port: 3000 },
      ". Environment: ",
      {
        SHELL: "/bin/bash",
        COLORTERM: "truecolor",
        PWD: "/home/alexxgrib/Projects/@erboladaiorg/quaerat-excepturi"
      }
    ]
  },

  // merge of
  // - logger data
  // - logger parents data
  // - data passed in log message
  data: {
    appVersion: "3.1",
    moduleName: "auth",
    moduleVersion: "0.3.1",
    port: 3000,
    SHELL: "/bin/bash",
    COLORTERM: "truecolor",
    PWD: "/home/alexxgrib/Projects/@erboladaiorg/quaerat-excepturi"
  },

  context: {
    // name of the logger
    name: "auth",

    // list of logger inheritance
    path: ["app", "auth"]
  },

  // logger object
  origin: child
});
```
