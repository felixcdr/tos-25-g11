<!--
Guidance for AI coding agents working on TransportationOrderServer
Focus: concise, actionable rules and examples tied to files in the repo.
-->
# TransportationOrderServer — Copilot instructions

Quick summary
- Spring Boot (2.7.x) REST service. Java 11. Maven-based (mvnw present).
- Main components: `controller/TransportationOrderController.java` (REST endpoints),
  `model/TransportationOrder.java` (JPA entity), `repository/TransportationOrderRepository.java` (CrudRepository).

What you need to know to be productive

- Build & run
  - Use the included Maven wrapper from the project root:
    - Build & test: `./mvnw test` (or `./mvnw -q test` for quieter output)
    - Run service locally: `./mvnw spring-boot:run` (app runs on default Spring Boot port 8080)
  - Java version: 11 (see `pom.xml` property `<java.version>`).

- Test & fixtures
  - Tests use `@WebMvcTest` and `MockMvc` (see `src/test/.../TransportationOrderControllerTest.java`).
  - Sample data lives in `src/main/resources/orders.json` and is loaded by tests via ClassPathResource.
    Tests expect the file to contain one JSON object per line (the test reads line-by-line and maps each line to a TransportationOrder).
  - If you change the test expectations, update the fixture accordingly (the current test asserts 20 elements in the list).

- Data model and persistence
  - `TransportationOrder` is a JPA `@Entity`. Primary key is the `truck` field (`@Id`), type `String`.
  - Repository interface: `TransportationOrderRepository extends CrudRepository<TransportationOrder,String>` — standard CRUD methods are available.
  - The project includes H2 and Spring Data JPA in `pom.xml` — the app uses an embedded DB during tests/local runs.

- REST API patterns (use these exact paths and behaviors when editing or adding endpoints):
  - GET /transportationorders -> returns list of TransportationOrder (controller: `all()`)
  - POST /transportationorders -> create new order (request body -> saved entity)
  - GET /transportationorders/{truck} -> returns 200 + entity or 404 if not found
  - PUT /transportationorders -> updates and returns 200 or 404 per controller behavior
  - DELETE /transportationorders/{truck} -> deletes by truck id

- Conventions & common patterns
  - Lombok is used for boilerplate (`@Getter`, `@Setter`, `@NoArgsConstructor`, `@ToString`) in `TransportationOrder`.
  - Equality/hashCode is defined only on `truck` (so treat `truck` as unique identifier across code changes).
  - Controller methods often return `ResponseEntity<T>` for 200/404 control.
  - Unit tests isolate the controller with `@WebMvcTest` and mock the `TransportationOrderRepository` with `@MockBean`.

- API docs
  - `springdoc-openapi-ui` is included — when running locally, the OpenAPI UI is usually available at `/swagger-ui/index.html`.

Code examples (copy/paste when implementing similar features)

- Controller -> repository usage (create/list):
  - List: `List<TransportationOrder> all() { return (List<TransportationOrder>) repository.findAll(); }`
  - Create: `TransportationOrder newOrder(@RequestBody TransportationOrder newOrder) { return repository.save(newOrder); }`

Practical rules for code changes
- When adding fields to `TransportationOrder`, update:
  1) the constructor(s) and ensure Lombok annotations still provide needed accessors
  2) `equals`/`hashCode` only if identity semantics should change (current identity = truck)
  3) tests and `orders.json` fixtures

- When changing endpoint paths or behavior, update the unit test in `src/test/.../TransportationOrderControllerTest.java` and the fixture `orders.json`.

Files worth opening first
- `src/main/java/es/upm/dit/apsv/transportationorderserver/controller/TransportationOrderController.java`
- `src/main/java/es/upm/dit/apsv/transportationorderserver/model/TransportationOrder.java`
- `src/main/java/es/upm/dit/apsv/transportationorderserver/repository/TransportationOrderRepository.java`
- `src/test/java/.../TransportationOrderControllerTest.java` and `src/main/resources/orders.json`

If something is missing or unclear
- Tell me which endpoint, data field, or build/test step is unclear. I can update this doc with examples, add curl examples, or add small unit tests to clarify expected behavior.

End of file
