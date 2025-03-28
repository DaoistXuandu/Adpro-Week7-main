# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
```
In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?
```
If BambangShop needs to support multiple types of subscribers, such as email, SMS, or webhook notifications, using a trait is the best approach. A trait allows for flexibility and scalability by enabling different subscriber types to implement their own behavior while still adhering to a common interface. This follows the Open-Closed Principle, making it easy to add new subscriber types in the future without modifying existing code. Implementing a trait now ensures that the system remains adaptable if new features are needed later.

However, if the application only ever requires one type of subscriber, a single struct is sufficient, and adding a trait would introduce unnecessary complexity. Defining a trait in this case would add an extra layer of abstraction without providing any real benefit. Keeping the implementation simple with a single struct avoids over-engineering while still achieving the necessary functionality. The choice ultimately depends on whether the system is expected to evolve—if multiple subscriber types are anticipated, a trait should be used, but if only one type is needed, a simple struct is the better option.

```
id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?
```
A Vec would require manually checking for uniqueness before inserting a new subscriber. This process has an O(n) time complexity, meaning that as the number of subscribers grows, the performance degrades significantly. Every time a new subscriber is added, the system would have to iterate through the entire list to check for duplicates, which is inefficient and can slow down operations. Additionally, using Vec introduces a risk of accidental duplicates if checks are not performed correctly before insertion.

On the other hand, DashMap provides O(1) lookup time and ensures uniqueness by treating the id or url as a key in a key-value structure. This allows for fast and efficient data retrieval while automatically preventing duplicate entries. Given that BambangShop likely needs frequent lookups and modifications to its subscriber list, DashMap is the more efficient choice. It improves performance, reduces complexity, and ensures data consistency, making it a much better solution than Vec for handling unique subscribers.

```
When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?
```
The SUBSCRIBERS static variable needs to be thread-safe. The question is whether using DashMap is necessary or if the Singleton pattern can be used instead.

Solution: The Singleton pattern ensures that only one instance of the subscriber list exists, which helps with global state management. However, a Singleton alone does not provide thread safety. If multiple threads try to access the subscriber list simultaneously, race conditions could occur unless additional synchronization mechanisms, such as Mutex<HashMap> or RwLock<HashMap>, are implemented. These locks can cause performance overhead due to blocking, especially in a system with high concurrency.

DashMap, on the other hand, is designed for concurrent access and allows multiple threads to read and write safely without blocking each other. Since DashMap internally manages thread safety, it eliminates the need for manual locking, improving performance and efficiency. Even if a Singleton pattern is used to ensure a single instance, DashMap should still be the preferred choice for storing the subscriber list because of its built-in concurrency support. This makes DashMap the best solution for managing subscribers safely and efficiently in a multi-threaded environment.

#### Reflection Publisher-2
```
In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
```
Separating the “Service” and “Repository” layers follows the Single Responsibility Principle (SRP), ensuring that each component has a distinct purpose. The Repository layer is responsible for interacting with the database, handling queries, and managing data persistence, while the Service layer contains business logic and orchestrates interactions between different models. This separation makes the codebase more modular, testable, and maintainable. By keeping business logic out of the data access layer, we reduce dependencies and avoid direct coupling between the database and application logic.

If the Model handles both business logic and data access, it becomes difficult to manage as the application grows. Any changes in how data is stored or retrieved could require modifications to business logic, increasing the risk of unintended side effects. By using separate Service and Repository layers, we can change the database implementation without affecting business logic and vice versa. This approach also improves scalability, as different parts of the application can evolve independently without breaking the entire system.

```
What happens if we only use the Model? Explain your imagination on how the interactions between each model
```
If all logic is placed inside the Model, interactions between different models become tightly coupled, leading to increased code complexity. For example, if the Program model needs to notify a Subscriber, it would have to directly handle the logic for sending notifications, checking subscriber preferences, and ensuring message delivery. This makes the Program model responsible for multiple concerns beyond its primary function, violating the Single Responsibility Principle. As more features are added, the dependencies between models grow, making the system harder to maintain and debug.

With separate Service and Repository layers, these interactions become more structured and manageable. A NotificationService could handle message delivery, while a SubscriberService manages subscriber preferences. The Program model would only need to interact with these services instead of handling everything itself. This modular approach simplifies code maintenance, improves reusability, and makes it easier to introduce changes without affecting multiple parts of the system. Without this separation, every change to one model could have unintended consequences on others, making development slower and error-prone.



```
Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
```
Postman is a powerful API testing tool that helps developers interact with APIs, automate testing, and debug requests efficiently. One of its key advantages is allowing users to send HTTP requests without needing to write client-side code, making it easier to test endpoints quickly. It also provides detailed response data, including headers, status codes, and response times, which helps in identifying issues early in the development process.

Some of the most useful features in Postman include Collections, which allow organizing and reusing API requests, and Environment Variables, which enable testing the same API with different configurations. Automated testing through Postman’s built-in scripting capabilities is also valuable for continuous integration workflows. For group projects, collaboration features like shared workspaces help teams work on API development together. In future software engineering projects, Postman can be useful for API documentation, testing authentication flows, and ensuring API endpoints behave as expected before integrating them into frontend or backend applications.

#### Reflection Publisher-3
