# Logger

This is a small, self-contained C++ logger with minimal overhead.

It allows logging messages with different severities, and allows the user of the services to choose which severity they want displayed.
This is a niche alternative to [Google (`glog`)](), [`spdlog`](), [`plog`]() and [`Boost::Log`]().
It is also compatible with [ETL]()'s `etl::string`.
It is lite enough to be used in a microcontroller.

The following log levels are supported:
| Level | Description |
| ----- | ----------- |
| trace | TODO |
| debug | TODO |
| info | TODO |
| notice | TODO |
| warning | TODO |
| error | TODO |
| emergency | TODO |
| disabled | TODO |

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
		trace = 32, ///< Very detailed information, useful for tracking the individual steps of an operation
		debug = 64, ///< General debugging information
		info = 96, ///< Noteworthy or periodical events
		notice = 128, ///< Uncommon but expected events
		warning = 160, ///< Unexpected events that do not compromise the operability of a function
		error = 192, ///< Unexpected failure of an operation
		emergency = 254, ///< Unexpected failure that renders the entire system unusable
		disabled = 255, ///< Use this log level to disable logging entirely. No message should be logged as disabled.
	};
```

The logger uses `#define`s that allow logging through `operator<<`:
```cpp
LOG_ERROR << "Fingolfin stumbled backwards into a pit!";
LOG_INFO << "Amon Sûl was struck by " << 42 << " thunderbolts today";
LOG_DEBUG << "Slayed Uruk-Hai " << urukHaiName << " with " << getWeapon(adventurerName);
```

Don't forget to include the line `add_compile_definitions<LOG_LEVEL>` in your `CmakeLists.txt`, for example `add_compile_definitions(LOGLEVEL_TRACE)`.
