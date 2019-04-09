# Logging

The stack factory methods all accept a `log4cpp::Logger` instance.  Log4cpp is a simple logging library
and doesn't provide any really useful backends. More than likely, you'll be writing some glue code to 
bridge the log callbacks into your applications logging framework.

## log4cpp::ILogHandler

The [log4cpp::ILogHandler]({{base_doxygen_path}}/classlog4cpp_1_1ILogHandler.html) interface defines the `log(..)` method that a log handler must implement.

A [log4cpp::Logger]({{base_doxygen_path}}/classlog4cpp_1_1Logger.html) is just a class references a shared `ILogHandler` and stores some metadata such as the
logger's name and the current level of the logger.

Log4cpp provides a simple `ILogHandler` that writes to the console called [ConsolePrettyPrinter]({{base_doxygen_path}}/classlog4cpp_1_1ConsolePrettyPrinter.html).
You initialize the logging for the library with the following:

``` cpp
// required includes
#include <log4cpp/ConsolePrettyPrinter.h>
#include <ssp21/stack/LogLevels.h>

int main(int argc, char* argv[])
{
    const auto handler = make_shared<log4cpp::ConsolePrettyPrinter>();

	// create a logger
	log4cpp:Logger logger(
	    make_shared<log4cpp::ConsolePrettyPrinter>(),
		Module::id,
		"logger-id",
		log4cpp::LogLevels::everything()
	);

	// do things with your logger
}
```

When writing a program with multiple concurrent `IStack` instances, you should use the a separate `Logger` for each session. You can do this by calling
one of the `detach(..)` methods provided on the `Logger` class which will create a new logger pointing to the same handler, but with a possibly distinct
name and log levels.

It is also possible to adjust the log level at runtime for a particular logger using the `set_levels` method. You can see all of the log levels defined
in the SSP21 library [here]({{base_doxygen_path}}/namespacessp21_1_1levels.html).

In addition to the standard log levels, the library provides four special levels for logging raw and decoded protocol messages for diagnostic purposes.
