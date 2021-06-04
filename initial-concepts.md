# Initial Concepts

## Streams

As said on the [reactive programming wiki](https://github.com/pachecoka/spring-webflux-study/blob/master/reactive-programming.md#project-reactor), Spring Webflux makes use of Project Reactor library, which implements the reactive programming model based on the **Reactive Streams** specification, therefore it is important to know what are Streams to understand the whole concept behind Reactive Programming.

> In order for an application to be reactive, the first thing it must be able to do is to produce a stream of data.
>
> *- [Baeldung Team](https://www.baeldung.com/reactor-core)*

Introduced in Java 8, the Stream API is used to process collections of objects. A Java 8 stream is a collection of data which flows from the data source (array or collection) and can then be transformed via operators so that finally consumers can consume it.

A stream of data could be something like thousands of stock updates per second coming into a financial application, or even the many tax quotation requests Tax Service receives, for example.

The reactive stream is a **collection of data that arrives over time**. It is similar to Java 8 streams but with the time component added. In java 8 streams data arrives immediately but in reactive streams data arrives over time.

Without this data, there would be nothing to react to, which is why it is logical that this should be part of the specification.

This stream of data is produced by publishers and consumed by subscribers. In this sense the Reactive Streams specification defines four Stream types:

1. Publisher\<T>
2. Subscriber\<T>
3. Subscription\<T>
4. Processor\<T,R>

### Publisher\<T>

A [Publisher\<T>](http://www.reactive-streams.org/reactive-streams-1.0.0-javadoc/org/reactivestreams/Publisher.html) is the producer of values that *may eventually arrive over time*. A Publisher\<T> produces values of type T to a Subscriber\<T>.

### Subscriber\<T>
  
A [Subscriber\<T>](http://www.reactive-streams.org/reactive-streams-1.0.0-javadoc/org/reactivestreams/Subscriber.html) subscribes to a Publisher\<T>, receiving notifications of any new values of type T that arrive over time.
To be able to receive these notifications, the subscriber makes use of some methods:

- onNext(T) - receives notifications of any new values that arrive.
- onError(Throwable) - called if there are any errors. 
- onComplete() - called when processing has completed normally.

### Subscription\<T>

When a Subscriber first connects to a Publisher, it is given a [Subscription\<T>](http://www.reactive-streams.org/reactive-streams-1.0.0-javadoc/org/reactivestreams/Subscription.html). The Subscription is a very important part of the specification as it enables **backpressure**. The Subscriber uses its specificatin methods to either request more data or to stop processing.

### Processor\<T,R>

The Reactive Streams specification provides also the [Processor\<T,R>](http://www.reactive-streams.org/reactive-streams-1.0.0-javadoc/org/reactivestreams/Processor.html) type, which is an interface that extends both Subscriber\<T> and a Publisher\<R>.
It represents a processing stage, which obeys the contracts of both a Subscriber and a Publisher.

## Mono & Flux

The Reactive Streams types are not enough. There needs to be a higher order implementation to support operations like filtering and transformation. So, with Project Reactor, we have Mono and Flux, which are implementations of Publisher\<T>.

### Mono

Mono produces at maximum one item.

Example - Generating a Mono of an Integer.

`Mono<Integer> integerOne = Mono.just(1);`

> Note that you can use a Mono to represent no-value asynchronous processes that only have the concept of completion (similar to a Runnable). To create one, you can use an empty `Mono<Void>`.
>
> *- Project Reactor reference documentation*

One can say that you can have a mono representing various values, such as something like `Mono<List<Integer>>`. Sure, but this is not the intention here.

### Flux

For more than one element, we should use Flux. It's a stream that can produce 0 to n elements.

Example - Generating a Flux of Integers.

`Flux<Integer> integers = Flux.just(1, 2, 3, 4);`
___
For more informations about creating Mono and Flux instances, I suggest reading [this nice post by Antoine Cheron](https://medium.com/@cheron.antoine/reactor-java-1-how-to-create-mono-and-flux-471c505fa158) which explores many interactions with other data types.

As Mono and Flux are publishers, data contained in these types won’t be executed until you consume it, this lead us to the next topic.

## Manipulating and managing reactive streams

To manipulate and manage data contained in reactive streams, we need to study the main operators of Reactor.

So let's see some examples of what we can do!

### Transforming an Existing Sequence

There are many ways we can transform data streams, let's take a look at some of them.

* Using `.map()` and `.flatMap()`

The `.map()` method is for synchronous, non-blocking, 1-to-1 transformations.

It is synchronous because it is a simple method call which emits a result, and non-blocking because it shouldn't introduce latency (shouldn't block the operator calling it).

`.map()` should be used when you want to transform an object/data in fixed time (synchronously).

The `.flatMap()` method is for asynchronous, non-blocking, 1-to-N transformations.

`.flatMap()` should be used when you require some asynch work to be done within it.

```
Mono<Person> person = Mono.just(Person("name", "age:12"));

person.map { person ->
      EnhancedPerson(person, "id-set", "savedInDb")
  }.flatMap { person ->
      reactiveMongoDb.save(person)
  }
```

In the example, `.flatMap()` is used for saving a person in the database, because it’s an async operation for which time is never deterministic, while for converting the person into an EnhancedPerson object, `.map()` is used because it’s a synchronous operation for which time taken is always going to be deterministic.


* Using `.collect()`

We can use the `.collect()` method to aggregate a Flux into an arbitrary container. There are many operations we can provide to collect a Flux.

```
Flux<Integer> numbers = Flux.just(1, 3, 2, 7, 5);
Mono<Long> sum = numbers.collect(Collectors.summingLong(value -> value));
sum.doOnNext(System.out::println).subscribe();
```

In this example we used `Collectors.summingLong()` which will take each value of the Flux **numbers**, sum them and then return a `Mono<Long>`. The result of this will be  `18`

There are also collect methods that return a specific container, like the `.collectSortedList()` which will return a list, sorting it.

```
Flux<Integer> numbers = Flux.just(1, 3, 2, 7, 5);
numbers.collectSortedList()
.doOnNext(System.out::println)
.subscribe();
```

This example will return the following: `1 2 3 5 7`.

As collect assumes a sequence of values, it can only be used by applying it to a `Flux`.


* Using `.zipWith()`

`zipWith` is used to combine publishers. It can be used by Flux as well as by Mono.

When using `zipWith` with Mono we are actually combining publishers by pairing values.

```
Mono.just(13)
.zipWith(Mono.just("B"), (inputOne, inputTwo) -> inputOne + inputTwo)
.doOnNext(System.out::println)
.subscribe();
```

In the example above, we apply `zipWith` to a Mono with a value of `13`. The `zipWith` function takes two arguments, first the other Mono that will be used to apply the method and second what should be done with those two values.

The output of this example is `13B`. 

Note in the example that the values of the Monos do not necessarily need to be of the same type.

### Peeking into a Sequence

We can peek into a sequence to get notified of some behavior or to execute additional behavior (also referred to as "side-effects").

We can do this by aplying functions to signals, let's see some of them.

* Using `.doOnNext()`

With `doOnNext()` we can peek into sequences when an emission is triggered.
```
Flux<Integer> numbers = Flux.fromIterable(Arrays.asList(1,2,3));
numbers.doOnNext(n -> {
    int i = n+1;
    System.out.println("doOnNext method, i is: " + i);
}).subscribe(n->{
    System.out.println("original value is: " + n);
});
```

What this code will print is this:
```
doOnNext method, i is: 2
original value is: 1
doOnNext method, i is: 3
original value is: 2
doOnNext method, i is: 4
original value is: 3
```

And that is because the subscription goes bottom-up, meaning that the subscriber subscribes to `doOnNext()`, and `doOnNext()` subscribes to the original flux, which then starts emitting events. 

`doOnNext()` will then intercept each event and perform some code.

* Using `.doOnSubscribe()`

This method is used for peeking into a sequence post-subscription. 
```
Flux<String> animals = Flux.fromIterable(Arrays.asList("Dog", "Cat"));
animals.doOnSubscribe(n -> System.out.println("A subscription happened!"))
.subscribe(System.out::println);
```

The output of the code is:
```
On subscribe
Dog
Cat
```

Notice that the print of "On subscribe" happened only once, and that is because a subscription to a sequence occurs only once.

* Using `.doOnError()`

When an error occurs we can set a behavior to occur through `.doOnError()`.

```
Mono<String> me = Mono.error(new Exception("An error occurred, run to the hills!"));
me.doOnError(e -> System.out.println("Error message: " + e.getMessage()))
.subscribe();
```
In the example we create a Mono which contains an exception, so when we subcribe to it, an error will occur and the `.doOnError()` behavior will be triggered, generating the output:

```
Error message: An error occurred, run to the hills!
```

### Filtering a Sequence  

With filtering methods we can filter a sequence to extract the values we want from it.

The most common filtering method is `filter()`.

```
Flux<Integer> evenNumbers = Flux
.range(0, 6)
.filter(x -> x % 2 == 0)
.doOnNext(System.out::println);

evenNumbers.subscribe();

Flux<Integer> oddNumbers = Flux
.range(0, 6)
.filter(x -> x % 2 > 0)
.doOnNext(System.out::println);

oddNumbers.subscribe();
```

The code above will first print the numbers from 0 to 5 (the range function is not inclusive on the second parameter) that are divisible by 2, meaning even numbers, which are 0, 2 and 4.

Then it will print the numbers from 0 to 5 that are not divisible by 2, the odd numbers, which are 1, 3 and 5.

### Handling Errors  

Example - Returning default values
```
Mono<Integer> integerMono = Mono.just(10);

integerMono.map(value -> {
    return value / 0;
})
.onErrorReturn(0)
.doOnNext(System.out::println)
.subscribe();
```
In the code above an exception is going to be thrown when dividing by zero. We can use `onErrorReturn` to return a default value for when an exception occurs. The `doOnNext` operator executes the print line method with the result of the operation, in this case, because of the exception, it will print the value 0.


### Working with Time  

Example - delay execution and timeout
```
public static void main(String[] args) {
    Flux<String> colors = Flux.just("red", "black", "tan");
    Flux<String> results = processColors(colors);

    StepVerifier.create(results).expectNext("processed red", "default", "processed tan")
            .verifyComplete();

}

public static Flux<String> processColors(Flux<String> colors) {
    return colors.concatMap(color ->
            simulateRemoteCall(color)
                    .timeout(Duration.ofMillis(400))
                    .onErrorReturn("default"));

}

public static Mono<String> simulateRemoteCall(String input) {
    int delay = input.length() * 100;
    return Mono.just(input)
            .doOnNext(s -> System.out.println("Received " + s + ", delaying for " + delay))
            .map(i -> "processed " + i)
            .delayElement(Duration.of(delay, ChronoUnit.MILLIS));
}
```
In the above code, the method `simulateRemoteCall` delays the return of the element using the operator `delayElement` based on the number of characteres of the `input`, each character corresponds to a 100ms delay here.
Now, at the method `processColors`, we set a timeout for this remote call simulation with the operator `timeout` for each color we have. If the simulation takes more than 400 ms, a default value is returned, this happens because `onErrorReturn` intercepts when an exception occurs, in this case, the exception `TimeoutException` is being thrown.

I have added a StepVerifier in the main method to execute and assert what is being done here.

### Splitting a Flux  
```
//TODO
```

### Going Back to the Synchronous World  
```
Mono<Integer> integerMono = Mono.just(1);

Integer integer = integerMono.map(value -> {
                    value = doSomethingWithTheValue(value);
                    return value;
                  }).block();
```
The `block` operator is used to block the reactive processing until you get the value. Notice the returned type isn't more an instance of `Mono`.

### More operators

For further handling Monos and Fluxes, the reactor documentation includes [this very useful chapter - "Which operator do I need?"](https://projectreactor.io/docs/core/release/reference/index.html#which-operator) which describes operators to be used for  many situations we face on day-to-day programming. We included some base examples for the situations listed in the documentation, but there are many more operators that may fit best your needs.

## Tests

### Testing Reactive Streams

Project Reactor in its test artifact (reactor-test) provides an interface called StepVerifier to test reactive code. With this interface we can define what's expected from our reactive streams.

Let's take a look into the following example:

```
class ExampleTest {

    Flux<String> getElementsFirstLetter(Flux<String> elements) {
        return elements.map(element -> element.substring(0, 1).toUpperCase(Locale.ROOT));
    }

    @Test
    void test() {
        StepVerifier.create(getElementsFirstLetter(Flux.just("red", "green", "blue")))
            .expectNext("R", "G", "B")
            .expectComplete()
            .verify();
    }
}
```

In this example we are using StepVerifier to test the behavior of `getElementsFirstLetter`.

First, we create a StepVerifier builder with the `create` method. Next, we wrap our target test method. Following, the signal is verified with `expectNext`, here we could use `expectNext` multiple times, each with one element, or pass multiple elements to a single method call. `expectComplete` is then used to assert that the stream is executed. Finally, verify() is used to trigger the test.

StepVerifier suits almost all cases of tests you want to do, it has many operators that facilitate testing. To keep this topic concise, you can check the references of this page, which includes links to blog posts about reactive streams testing and stepverifier documentation. You may also want to consult the tests included in the [example application](https://github.com/eduardoarndt/spring-webflux-example) built for this guide.

### Testing REST APIs

TODO

## References

1. [Get Started with Reactive Programming in Spring](https://developer.okta.com/blog/2018/09/21/reactive-programming-with-spring)
2. [Intro To Reactor Core](https://www.baeldung.com/reactor-core)
3. [Spring Reactive Stream Basic concepts](https://medium.com/@AnkurRatra/spring-reactive-stream-basic-concepts-mono-or-flux-part-1-baed4b432977)
4. [Reactor 3 Reference Guide](https://projectreactor.io/docs)
5. [Testing Reactive Streams Using StepVerifier and TestPublisher](https://www.baeldung.com/reactive-streams-step-verifier-test-publisher)
6. [StepVerifier](https://projectreactor.io/docs/test/release/api/reactor/test/StepVerifier.html)
7. [Flux.map vs Flux.flatMap for a 1-to-1 operation](https://stackoverflow.com/questions/46634666/flux-map-vs-flux-flatmap-for-a-1-to-1-operation)
8. [map vs flatMap in reactor](https://stackoverflow.com/questions/49115135/map-vs-flatmap-in-reactor)
