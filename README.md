# Logger

This is a small, self-contained C++ logger with minimal overhead.

## Description

It allows logging messages with different severities, and allows the user of the services to choose which severity they want displayed.
This is a niche alternative to [Google (`glog`)](), [`spdlog`](), [`plog`]() and [`Boost::Log`]().
It is also compatible with [ETL]()'s `etl::string`. Assuming `etl::string<SIZE> text = "Hello"`, you can use `text.data()` or `text.c_str()`.
It is lite enough to be used in a microcontroller.
It is used for all logging purposes in the [AcubeSAT nanosatellite project](https://acubesat.spacedot.gr/) (and aboard it, with some modifications).

## Table of Contents

<details>
<summary>Click to expand</summary>

- [Logger](#logger)
	- [Description](#description)
	- [Table of Contents](#table-of-contents)
	- [Usage](#usage)
	- [Features](#features)
	- [Log Levels](#log-levels)
		- [Caveats](#caveats)

</details>

## Usage

The logger uses `#define`s that allow logging through `operator<<`:
```cpp
LOG_ERROR << "Fingolfin stumbled backwards into a pit!";
LOG_INFO << "Amon Sûl was struck by " << 42 << " thunderbolts today";
LOG_DEBUG << "Slayed Uruk-Hai " << urukHaiName << " with " << getWeapon(adventurerName);
```

Don't forget to include the line `add_compile_definitions<LOG_LEVEL>` in your `CmakeLists.txt`, for example `add_compile_definitions(LOGLEVEL_TRACE)`.

## Features

- Easy-to-customize log levels
- Determine if the level is sufficient for an expression to be logged at compile time
- Empty log entries/log entries that will not be displayed are not processed or stored
- Logs composed with `operator<<`
- Force `inline`, to force `const` propagation; works like a macro in `-O1`
- Dummy log entry to nuke away vtables, etc.
- ETL-agnostic
- Minimal assembly produced

## Log Levels

The following log levels are supported:
| Level | Description |
| ----- | ----------- |
| trace | Very detailed information, useful for tracking the individual steps of an operation |
| debug | General debugging information |
| info | Noteworthy or periodical events |
| notice | Uncommon but expected events |
| warning | Unexpected events that do not compromise the operability of a function |
| error | Unexpected failure of an operation |
| emergency | Unexpected failure that renders the entire system unusable |
| disabled | Use this log level to disable logging entirely. No message should be logged as disabled. Must be enforced by the user |

Changing the log levels and their order is very simple.
In [`Logger.hpp`](), change the `#define`s for the levels, e.g.:
```cpp
#if defined LOGLEVEL_TRACE
#define LOGLEVEL Logger::trace
#elif defined LOGLEVEL_DEBUG
#define LOGLEVEL Logger::debug
```

```cpp
#define LOG_TRACE     (LOG<Logger::trace>())
#define LOG_DEBUG     (LOG<Logger::debug>())
```

and the `enum` for the order:
```cpp
enum LogLevel : LogLevelType {
	trace = 32,
	debug = 64,
	info = 96,
	notice = 128,
	warning = 160,
	error = 192,
	emergency = 254,
	disabled = 255,
};
```

### Caveats

For messages that will not be logged, any calls to functions that contain side effects will still take place:
```cpp
LOG_DEBUG << "The temperature is: " << getTemperature();
```

Here, if `getTemperature()` will cause a side effect (e.g. a `std::cout` print), it will still be executed, even if the debug message will not be printed to the screen due to an insufficient `LOGLEVEL`. You should prefer to use functions that return plain values as parts of the log function, so that they might be optimzied away at compile time.

For type casts, [`etl::to_string`](https://www.etlcpp.com/to_string.html) and co are your friends. Or, you can just implement the corresponding operators for other types.
