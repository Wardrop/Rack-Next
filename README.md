rack-next
=========

This github project serves as the starting point for discussion and high-level planning of the next ruby web interface. Rack at this moment has no obvious future. The Rack Next project aims to get the ball rolling on a potential replacement, whether it be named Rack 2.0, or something entirely different.

Rack Next main design goal is to provide a common framework and specification for modern web technologies, such as WebSockets and HTTP/2. Rack Next should be as lightweight, simple and unassumining as it possibly can whilst achieving it's design objectives.

Here are sources of inspiration and guidance that will no doubt initially influence the direction of Rack Next:

* https://gist.github.com/raggi/11c3491561802e573a47
* https://www.ruby-forum.com/topic/5437247


Rack Shortcomings
-----------------
Rack has a number of flaws and limitations which should be addressed by any potential replacement; some are issues with the specification, and some are issues with the Rack framework.

#### Middleware Ordering
Rack uses stack-based ordering of middleware, meaning the load order of middleware determines the order in which it's processed. The flaw of this design should be simple. Any middleware that depends on any other middleware will either break or simply not take effect if it's not loaded in the correct order.

A means to address this would be to implement dependancy decrelations, much like you would in a Gemfile. In addition, arbitrary conditions could also be defined (e.g. don't load me unless condition x is true) which can intelligently fall through and fail if it cannot be satisfied by other loaded middleware. A loading queue can be used to keep middleware who's conditions cannot be satisfied which can be rechecked whenever there's a chance the condition could now be satisfied, such as if new middleware is loaded. See https://github.com/Wardrop/RequirePattern for a simplified version of such an algorithm.
