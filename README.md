rack-next
=========

This github project serves as the starting point for discussion and high-level planning of the next ruby web interface. Rack at this moment has no obvious future. The Rack Next project aims to get the ball rolling on a potential replacement, whether it be named Rack 2.0, or something entirely different.

Rack Next main design goal is to provide a common framework and specification for modern web technologies, such as WebSockets and HTTP/2. Rack Next should be as lightweight, simple and unassumining as it possibly can whilst achieving it's design objectives.

Here are sources of inspiration and guidance that will no doubt initially influence the direction of Rack Next:

* https://gist.github.com/raggi/11c3491561802e573a47
* https://www.ruby-forum.com/topic/5437247


Implementation
--------------
Rack has a number of flaws and limitations which should be addressed by any potential replacement; some are issues with the specification, and some are issues with the Rack framework. I imagine the specification for Rack Next to be more low-level than Rack. The spec may simply define that one IO object needs to be provided as the input, and a single IO object for the raw output. All parsing of HTTP and WebSockets is handled either automatically by the Rack Next framework, or by the application itself, which may choose to use Rack Next as a library rather than a framework.

#### Middleware Ordering
Rack uses stack-based ordering of middleware, meaning the load order of middleware determines the order in which middleware is run when requests are processed. The flaw of this design should be obvious. Any middleware that depends on any other middleware will either break or simply not take effect if it's not loaded in the correct order. This also posses limitations for runtime and lazy loading of middleware which a lot of application frameworks use.

A means to address this would be to implement dependancy decleraations, much like you would in a Gemfile. In addition, arbitrary conditions could also be defined (e.g. don't load me unless condition x is true) which can intelligently fall through and fail if it cannot be satisfied by other loaded middleware. A loading queue can be used to keep middleware who's conditions cannot be satisfied which can be rechecked whenever there's a chance the condition could now be satisfied, such as if new middleware is loaded. See https://github.com/Wardrop/RequirePattern for a simplified version of such an algorithm.

### Input and Output
An IO object should be provided for both input and output, with full streaming capablities. This could even leverage the STDIN/STDOUT infrastructure built into Ruby, which would provide the easiest means of interoperation with other server and middleware vendors.

Applications that wish not to stream can wait for EOF, where as other applications may wish to continually poll the input stream stream, or push to the output stream as quickly as possible. This model should be protocol agnostic. The same input/output model should work for both HTTP and WebSockets. These same input and output objects can be wrapped by higher-level request/response classes, which themselves may live in a single context object, serving the same role as the `env` hash in Rack.

### Parsing and Encoding
Rack should take care of a lot more of the boring HTTP-level stuff, and provide succinct API's. By leaving out comprehensive header parsing capabilities for example, you get a lot of people rolling their own adhoc solutions using regex or other means of parsing which don't respect the rules defined in the HTTP spec. Media types is an example of this, in which there is currently no solution out there that correctly parses and interprets Content-Type headers in requests.

### Production Ready
A replacement for Rack should ideally provide a production-ready means of running web applications with reasonable performance that provides HTTP/2 and WebSockets support out-of-the-box. The performnce and feature requirements of most applications is small, so it would serve as a great convenince to many. This would also greatly assist getting Rack Next off the ground without depending on other server and middleware vendors to join the party.

### Documentation
Formal specifications and API documentation is irreplacaeble, but they should be used for reference, not as the sole documentation for Rack Next. It could be said that Rack failed in this respect, and many users became overly dependant on Frameworks because Rack itself did not provide enough guidance on using it's native API's.
