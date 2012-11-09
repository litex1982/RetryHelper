RetryHelper
=======

This is a generic helper to help try some action until given condition is met.

Usage
----
Write retry logic for operations like web request or file operation in a more readable way rather than a try-catch nested in a loop. You can specify end conditions based on return value or exception, config the retry interval, maximum retry count and maximum retry time flexibly, and more.

Examples:
-------

### One-line sample

    RetryHelper.Instance.Try(() => TryDoSomething()).UntilNoException();

### Example 1: Retry until I get a return value < 0.1 from the method *TryGetSomething*.

    // Basic usage
    RetryHelper.Instance.Try(() => TryGetSomething()).Until(result => result < 0.1);

    // Get the result from the retried method
    var resultSmallEnough = RetryHelper.Instance.Try(() => TryGetSomething()).Until(result => result < 0.1);

    // Specify interval
    RetryHelper.Instance.Try(() => TryGetSomething()).WithTryInterval(100).Until(result => result < 0.1);

    // Specify max try count, will throw TimeoutException if exceeded
    RetryHelper.Instance.Try(() => TryGetSomething()).WithMaxTryCount(20).Until(result => result < 0.1);

    // Can also constrain the total try time
    RetryHelper.Instance.Try(() => TryGetSomething()).WithTimeLimit(TimeSpan.FromSeconds(10)).Until(result => result < 0.1);

    // Specify the extra success/fail action
    RetryHelper.Instance.Try(() => TryGetSomething())
        .WithMaxTryCount(20)
        .OnSuccess(result => Trace.TraceInformation(string.Format("Get result {0}.", result)))
        .OnTimeout(lastResult => Trace.TraceError("Did not get result under 0.1 in 10 times."))
        .Until(result => result < 0.1);


### Example 2: Retry method *TryDoSomething* until there's no exception thrown.

    // Retry on any (non-fatal) exception
    RetryHelper.Instance.Try(() => TryDoSomething()).UntilNoException();

    // Retry on specific exception
    RetryHelper.Instance.Try(() => TryDoSomething()).UntilNoException<ApplicationException>();

    // Lambda can be simplified in this case
    RetryHelper.Instance.Try(TryDoSomething).UntilNoException();


### Change the global default settings

    RetryHelper.Instance.DefaultMaxTryCount = 3;
    RetryHelper.Instance.DefaultMaxTryTime = TimeSpan.FromSeconds(10);
    RetryHelper.Instance.DefaultTryInterval = TimeSpan.FromMilliseconds(100);


### Get another RetryHelper instance with custom TraceSource and seperate configration

    var retryHelper = new RetryHelper(new TraceSource("MyTraceSource"))
    {
        DefaultMaxTryCount = 10,
        DefaultMaxTryTime = TimeSpan.FromSeconds(30),
        DefaultTryInterval = TimeSpan.FromMilliseconds(500)
    }

LICENSE
=======
[Apache 2.0 License]
