# Kinetic 🚀

[![Java Version](https://img.shields.io/badge/Java-17%20%2F%2021-orange.svg)](https://www.oracle.com/java/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Kinetic** is a minimalist, action-driven command dispatcher and micro-workflow engine for Java backend applications. 

Tired of creating a new Controller, mapping new URLs, and writing repetitive boilerplate code for every single business action? **Kinetic solves this by introducing a Unified Single-Gateway architecture.** All state-changing actions route through one single endpoint, automatically distributing payloads to your registered decoupled Action Beans based on Java Reflection and Parameter-Type matching.

Turn your static architecture into dynamic **kinetic energy**.

---

## 🌟 Key Features

* **Single-Gateway Architecture (`/api/v1/actions`):** One Controller to rule them all. No more endpoint proliferation.
* **Zero-Boilerplate Routing:** Drop the mapping annotations. Just declare an Action Bean, and Kinetic handles the discovery, routing, and invocation.
* **Parameter-Type Driven Auto-Mapping:** Powered by Jackson, Kinetic inspects your action's execution method and automatically deserializes the raw JSON payload into your specific type-safe DTO.
* **Built-in JSR-303 Validation:** Automatically validates your input DTOs using standard annotations (`@NotBlank`, `@Min`, etc.) before the business logic is ever touched.
* **The Action Lifecycle (Future-proof):** Designed to seamlessly support structured hooks for Guards (pre-conditions), Core Execution, and Asynchronous Side-Effects.

---

## 📦 How It Works: The Workflow

Instead of creating a Controller + Service + DTO layer for every request, Kinetic flattens your development into just two core pieces: **The Input Data Model** and **The Action Logic**.

### 1. Define your Typed Payload (DTO)

~~~java
public class SubmitOrderDTO {
    @NotBlank(message = "Customer ID is required")
    private String customerId;

    @Min(value = 1, message = "Amount must be greater than 0")
    private double amount;

    // Getters and Setters
}
~~~

### 2. Create your Decoupled Action Bean

~~~java
@Component
@ActionDefinition(name = "SUBMIT_ORDER")
public class SubmitOrderAction {

    @ActionExecute
    public ActionResponse execute(SubmitOrderDTO dto) {
        // Your clean, isolated business logic goes here!
        System.out.println("Processing order for customer: " + dto.getCustomerId());
        
        return ActionResponse.success("Order processed successfully");
    }
}
~~~

### 3. Trigger it via a Single Endpoint
Clients simply send a POST request to your unified endpoint with the targeted action name:

**`POST /api/v1/actions`**

~~~json
{
  "action": "SUBMIT_ORDER",
  "payload": {
    "customerId": "CUST-99",
    "amount": 250.50
  }
}
~~~

Kinetic intercepts the request, maps the payload into `SubmitOrderDTO`, validates the constraints, and triggers `SubmitOrderAction.execute()` flawlessly.

---

## 🛠️ Architecture Deep Dive

Kinetic shifts your architecture from being *endpoint-driven* to *behavior-driven*.

1. **Ingress (The Funnel):** The centralized `UnifiedActionController` accepts all incoming JSON packets.
2. **Registry:** At bootstrap, Kinetic scans the application context and caches all `@ActionDefinition` beans into an internal high-performance Map.
3. **Dispatcher Engine:** When an action arrives, the engine dynamically extracts the method argument type, deserializes the unstructured map using Jackson, runs `jakarta.validation`, and invokes the targeted bean via Reflection.

---

## 🗺️ Roadmap & Ecosystem Extensions

Kinetic is built to easily scale beyond simple local invocation:
- [ ] **Distributed Lock Integration:** Prevent race conditions on cluster environments using Redis/Redisson before executing actions.
- [ ] **Saga Pattern Coordinator:** Support structured rollbacks if sequential multi-service executions fail.
- [ ] **Reactive Side-Effects Bus:** Push post-execution hooks (emails, notifications, streaming) completely asynchronously using Project Reactor.
- [ ] **Transport Agnostic Ingress:** Easily swap or add gRPC, GraphQL, or Kafka Event consumers without altering a single line of your Action Beans.

---

## 📄 License

Distributed under the Apache 2.0 License. See `LICENSE` for more information.
