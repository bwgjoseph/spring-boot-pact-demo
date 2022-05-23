# Spring Boot Pact Demo

This project is written to attempt to understand more about `Consumer Driven Contract` with the use of Spring Boot + [Pact](https://pact.io/)

## Project

This repository includes two sub-project;

- [pact-consumer](./pact-consumer/)
- [pact-provider](./pact-provider/)
  - Provides two API
    - GET /profiles
    - GET /profiles/{id}

### Run

Navigate to the individual directory and run

```bash
./gradlew bootRun
```

### Build

Navigate to the individual directory and run

```bash
./gradlew [clean] build
```

## Contract

### Consumer

After consumer project is built, it will output the generated consumer contract to `/pact-consumer/build/pacts/` which can then be copied to provider project for test

## Consideration

- Consumer Driven Contract [Pact] vs Provider Driven Contract [SCC]
- Ease of use
- Learning curve / Overhead

## Snippet

### PactDslJsonBody

```java
// {
//     "profiles": [
//         {
//             "id": 1,
//             "name": "Joseph",
//             "age": 22,
//             "email": "jose@gmail.com",
//             "dob": "2000-01-01"
//         },
//     ]
// }
new PactDslJsonBody()
    .minArrayLike("profiles", 1, 1)
        .integerType("id", 1)
        .stringType("name", "fake")
        .integerType("age", 10)
        .stringType("email", "fake@gmail.com")
        .date("dob")
        .closeObject()
    .closeArray()
```

### PactDslJsonArray

```java
// [
//     {
//         "id": 1,
//         "name": "Joseph",
//         "age": 22,
//         "email": "jose@gmail.com",
//         "dob": "2000-01-01"
//     }
// ]
PactDslJsonArray.arrayEachLike()
    .integerType("id", 1)
    .stringType("name", "fake")
    .integerType("age", 10)
    .stringType("email", "fake@gmail.com")
    .date("dob")
.closeObject()
```

## Further exploration

Possibly look into using [Spring Cloud Contract](https://spring.io/projects/spring-cloud-contract#overview) with [pact-broker](https://cloud.spring.io/spring-cloud-contract/reference/html/howto.html#how-to-use-pact-broker)

## Known Issue

- Since `au.com.dius.pact.consumer:junit5:4.3.0` onwards, when running the test, it will throw error `java.lang.UnsupportedOperationException: Method getSingleProfile does not conform required method signature 'public au.com.dius.pact.core.model.V4Pact xxx(PactBuilder builder)'`
  - See [test-report](issues/pact-4.3.0/test/index.html), most likely related to this [issue](https://github.com/pact-foundation/pact-jvm/issues/1488) and this [note](https://docs.pact.io/implementation_guides/jvm/upgrade-to-4.3.x)
  - To overcome this, we can change to `PactSpecVersion.V3` like such `@PactTestFor(providerName = "ProfileProvider", pactVersion = PactSpecVersion.V3)`
  - Seem like `PactSpecVersion.V4` is the default, and is incompatible with `V3`
- When running the test via `VSCode` (manual click), `pact` generated contract will be output to `target/pact` even though using `gradle`. However, if running via command `./gradlew build`, there won't be such issue

## Reference:

- [pact-workshop-Maven-Springboot-JUnit5](https://github.com/pact-foundation/pact-workshop-Maven-Springboot-JUnit5)
- [contract-test-spring-cloud-contract-vs-pact](https://blog.devgenius.io/contract-test-spring-cloud-contract-vs-pact-420450f20429)