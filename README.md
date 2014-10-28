Rack Next
=========
This github-hosted project serves as the starting point for high-level discussion and planning of the next ruby web interface, intended to one day make Rack obselete. With Rack at this moment having no obvious future, the Rack Next project aims to get the ball rolling on a replacement that addresses the inadequacies of Rack as both a specification and a framework.

The objective of Rack Next is to provide a common specification and framework for Ruby that supports modern web technologies, such as WebSockets and HTTP/2, with an emphasis on ascynhronous requests and bi-directional communication between client and server. Rack Next should be as lightweight, simple, and unassumining as it possibly can while obviously achieving its design objectives.

Here are sources of potential inspiration and guidance that will no doubt influence the initial direction of the Rack Next project:

* https://gist.github.com/raggi/11c3491561802e573a47
* https://www.ruby-forum.com/topic/5437247

Where do we begin?
------------------
For Rack Next to succeed it needs to be a community effort, and we need to ensure the concerns of everyone are well considered and addressed if possible. Everything is open to discussion, debate, and of course change. The Github issues and wiki features will be used as the main collaberation tools for project discussion and planning. Issues will be used to raise points of discussion, while the wiki will be used to share concepts and document the outcome of questions, issues and ideas. Eventually, the git repository itself will be used to start forming a formal specification.

If traction is gained and we can agree on a final project name, this repository will likely be moved under a generic github account of that name to reflect the fact it's a community project, rather than a personal endavour. If traction is poor or early community involvement becomes more of a hindrance than a help, I will likely push on by myself before attempting once again to gain community involvement when the project is more mature.

#### Milestones
* Establish the project scope and high-level objectives.
* Formally define a specification that accomodates HTTP/2 and WebSockets, whilst leaving it open for other bi-directional, asychronous, and persistant network protocols. Simplicity is the ultimate objective, with interoperability being a close second. By the end of this milestone, we should have a proof of concept application server that takes input in some form, and produces output consistant with the specification at that time.
* Establish the scope of the framework. What features and capabilities will be provided and how will they be designed? Do we accomodate application developers as much as framework developers? How will the API be structured?
* Start implementing the framework.

Implementation
--------------
Rack has a number of flaws and limitations which should be addressed by any potential replacement; some are issues with the specification, and some are issues with the Rack framework. I imagine the specifications for Rack Next to be a little bit lower-level than Rack. The spec may simply define that one IO object needs to be provided as the input, and a single IO object for the raw output, i.e. STDIN and STDOUT.

#### Middleware Ordering
Rack uses stack-based ordering of middleware, meaning the load order of middleware determines the order in which middleware is run when requests are processed. The flaw of this design should be obvious. Any middleware that depends on any other middleware will either break or simply not take effect if it's not loaded in the correct order. This also posses limitations for runtime and lazy loading of middleware which a lot of application frameworks use.

A means to address this would be to implement dependancy decleraations, much like you would in a Gemfile. In addition, arbitrary conditions could also be defined (e.g. don't load me unless condition x is true) which can intelligently fall through and fail if it cannot be satisfied by other loaded middleware. A loading queue can be used to keep middleware who's conditions cannot be satisfied which can be rechecked whenever there's a chance the condition could now be satisfied, such as if new middleware is loaded. See https://github.com/Wardrop/RequirePattern for a simplified version of such an algorithm.

### Input and Output
An IO object should be provided for both input and output, with full streaming capablities. This could even leverage the STDIN/STDOUT infrastructure built into Ruby, which would provide the easiest means of interoperation with other server and middleware vendors.

Applications that wish not to stream can wait for EOF, where as other applications may wish to continually poll the input stream, or push to the output stream as quickly as possible. This model should be protocol agnostic. The same input/output model should work for both HTTP and WebSockets. These input and output objects could be wrapped by a higher-level context object, serving the same role as the `env` hash in Rack.

### Parsing and Encoding
Rack should take care of a lot more of the boring HTTP level stuff, and provide succinct API's that are as easy and pleasurable to use as possible, without getting in the way of higher-level frameworks. By Rack leaving out comprehensive header parsing capabilities for example, you ended up getting a lot of people rolling their own adhoc solutions using regex or other means of parsing which don't respect the rules defined in the HTTP spec; a spec which not many people have the time or motivation to read. Media types is an example of this, in which there is currently no solution out there that correctly parses and interprets Content-Type headers in requests (at least until I finish my `rack-acceptable` gem).

### Production Ready
A replacement for Rack should ideally provide a production-ready means of running web applications with reasonable performance that provides HTTP/2 and WebSockets support out-of-the-box; this is a production-ready equivilant to todays `rackup`. The performance and feature requirements of most applications are simple, so it would serve as a great convenince. This would also greatly assist getting Rack Next off the ground without depending on other server and middleware vendors to join the party straight away.

### Documentation
Formal specifications and API documentation is irreplacaeble, but they should be used for reference, not as the sole documentation for Rack Next. It could be said that Rack failed in this respect, and many users became overly dependant on Frameworks because Rack itself did not provide enough guidance on using it's native API's.
