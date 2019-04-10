# Crypto

You'll notice that the factory methods to create an `IStack` instance, don't accept any arguments that specify the cryptographic backend. The cryptographic library
is specified globally, usually as the first thing the application does during initialization. This makes it possible to call the cryptographic functions statelessly
without relying on an object instance.

Cryptographic backends implement the [ICryptoBackend]({{base_doxygen_path}}/classssp21_1_1ICryptoBackend.html) interface. In the future, this interface might also
contain methods that define the support provided by the backend, but for the time being all backends must implement all of the abstract methods.

There is an [implementation available]({{base_doxygen_path}}/classssp21_1_1sodium_1_1CryptoBackend.html) for libsodium. You must initialize any program you write with
some backend or the `IStack` factory methods will fail. Initializing with libsodium is straight forward:

```c++

#include "sodium/CryptoBackend.h"
#include <iostream>

using namespace std;

int main(int argc, char*  argv[])
{
    if (!ssp21::sodium::CryptoBackend::initialize())
    {
        cerr << "Unable to initialze the crypto backend" << endl;
        return -1;
    }

	// rest of program
}

```

To implement your own backend based on another library (e.g. openssl):

1. Implement the `ICryptoBackend` interface using the alternate library, either in a standalone library or in your application.

2. Call [Crypto::initialize(..)]({{base_doxygen_path}}/classssp21_1_1Crypto.html) to inject the backend before calling any of the `IStack` factory methods.

The libsodium initialize routine is simple:

``` c++
bool CryptoBackend::initialize()
{
    // call libsodium's own initialize function
    if (sodium_init() != 0) return false;

	// create a shared_ptr<ICryptoBackend> and pass it to Crypto::Initialize(..)
	return Crypto::initialize(std::make_shared<CryptoBackend>());
}
```
