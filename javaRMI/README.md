# Java RMI

Here you can find examples on how to perform [Java Remote Method Invocation](https://en.wikipedia.org/wiki/Java_remote_method_invocation).

## 1. RemotePrintServer / RemotePrintClient

The interface [RemotePrintInterface](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java) provides a method `print` which receives one `String`. The RMI Server [RemotePrintServer](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java) implements this method in a straightforward fashion: it prints the `String` to `System.out`. An instance of this server is created and registerd as RMI server object under the name `server` in a registry listening for requests on port `9999`. The [RemotePrintClient](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClient.java) wants to make use of this very valuable print service: It first obtains the registry and then, from this registry, the object names `server`. This object must be an instance of [RemotePrintInterface](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java), so we can call its `print` method. When doing so, the string we pass in is sent to the server object. Since the object actually resides in a different Java virtual machine in a different process, the string is marshalled and sent over a socket to that other process, read and unmarshalled from the socket, handed to the server object, and then printed. This would work over the network as well, we can call a method which will be executed on a different computer. And we can do so relatively conveniently without bothering too much about details.

1. [RemotePrintInterface.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java)
1. [RemotePrintServer.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java)
1. [RemotePrintClient.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClient.java)

## 2. RemotePrintServer / RemotePrintClientErroneous

As the name [RemotePrintClientErroneous](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClientErroneous.java) implies, this is a "wrong" version of the above example. The error is located in an incorrect typecast in the [RemotePrintClientErroneous](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClientErroneous.java) program: The program incorrectly assumes that the object returned from the registry can be cast to [RemotePrintServer](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java), which it cannot. It can only be cast to [RemotePrintInterface](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java), as done in [RemotePrintClient](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClient.java).

But why is that? The reason is already mentioned in the text of the first example: The actual instance of [RemotePrintServer](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java) resides in a different virtual machine (process) from the client's process. Its actual implementation code is unknown to the client. The object returned by the registry to the client is just a [Proxy](http://docs.oracle.com/javase/7/docs/api/java/lang/reflect/Proxy.html) instance which "implements" all interfaces of the original object which inherit from [Remote](http://docs.oracle.com/javase/7/docs/api/java/rmi/Remote.html). [RemotePrintInterface](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java) is such an interface. If a method of the proxy object is called, it will send a message with the method name and parameters to the actual object on the server side. The server object will invoke the method and send back the results. Then the proxy's method can return as well. This way, the communication and "remoteness" of the procedure invocation is invisible (transparent) to the user of the client object. And since the proxy object is not the real object, we cannot cast it to [RemotePrintServer](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java) without receiving an exception.

1. [RemotePrintInterface.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintInterface.java)
1. [RemotePrintServer.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintServer.java)
1. [RemotePrintClientErroneous.java](http://github.com/thomasWeise/distributedComputingExamples/tree/master/javaRMI/src/RemotePrintClientErroneous.java)