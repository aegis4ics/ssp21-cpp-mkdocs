# IExecutor

[IExecutor]({{base_doxygen_path}}/{{version}}/classexe4cpp_1_1IExecutor.html) is an abstraction around some concrete event loop. The library code
uses `IExecutor` to "post" callbacks and to start timers.

An application using SSP21-CPP needs to provide a concrete implementation of this abstract class. The most common event-based networking library in C++
is [ASIO](https://think-async.com/Asio/). As such, the `exe4cpp` dependency provides two concrete implementations of `IExecutor` based on ASIO:

* [BasicExecutor]({{base_doxygen_path}}/classexe4cpp_1_1BasicExecutor.html) is a single-threaded implementation.
* [StrandExecutor]({{base_doxygen_path}}/classexe4cpp_1_1StrandExecutor.html) is multi-threaded and meant to be used with a [ThreadPool]({{base_doxygen_path}}/classexe4cpp_1_1ThreadPool.html).
It can be forked to create sibling executors which guarantee that any callback (e.g. a timer) are only called by a single thread at a time.

!!! note
    While SSP21-CPP only provides examples of integration with ASIO, it should be quite possible to adapt other event libraries such as [libuv](http://libuv.org/)
	to the abstractions that SSP21-CPP provides.

## Event-driven

Ultimately, any application using the library will need to run an event loop. The proxy application uses the `BasicExecutor` to run a single-threaded event loop:

``` c++
const auto executor = make_shared<exe4cpp::BasicExecutor>(std::make_shared<asio::io_service>());

// omitted: configure proxy components

SIMPLE_LOG_BLOCK(logger, ssp21::levels::event, "begin io_context::run()");
executor->get_service()->run();
SIMPLE_LOG_BLOCK(logger, ssp21::levels::event, "end io_context::run()");
```

The program exits after the last 3 lines above. The program consists solely of asynchronous operations, and is
capable of running many concurrent communication sessions without blocking.
