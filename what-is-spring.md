# What is Spring?

Spring framework is an open source Java platform and was first released in 2003 as a response to the complexity of the early J2EE specifications. Spring is complementary to [Java EE](https://www.oracle.com/java/technologies/java-ee-glance.html) and not a competitor. The Spring programming model does not embrace the Java EE platform specification, but [it integrates with selected individual specifications from the EE umbrella](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/overview.html#overview-history).

The core features of the Spring Framework provide everything that is needed to embrace the Java language in an enterprise environment, with the flexibility to create many kinds of architectures depending on the application’s needs. Spring framework targets to make J2EE development easier to use and promotes good programming practices by enabling a POJO-based programming model.

Basically Spring is a framework for **dependency-injection** which is a pattern that allows building very decoupled systems, with some convenience layers (database access, proxies, aspect-oriented programming, RPC, a web mvc framework) added on top. It helps to build Java applications faster and more conveniently.

But, the term "Spring" can mean different things in different contexts. It can be used to refer to the Spring Framework project itself, but, over time, **other Spring projects were built on top of the Spring Framework**. So, "Spring" can also mean the entire family of projects.

The [Spring Framework](https://spring.io/projects/spring-framework) is divided into modules and applications can choose which modules they want to use. 

There are the modules of the core container, which includes a configuration model and a dependency injection mechanism. 
Besides that, the Spring Framework provides foundational support for different **application architectures**, including messaging, transactional data and persistence, and web. It also includes the Servlet-based Spring MVC web framework and, in parallel, the Spring WebFlux reactive web framework.

Beyond the Spring Framework, there are other projects, which are all built on top of the Spring Framework, such as Spring Boot, Spring Security, Spring Data, Spring Cloud, Spring Batch, among [others](https://spring.io/projects).


# What is Spring WebFlux?
[Spring WebFlux](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux) is the reactive-stack web framework of Spring.
It is fully non-blocking, supports reactive streams back pressure, and uses Netty as inbuilt server to run reactive applications.

Spring WebFlux uses [project reactor](https://projectreactor.io/) as reactive library. Reactor is a Reactive Streams library and, therefore, all of its operators support non-blocking back pressure. It is developed in close collaboration with Spring.

The reactive programming paradigm promotes an asynchronous, non-blocking, event-driven approach to data processing. 
More information about reactive programming can be found on the page [Reactive Programming](https://github.com/pachecoka/spring-webflux-study/blob/master/reactive-programming.md).


## References

1. [Spring introduction](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/overview.html#spring-introduction)
2. [Spring framework overview](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/overview.html)
3. [Spring framework Benefits](https://www.tutorialspoint.com/spring/spring_overview.htm)
4. [What is Spring framework](https://www.marcobehler.com/guides/spring-framework)
5. [SpringWebflux Tutorial](https://howtodoinjava.com/spring-webflux/spring-webflux-tutorial/)
