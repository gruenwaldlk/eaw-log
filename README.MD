# eaw-log

A logging framework for Star Wars: Empire at War and Star Wars: Empire at War: Forces of Corruption

## Installation

Drop the provided scripts directory into your `mod/data` directory. That's it.

Logging will only occur if you are running the debug build of EaW/FoC.

## Usage

The framework splits the logging into the logger (the thing you usually call
within your code to log a message) and the sink (the part that takes care of
the actual output).

You can create a logger instance with a single call:

```lua
my_logger = require("eaw-log/logger")
```

This instance comes with reasonable defaults and a default file sink pre-configured.
Currently the file sink is the only supported logging mechanism and outputs all logged messages to a specific log file (default: `eaw-lua.log`)

## Logging

The logger provides the following logging methods. Let's assume the logger instance is called `my_logger` and was created as described above.

```lua
my_logger:trace("A trace message.")
my_logger:info("An info message.")
my_logger:warn("A warning message.")
my_logger:error("An error message.")
```

Each method can also be called with a formatting string and an arbitrary amount of additional arguments:

```lua
my_logger:trace("A message from %s with a nice floating point number %f.", "Darth Vader", 2.344)
```

Depending on the object you pass, you may have to explicitly call `tostring()` on the argument. This is usually necessary for non-native data types, e.g. `GameObjects`, etc.

```lua
local my_object = {my = 3, complicated = 4, object = "test"}
my_logger:trace("A message from %s with a nice floating point number %f.", "Darth Vader", 2.344, tostring(object))
```

## Configuration

The logger provides several configuration methods that allow chaining:

### Log Level

Log levels range between 0 and 3, default is 2.

|Log Level|Explanation|
|:---:|:---|
| 0 | Only error messages will be logged.|
| 1 | Only error and warning messages will be logged.|
| 2 | Error, warning, and information messages will be logged.|
| 3 | Error, warning, information, and debug trace messages will be logged.|

```lua
my_logger = require("eaw-log/logger"):with_log_level(1)
```

### Logger with Custom Sink

```lua
my_logger = require("eaw-log/logger"):with_sink("file")
```

`with_sink()` either accepts a string that represents the name of a file in the `eaw-log/sinks/` directory, or a preconfigured sink.

### Example

```lua
my_customized_logger =
    require("eaw-log/logger")
    :with_sink(require("eaw-log/sinks/file")
        :with_target_file("my_log_file.txt"))
    :with_log_level(3)
```

This will create a logger (`require("eaw-log/logger")`) that logs all log messages from errors to traces (`:with_log_level(3)`) to the file `"my_log_file.txt"` (`:with_sink(require("eaw-log/sinks/file"):with_target_file("my_log_file.txt"))`)

## Adding Sinks

You can add sinks by adding them to the `eaw-log/sinks/` directory. The only hard requirement to add a new sink is that it provides a function `sink.__log(self, message)` and is a valid table. See the `eaw-log/sinks/file.lua` as an example.
