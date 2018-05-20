# ionic-logging-service

**The dependencies used by the latest version are the same as needed for [Ionic 3.9.0](https://github.com/ionic-team/ionic/blob/master/CHANGELOG.md). For older versions use:**

| ionic-logging-service | Ionic | Angular
| ----- | -------- | ------
| 4.0.0 | >= 3.9.0 | ^5.0.0
| 3.1.0 | >= 3.0.0 | ^4.0.0
| 2.0.0 | >= 2.2.0 | ^2.4.8
| 1.2.1 | >= 2.0.0 | ^2.2.1

- **Ionic 2.0.0: version 1.2.1.**
- **Ionic 2.2.0: version 2.0.0.**

[![Build](https://travis-ci.org/Ritzlgrmft/ionic-logging-service.svg?branch=master)](https://travis-ci.org/Ritzlgrmft/ionic-logging-service)
[![Codecov](https://codecov.io/gh/Ritzlgrmft/ionic-logging-service/branch/master/graph/badge.svg)](https://codecov.io/gh/Ritzlgrmft/ionic-logging-service)
[![Version](https://badge.fury.io/js/ionic-logging-service.svg)](https://www.npmjs.com/package/ionic-logging-service)
[![Downloads](https://img.shields.io/npm/dt/ionic-logging-service.svg)](https://www.npmjs.com/package/ionic-logging-service)
[![Dependencies](https://david-dm.org/ritzlgrmft/ionic-logging-service/master/status.svg)](https://david-dm.org/ritzlgrmft/ionic-logging-service/master)
[![Peer-Dependencies](https://david-dm.org/ritzlgrmft/ionic-logging-service/master/peer-status.svg)](https://david-dm.org/ritzlgrmft/ionic-logging-service/master?type=peer)
[![Dev-Dependencies](https://david-dm.org/ritzlgrmft/ionic-logging-service/master/dev-status.svg)](https://david-dm.org/ritzlgrmft/ionic-logging-service/master?type=dev)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
[![Known Vulnerabilities](https://snyk.io/test/github/ritzlgrmft/ionic-logging-service/badge.svg)](https://snyk.io/test/github/ritzlgrmft/ionic-logging-service)
[![License](https://img.shields.io/npm/l/ionic-logging-service.svg)](https://www.npmjs.com/package/ionic-logging-service)

This service encapsulates [log4javascript](http://log4javascript.org/)'s functionalities for apps built with [Ionic framework](http://ionicframework.com).

For a sample, just have a look at [ionic-logging-sample](https://github.com/Ritzlgrmft/ionic-logging-sample).

## Usage

```TypeScript
import { Logger, LoggingService } from "ionic-logging-service";

export class MyComponent {

  private logger: Logger;

  constructor(
    loggingService: LoggingService) {

    this.logger = loggingService.getLogger("MyApp.MyComponent");
    const methodName = "ctor";
    this.logger.entry(methodName);

    ...

    this.logger.exit(methodName);
  }

  public myMethod(index: number, message: string): number[] {
    const methodName = "myMethod";
    this.logger.entry(methodName, index, number);

    try {
      ...
    } catch (e) {
      this.logger.error(methodName, "some error", e);
    }

    this.logger.exit(methodName);
    return result;
  }
}

```

Depending how the code is called, this could produce the following output in the browser's console:

```text
I  18:49:43.794  MyApp.MyComponent  ctor  entry
I  18:49:43.797  MyApp.MyComponent  ctor  exit
I  18:49:43.801  MyApp.MyComponent  myMethod  entry  42  Hello
E  18:49:43.814  MyApp.MyComponent  myMethod  some error
I  18:49:43.801  MyApp.MyComponent  myMethod  exit  [2, 5, 99]
```

## Logger

A logger is the component responsible for logging. Typically, you have one logger per every class. The logger name describe the place where in your app the class is placed. The single parts are separated by dots ('.'). This is quite the same as with namespaces in dotnet or packages in Java.

This builds some kind of hierarchy. E.g., if you have a logger named `A.B.C.D`, you get automatically also loggers for `A.B.C`, `A.B` and `A`. Additionally, there is the so-called root logger, which is the parent of all other loggers.

The hierarchy is important, since the loggers inherit the log level from there parent - if there is no other level defined. That means, you can define just one log level for the complete app (by setting the root logger's level), and you can par example define, you do not want to see logs written for logger `A.B.C` (this includes also `A.B.C.D`).

## Level

Every log message has a level. This is the severity of the message. Available levels are `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR` and `FATAL` - these correspond to the logging methods `trace`, `debug`, `info`, `warn`, `error` and `fatal` of `Logger`. Levels are ordered as follows: `TRACE` < `DEBUG` < `INFO` < `WARN` < `ERROR` < `FATAL`. This means the `FATAL` is the most severe and `TRACE` the least. Also included are levels called `ALL` and `OFF` intended to enable or disable all logging respectively.

Setting a level to a logger disables log messages of severity lower than that level. For instance, if a level of `INFO` is set on a logger then only log messages of severity `INFO` or greater will be logged, meaning `DEBUG` and `TRACE` messages will not be logged.

## Appender

Appenders make the logs visible, e.g. by writing tehm to the browser's console. This is quite useful during development, either in console or using `ionic serve --consolelogs`. But later, you will need other logs:

- `AjaxAppender`: sends the log messages to a backend server
- `MemoryAppender`: keeps the log messages in memory
- `LocalStorageAppender`: stores the log messages in local storage

If you want to see a complete example, have a look at [ionic-feedback-sample](https://github.com/Ritzlgrmft/ionic-feedback-sample).

## Configuration

The configuration is done with the [ionic-configuration-service](https://github.com/Ritzlgrmft/ionic-configuration-service).

The specific configuration for the `ionic-logging-service` is taken from the key `logging`.
Its structure is defined in the interface [LoggingConfiguration](src/logging-configuration.model.ts).

By default, the following configuration is used:

- Logger:
  - root: `Level.WARN`

- Appender:
  - `BrowserConsoleAppender`
  - `MemoryAppender`

### logLevels

`logLevels` gets an array of log level definitions for different loggers, e.g.

```JavaScript
{
  "logLevels": [
    {
      "loggerName": "root",
      "logLevel": "DEBUG"
    },
    {
      "loggerName": "MyApp.MyNamespace.MyLogger",
      "logLevel": "INFO"
    }
  ]
};
```

That means, instead of the default log level `WARN`, you want to log all messages with level `DEBUG` and higher. Only for `MyApp.MyNamespace.MyLogger`, you want to restrict the level to `INFO`.

### ajaxAppender

With [ajaxAppender](typedoc/interfaces/ajaxappenderconfiguration.html), you add an additional appender of type [AjaxAppender](typedoc/classes/ajaxappender.html), which sends the log messages to a backend server.

### browserConsoleAppender

With [browserConsoleAppender](typedoc/interfaces/browserconsoleappenderconfiguration.html), it is possible to configure the `BrowserConsoleAppender`, which writes the log to the browser's console.

### localStorageAppender

With [localStorageAppender](typedoc/interfaces/localstorageappenderconfiguration.html), you add an additional appender of type [AjaxAppender](typedoc/classes/localstorageappender.html), which stores log messages in the local storage.

### memoryAppender

With [memoryAppender](typedoc/interfaces/memoryappenderconfiguration.html), it is possible to configure the [MemoryAppender](typedoc/classes/memoryappender.html), which keeps log messages in the memory.

## API

see [API documentation](typedoc/index.html).