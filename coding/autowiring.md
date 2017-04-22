Two main approaches:
* Using `@Autowired` and `@Component` annotations.
* Using only constructor arguments and having `@Bean` methods in `@Configuration` classes for wiring it all together.

Mixed approaches are also possible.

### Using @Autowired and @Component annotations

#### Pros
* Adding a new component is as simple as adding a new class. No more issues due to forgetting to update a configuration class to instantiate a new bean. This is particularly useful if you have a component which holds references to all components of a certain (super-)type. Then adding a new dependency really is as simple as adding a new class (as long as it's properly annotated, that is). This is exactly what the Open-Closed Principle is all about.
* With `@Autowired` fields, there is no need to add a constructor with arguments and/or setters to initialize all fields. This can help keep classes smaller and reduce some of that boilerplate that Java is notorious for.

#### Cons
* The main drawback with this approach is that these annotations end up in most classes implementing your business logic. It feels like the framework is taking over the project. In more concrete terms, the problem here is that if you ever want to switch from one DI framework to another, you end up having to touch most of the classes in your project. It's always good to keep frameworks near the periphery of your projects and not become dependent on them. That said, I have never worked on a project that used Spring for dependency injection and where there was a sudden need to change to a different framework for any reason. So this point seems a bit theoretical in this particular case. It's up to you to decide if it is a valid concern for your project, but it is important to keep in mind in general.
* Unit tests will require frameworks with similarly clever functionality to mock the fields if there are no setters or constructor arguments (e.g. [Mockito](http://site.mockito.org/)'s `@InjectMocks` annotation).

### Using @Configuration classes

#### Pros
* Keeps the framework out of the classes implementing the business logic. Here you just need to add several `@Configuration` classes in your project to wire all the `@Bean`s together. Thus, switching to a new framework is much easier in this case.
* Keeping annotations of different frameworks out of your classes reduces noise and makes the code more readable. Although, once you get used to these annotations, they shouldn't be much distraction.

#### Cons
* Adding any new component requires an extra change in a separate configuration class to make sure an instance of the new component is created. This is an annoying extra step that is easy to forget about.
* Extra classes are needed just for the purpose of instantiating `@Bean`s and these classes will need to be maintained and occasionally refactored as your project evolves.
* If you choose to place all configuration classes in their own package you might have to make classes public that would otherwise be kept package private, just to be able to wire them into instances of the classes that depend on them. You could always have a configuration class per package, which makes perfect sense as a way of distributing bean creation across configuration classes, but then your configuration is smeared all over your business logic code again. Although now it should still be easier to change/remove them compared to the scenario in which you have `@Autowired` and `@Component` annotations everywhere.

