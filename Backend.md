# Knowledge- <!-- omit in toc -->

Backend for Java and Kotlin.

## Table of contents <!-- omit in toc -->

- [Java 8](#java-8)
  - [Collection](#collection)
  - [Stream](#stream)
  - [Patterns](#patterns)
  - [Spring Boot (Spring)](#spring-boot-spring)
    - [Beans scope](#beans-scope)
  - [Concurrency: Threads (CompletableFuture API)](#concurrency-threads-completablefuture-api)
- [Kotlin](#kotlin)
  - [Nullable: es pot forçar a que una variable sigui not nullable](#nullable-es-pot-forçar-a-que-una-variable-sigui-not-nullable)
  - [Safe Calls](#safe-calls)
  - [Elvis Operator](#elvis-operator)
  - [The !! Operator](#the--operator)
  - [\* VS ANY](#-vs-any)
  - [Llibreries utils:](#llibreries-utils)
  - [Testing](#testing)
    - [Configuracio:](#configuracio)
  - [Que testejar?](#que-testejar)
  - [Links interessants:](#links-interessants)
  - [Configuracio](#configuracio-1)

# Java 8

## Collection

A Collection is an in-memory data structure, which holds all the values that the data structure currently has. Every element in the Collection has to be computed before we add it to the Collection. Operations such as searching, sorting, insertion, manipulation, and deletion can be performed on a Collection. It provides many interfaces like (Set, List, Queue, Deque) and Classes like (ArrayList, Vector, LinkedList, PriorityQueue, HashSet).

```
// Java Program to Illustrate Collection

// Importing required classes
import java.io.*;
import java.util.*;

// Main class
class GFG {

	// Main driver method
	public static void main(String[] args)
	{
		// Creating an instance of list
		List<String> CompanyList = new ArrayList<>();

		// Adding elements using add() method
		CompanyList.add("Google");
		CompanyList.add("Apple");
		CompanyList.add("Microsoft");

		// Now creating a comparator
		Comparator<String> com
			= (String o1, String o2) -> o1.compareTo(o2);

		// Sorting the list
		Collections.sort(CompanyList, com);

		// Iterating using for each loop
		for (String name : CompanyList) {

			// Printing the elements
			System.out.println(name);
		}
	}
}
```

## Stream

On the other hand, IStream is an API that is introduced in Java 8 which is used to process collections of objects. A stream is a sequence of objects that supports various methods which can be pipelined to produce the desired result. The Stream API is used to process collections of objects. A stream is a sequence of objects that supports various methods that can be pipelined to produce the desired result.

The features of the Java stream are:

A stream is not a data structure instead it takes input from the Collections, Arrays, or I/O channels.
Streams don’t change the original data structure, they only provide the result as per the pipelined methods. Streams only provide the result as per the pipelined methods and don’t change the original data structure.
Each intermediate operation is lazily executed and returns a stream as a result, hence various intermediate operations can be pipelined. Terminal operations mark the end of the stream and return the result.

```
// Java Program to Illustrate Collection

// Importing required classes
import java.io.*;
import java.util.*;

// Main class
class GFG {

	// Main driver method
	public static void main(String[] args)
	{
		// Creating an instance of list
		List<String> CompanyList = new ArrayList<>();

		// Adding elements using add() method
		CompanyList.add("Google");
		CompanyList.add("Apple");
		CompanyList.add("Microsoft");

		// Now creating a comparator
		Comparator<String> com
			= (String o1, String o2) -> o1.compareTo(o2);

		// Sorting the list
		Collections.sort(CompanyList, com);

		// Iterating using for each loop
		for (String name : CompanyList) {

			// Printing the elements
			System.out.println(name);
		}
	}
}
```

https://www.geeksforgeeks.org/difference-between-streams-and-collections-in-java/

## Patterns

How to pass from a imperative function to functional one using patterns
**Key part**: never type **-> {** (anonymous lambdas)
How to:

1. Transform it using Stream API
2. Prefer Named Functions Over Anonymous Lambdas (toDto)
3. Create a class for that (UserDto) and put that logic on the constructor. Refering that using **double colon operator** (https://www.baeldung.com/java-8-double-colon-operator) **.map(UserDto::new)**
4. (optional in case we need other component inside de lambda function):

```
public class UserFacade {
          @Autowired
          private UserRepo userRepo;
          @Autowired
          private UserMapper mapper;

          public List<UserDto> getAllUsers() {
                   return userRepo.findAll().stream().map(mapper::toDto).collect(toList());

          }
}

@Component
public class UserMapper {
          @Autowired
          private OtherClass otherClass;

          public UserDto toDto(User user) {
                   UserDto dto = new UserDto();
                   dto.setUsername(user.getUsername());
                   ... // code using otherClass
                   return dto;
          }
}
```

https://dzone.com/articles/functional-programming-patterns-with-java-8

## Spring Boot (Spring)

### Beans scope

By default, beans are defined as "Singleton", which means that Spring inject just once instance for application.
If we want to have one instance for each request to that bean, we have to use "Prototype" scope. Be carefull because the user must controll the creation and destruction of each instance, otherwise we can collapse easily the RAM memory.

<!-- TODO: WIP profiles. -->

- Run the application: `mvn spring-boot:run`:
  - [Profiles](https://stackoverflow.com/a/54668353): `mvn spring-boot:run -Dspring-boot.run.profiles=local` or `SPRING_PROFILES_ACTIVE=local mvn spring-boot:run` (bash).
  - In debug, add `-Dspring-boot.run.fork=false` if the execution does not stop at breakpoints.
- Quick application restarts: [Hot Swapping](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-hot-swapping) and [Developer Tools](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools).

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
  </dependency>
  ```

<!-- TODO: add in an example code and validate if this is the current best solution. -->

- [Catch parameter parsing exception in Spring 3.0 WebMVC](https://stackoverflow.com/a/4955943).
<!-- TODO: should this be moved to Java Logging? Is this Spring specific? -->
- [Configure log level](https://docs.spring.io/spring-boot/docs/2.1.1.RELEASE/reference/html/boot-features-logging.html):

  ```properties
  # logging.level.<logger-name>=<level>.
  # logger-name=root, change the logging level for all packages.
  # level=FATAL > ERROR > WARN > INFO > DEBUG > TRACE, or OFF
  logging.level.root=WARN
  logging.level.org.springframework.web=DEBUG
  logging.level.org.hibernate=ERROR
  ```

- [Show/hide endpoint(s)](https://www.baeldung.com/spring-swagger-hiding-endpoints): add `@ApiIgnore` to the interface, controller or class.

- Test [clean context](https://www.baeldung.com/spring-dirtiescontext):
  - Class level: `@DirtiesContext(classMode = ClassMode.AFTER_CLASS)`.
    - Options: `BEFORE_CLASS`, `BEFORE_EACH_TEST_METHOD`, `AFTER_EACH_TEST_METHOD` or `AFTER_CLASS`.
  - Method level: `@DirtiesContext(methodMode = MethodMode.AFTER_METHOD)`.
    - Options: `BEFORE_METHOD` or `AFTER_METHOD`.

## Concurrency: Threads (CompletableFuture API)

Runneable -> Doesn't return. https://www.callicoder.com/java-8-completablefuture-tutorial/
Callable -> Return. https://www.callicoder.com/java-callable-and-future-tutorial/
ExecutorService -> An Executor that provides methods to manage termination and methods that can produce a Future for tracking progress of one or more asynchronous tasks.

---

# Kotlin

Caracteristiques del llenguatge
Type inference: Dedueix el tipo d’una variable per la seva asignacio
`val apple = "apple" // String`

## Nullable: es pot forçar a que una variable sigui not nullable

```
val noNull: String = "s"
val nullable: String? = null
```

## Safe Calls

```
val a = "Kotlin"
val b: String? = null
println(b?.length) // This returns b.length if b is not null, and null otherwise
println(a?.length) // Unnecessary safe call

Output:
null
6
```

## Elvis Operator

When we have a nullable reference b, we can say "if b is not null, use it, otherwise use some non-null value":
`val l: Int = if (b != null) b.length else -1`

Along with the complete if-expression, this can be expressed with the Elvis operator, written ?::
`val l = b?.length ?: -1`

## The !! Operator

Per convertir un valor a un tipus no nullable, quan estem segurs que no pot ser null (sino seguiria llençant el NPE =NULLPOINTEREXCEPTION)

## \* VS ANY

El _ indica que pot ser d’un tipus Xm pero tots seran del tipus X. (ex: Link<_>)
El any indica que pot ser de qualsevol tipus. (ex: Link<any>)

## Llibreries utils:

mapstruct

## Testing

Llibreries usades

- Junit.jupiter
- io.mockk

### Configuracio:

en el arxiu application.yml de la carpeta test. Exemple:
cisl:
config:
commonsDispatcherUrl: http://localhost:${wiremock.server.port}/
    authUrl: http://localhost:${wiremock.server.port}/oauth/token
grantType: client_credentials
clientId: testClientId
clientSecret: testSecret
user: user
password: password

itmp:
mo:
creditor:
repository:
mock:
enabled: false

bff:
abs:
mapping:
users:
DE: 0000000
agentNumbers:
DE: 0000000

logging:
level:
root:
org:
junit: WARN

spring:
sleuth:
enabled: false
zipkin:
enabled: false

## Que testejar?

- Controlador: Mira que retorna el json esperat. Es pot comparar a nivell de json o d’objecte
- Service: Test Unitari. es mockqueja el repository pero no el client HTTP. Per tant, simplement, quan en el servei es fa una crida HTTP, s’indica quines dades retorna per aquella crida.
- Component: Es mockeja el client HTTP (el FeignClient) per tal de simular les mateixies condicions que s’usan quan es crida als repositories.

## Links interessants:

Parametrizar el tests: https://sam09.github.io/Parameterized-Tests-Kotlin/
How to use MapStruct with Spring Boot (Kotlin) painlessly

## Configuracio

Com llençar el mvn clean install de tots els moduls menys uns en concret: usant el !

Exemples:
Bash: `mvn clean install -DskipTests -pl \!application-policy-admin,\!code-coverage-report,\!itmp-mo-contract-admin,\!itmp-mo-contract-amendment`
CMD: ` mvn clean install -DskipTests -pl !application-policy-admin,!code-coverage-report,!itmp-mo-contract-admin,!itmp-mo-contract-amendmen`
