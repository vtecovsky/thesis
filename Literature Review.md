The aim of my work is to develop and analyse a fault-tolerant event-driven system that guarantees data consistency and system recovery in case of failure by applying Saga, Outbox, and Inbox patterns. The study will contain a comparison between 2 systems with identical functional requirements, but one employs listed patterns and another one does not. The comparison will focus on three main aspects - reliability, performance, and architectural complexity - in order to evaluate the practical impact of applying fault-tolerance and data consistency mechanisms in microservices.

To establish a theoretical foundation for this study, it is essential to review existing literature on the architectural patterns involved. The following section provides an overview and critical analysis of studies related to the Saga, Outbox, and Inbox patterns, highlighting their principles, practical applications, implementation challenges, and comparative advantages in achieving data consistency and resilience in microservices. 

### The Saga Pattern

The Saga Pattern is one of the key mechanisms for maintaining data consistency across multiple microservices without using distributed transactions. It divides a long lived transaction into a sequence of smaller local transactions, each executed by an individual service. If one of the steps fails, the system triggers compensating actions to undo the effects of previously completed transactions. This pattern allows distributed systems to preserve data consistency while remaining available and scalable. Both the conceptual foundation and practical implementation of this approach are examined in detail by Dürr, Lichtenthäler, and Wirtz in _“An Evaluation of Saga Pattern Implementation Technologies”_ [2].

In their study, the authors analyze how the Saga Pattern can be realized using existing orchestration and choreography frameworks. They compare two implementation technologies—**Eventuate Tram Saga Framework** and **Netflix Conductor**—based on a reference use case: a travel booking application composed of multiple services. The evaluation focuses on several aspects, including design simplicity, execution monitoring, fault handling, and compensation management. The paper highlights how the orchestration-based approach, in which a central coordinator manages the workflow, contrasts with the choreography-based approach, where services react to events independently.

The authors provide a detailed qualitative analysis of both technologies by implementing the same business process using each framework. Their findings show that while orchestration solutions offer clearer control flow and better observability, choreography promotes loose coupling and flexibility at the cost of higher complexity in error handling. The study also emphasizes that compensation logic, while essential for reliability, significantly increases development and testing overhead. Although the paper does not include quantitative performance metrics, it systematically identifies trade-offs related to maintainability, monitoring, and fault recovery.

Overall, this article provides a valuable contribution to understanding how the Saga Pattern operates in practice and how different frameworks influence its effectiveness. It bridges theoretical discussions of distributed consistency with empirical observations from real-world implementations. For the purposes of this work, the study serves as an important reference for analyzing the impact of the Saga Pattern on system reliability and architectural complexity, forming a strong basis for comparing fault-tolerant design approaches in microservice environments.

### The Outbox Pattern

The Outbox Pattern is an essential part of reliable message delivery in event-driven architectures that rely on message brokers. According to the Outbox Pattern, an application tends to write business data to a relational database and, within the same transaction, store a corresponding event. The saved event can then be processed asynchronously and published to a message broker, ensuring data consistency between the local storage and external consumers. This approach prevents data loss and reduces the risk of inconsistencies caused by partial failures in distributed nature. Both the theoretical rationale and practical implementation of this pattern are discussed in detail by Gunnar Morling in “Reliable Microservices Data Exchange With the Outbox Pattern” [1]. 

In his article, Morling describes how the Outbox Pattern addresses the well-known “dual writes” problem, which occurs when an application needs to update its database and send an event to another service simultaneously. The proposed solution introduces an _outbox table_ that stores pending events as part of the same database transaction as the business logic. An asynchronous worker then processes saved message and sends it to the message broker. This design guarantees atomicity between several components without relying on distributed transactions, thus improving both reliability and scalability in microservice systems.

Morling supports his explanation with a concrete implementation example using Java, PostgreSQL, Debezium, and Kafka. He demonstrates how Change Data Capture (CDC) connectors can automatically detect and publish committed changes, ensuring at-least-once delivery even under system restarts or temporary network failures. The article also discusses essential operational considerations, including message deduplication, idempotency, and topic management. However, it lacks formal performance evaluation, it provides valuable engineering insight into the trade-offs of the Outbox Pattern, such as additional infrastructure complexity and minor propagation delays.

Overall, Morling’s work effectively bridges the gap between architectural theory and practical usage. It shows how the Outbox Pattern, when combined with CDC mechanisms, enables fault-tolerant and consistent communication in distributed systems. For the purposes of this work, the article serves as a key reference demonstrating how transactional outbox mechanisms can ensure reliable, event-driven data communication across microservices.


### The Inbox Pattern

While the Outbox Pattern is used to reliably send messages, the Inbox Pattern ensures idempotency and correct message ordering on the consumer side. Alaftekin’s article _“Reliable Messaging in Microservices: The Outbox and Inbox Pattern”_ [3] highlights the Inbox Pattern as a key mechanism for achieving exactly-once processing. In this pattern, each service maintains an inbox table that records incoming messages along with a unique identifier. Before processing a message, the service checks the inbox table to determine whether the message has already been handled, thereby ensuring idempotent processing and preventing duplicates.

Alaftekin illustrates the Inbox Pattern in conjunction with the Outbox Pattern using a practical e-commerce example. When an order is created, the producing service saves the event in its outbox table. The consuming service then receives the event and first inserts it into its inbox table before executing any business logic. This approach guarantees that even if a message is delivered multiple times due to network retries or broker failures, it will only be processed once. The Inbox Pattern thus acts as a critical safeguard in event-driven systems, working closely with the Outbox Pattern to ensure end-to-end reliability.

The article further discusses practical implementation aspects, including the need for deduplication strategies, idempotent handlers, and consistent storage for inbox records. While it does not provide formal benchmarks or large-scale performance analysis, it gives clear engineering guidance for implementing the Inbox Pattern in production systems. By focusing on consumer-side reliability, the Inbox Pattern significantly reduces the risk of inconsistent state in microservices and supports robust fault-tolerant design.

Overall, Alaftekin’s work provides valuable insights into how the Inbox Pattern ensures reliable and consistent message processing. For this thesis, it serves as a practical reference demonstrating the consumer-side mechanisms necessary to achieve fault-tolerant, event-driven microservice communication.


Resources:

[1] - https://debezium.io/blog/2019/02/19/reliable-microservices-data-exchange-with-the-outbox-pattern/ 

[2] - https://ceur-ws.org/Vol-2839/paper12.pdf 

[3] - https://medium.com/%40serhatalftkn/reliable-messaging-in-microservices-the-outbox-and-inbox-pattern-2f831f15ff82 

