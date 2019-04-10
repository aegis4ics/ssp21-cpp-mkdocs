# Error Handling

The core library **never** throws exceptions during runtime. While the library goes to great lengths to avoid any kind of dynamic allocation (new/malloc) after initialization, it is still possible
to get a `std::bad_alloc` because the methods for `IExecutor` to post or start timers take `std::function<void ()>` which must malloc when the functors exceed a certain size. In general, however, there's
no productive way to "handle" being out of memory other than shutting down the application anyway. SSP21-CPP will never cause memory usage to grow in a way that such a condition arises.

!!! important
    You only need to handle exceptions when calling the `IStack` factory methods. The only exception type thrown is [ssp21::Exception]({{base_doxygen_path}}/classssp21_1_1Exception.html).

All of the error handling internal to the library at runtime uses error codes, e.g. when:

* a malformed message is detected
* cryptographic signatures don't match
* initiator and responder are configured for different key modes

All of these internal errors are "handled" via by simplying logging the error and performing the appropriate state transition (or not).