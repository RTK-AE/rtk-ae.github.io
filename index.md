# Mission

RTK ⌘ UAE is an RTK Drone Environment Surveying organization operating in UAE, established in 2019.

[RTK](https://en.wikipedia.org/wiki/Real-time_kinematic) is a navigation technique allowing to achieve centimeter 
precision of an object or landscape on a map. 

Our mission is to provide services, using RTK drones and Artificial Intelligence for collecting and analyzing data:

* Environmental and ecological monitoring, mapping and timelapse:
  * Sand dune migration
  * Desert temperature
  * Gulf air temperature
  * Gulf wind map
  * Oil ecological monitoring

![AW19 Mk2](https://avatars1.githubusercontent.com/u/51878591?s=200&v=4)

# Products

Our projects are hosted in [Bintray Maven Repository](https://bintray.com/infinite-technology/m2) 
and are publicly available via [Bintray JCenter](https://bintray.com/bintray/jcenter).

Our projects form the `Infinite Project Stack`, synergistically supplementing each other into a functional vertical
raising from lower-level components (compile-time AST transformations) to enterprise solutions (Messaging, 
Service Transformation Layer, API Security).

## Supplies

Misc commons/utilities/tools, specifically notable for `Marker` functionality, automatically showing line numbers of code 
during runtime.

* [See more](./Supplies/)


## Bobbin

Revolutionary high-performance Groovy/Java Slf4j logger.

`Bobbin` is a high-performance Groovy Slf4j-compatible logger designed for multi-threaded applications (especially those
 with persistent threads like batch and messaging applications).

`Bobbin` leverages the concept of Logback/Log4j2 sifting appenders while providing much more easier configuration using
 native Groovy/Java scripting expressions.

* [See more](./Bobbin/)

## Carburetor

Parameterized Groovy AST method Transformer.

`Carburetor` provides a foundation for other libraries to automatically generate Groovy Semantic handling code based on 
`Carburetor` configuration and inject it into User code during the Compilation stage resulting in a possibility to 
intercept and interact with application run-time events including their corresponding compile-time metadata 
(class name, method name, line start, line end, column start, column end, ASTNode class name).

* [See more](./Carburetor/)

## BlackBox

Logging code automation solution.

`BlackBox` is a solution to automatically generate Groovy Semantic logging code and inject it into User code during the 
Compilation stage resulting in a possibility to produce and review exhaustive application runtime data in a form of log
 files with structure based on simplified Groovy AST class model.

* [See more](./BlackBox/)

## Pigeon

An end-user server application (HTTP Message Broker) designed for distribution of text messages in HTTP format.

`Pigeon` is capable to:

* Enqueue a text message from external source using REST API or direct insert into Pigeon DB by the external app
* Convert it into one or more HTTP messages with a specified body/query string parameters using appropriate `Plugins` (plugins can be developed by end-users using Groovy script)
* Dispatch the resulting messages ansynchronously to one or more recipients (URLs) using a variety of HTTP connection and authentication mechanisms (such as AWS v4 signature)
* If needed retry sending the message several times

* [See more](./Pigeon/)