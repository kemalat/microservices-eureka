# microservices-eureka

This is the demonstration of microservice architecture using Eureka Server from Spring Cloud NetFlix. Eureka as service discovery server enables microservices to locate the different instances of another services without knowing their addresses(hostname, ip or url) for purposes of loadbalancing and failover. 

Apart from Eureka, Ribbon and Feign from Netflix Spring Cloud were also used in our demonstration. The primary task of Netflix Ribbon is to provide client-side load balancing.Ribbon load balancers provide service discovery in dynamic environments like a cloud. Ribbon API can dynamically determine whether the servers are up and running in a live environment and can detect those servers that are down. 

Netflix Feign is a declarative HTTP client which aims at simplifying HTTP API clients. By means of  Feign, the developer needs only to declare and annotate an interface while the actual HTTP requiest implementation will happen at runtime.




