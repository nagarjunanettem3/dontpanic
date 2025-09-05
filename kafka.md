#### Producers, Consumers, Consumer Groups
Theory you must know:
- Producer: Publishes messages to a topic. Can choose partitioning strategy (round-robin, hash key, custom).
- Consumer: Reads messages from a topic (partition).
- Consumer Group: A set of consumers working together. Kafka assigns partitions so that each partition is consumed by only one consumer in the group.

#### Topic Partitions, Offsets, Replication

Theory you must know:
- Partition: Unit of parallelism in Kafka. A topic can have multiple partitions.
- Offset: Position of a record in a partition. Consumers track offsets to know what they’ve read.
- Replication: Each partition is replicated across brokers for fault tolerance. One replica is the leader, others are followers.

#### At-least-once vs Exactly-once Semantics

Theory you must know:
- At-most-once: Messages may be lost, but never processed twice.
- At-least-once: Messages may be processed more than once, but never lost (most common).
- Exactly-once: Each message is processed once and only once (hard to achieve, introduced with Kafka idempotent producers + transactions).

#### Kafka vs Traditional Message Queues (RabbitMQ, SQS)

Theory you must know:
- Kafka:
  - Distributed log, high-throughput, scalable.
  - Messages retained (not deleted after consumption).
  - Good for streaming & event sourcing.
- RabbitMQ / SQS:
  - Traditional queues.
  - Messages are deleted once consumed.
  - Better for task distribution / simple async jobs.


-----

🔹 Consumer Groups & Rebalancing

Q1. What happens if you add a new consumer to a consumer group?
- 	Why: Kafka must balance load across consumers. If a new one joins, partitions need to be redistributed.
- 	How: Kafka triggers a rebalance — partitions are reassigned so that each partition is owned by exactly one consumer. This improves parallelism but causes a short pause during reassignment.

Q2. What if one consumer in the group goes down?
- 	Why: High availability is a must; the system should not stall.
- 	How: Kafka detects missing heartbeats from that consumer. A rebalance happens, and remaining consumers take over the abandoned partitions.

Q3. How do you achieve ordering in Kafka consumers?
- 	Why: Many business flows (e.g., payments, user actions) depend on event order.
- 	How: Kafka guarantees ordering only within a single partition. To enforce strict ordering, send related messages to the same partition. If you must enforce global ordering, you’re forced to use a single partition (sacrificing throughput).

⸻

🔹 Partitions, Offsets, Replication

Q1. Why do we need partitions?
- 	Why: A single log limits throughput. Partitions allow scaling.
- 	How: A topic is split into partitions, which can be processed in parallel by different consumers across multiple brokers.

Q2. What if the leader broker of a partition goes down?
- 	Why: To ensure fault tolerance and availability.
- 	How: Kafka promotes one of the in-sync replicas (ISR) as the new leader, and producers/consumers automatically switch to it.

Q3. How do consumers manage offsets?
- 	Why: Consumers need to know where they left off to ensure no loss or duplication.
- 	How: Offsets can be auto-committed at intervals or manually committed after processing. For reliable systems, commit only after successful processing.

Q4. Can two consumers in different groups read the same partition?
- 	Why: Kafka must support fan-out (multiple independent subscribers).
- 	How: Yes — each consumer group tracks its offsets independently, so different groups can consume the same partition without conflict.

⸻

🔹 Delivery Semantics

Q1. How do you achieve at-least-once delivery in Kafka?
- 	Why: Guarantees no message loss, even if consumers fail.
- 	How: Process the message → then commit offset. If consumer crashes before commit, the message is replayed (may cause duplicates).

Q2. How do you achieve exactly-once in Kafka?
- 	Why: Some domains (payments, billing) cannot tolerate duplicates.
- 	How: Use idempotent producers (enable.idempotence=true) + transactions so that producing messages and committing offsets happen atomically.

Q3. If you have a payment system, which delivery guarantee would you use?
- 	Why: Financial correctness is critical.
- 	How: Prefer exactly-once semantics if infrastructure supports it. Otherwise, design idempotent consumers with at-least-once delivery (deduplicate using transaction IDs).

⸻

🔹 Kafka vs Other Message Queues

Q1. When would you choose Kafka over RabbitMQ?
- 	Why: Kafka’s strength is streaming + replay.
- 	How: Use Kafka when you need event replay, stream processing, or high-throughput pipelines (e.g., analytics, clickstream, audit logs).

Q2. When is RabbitMQ a better choice than Kafka?
- 	Why: RabbitMQ is optimized for traditional queuing.
- 	How: Use RabbitMQ when you need complex routing, per-message acknowledgment, or strict once-only delivery (e.g., job/task queue systems).

Q3. How does Kafka compare to AWS SQS?
- 	Why: Both are popular but serve different needs.
- 	How: SQS is fully managed, simple, pull-based, and deletes messages after consumption. Kafka gives you more control, allows multiple consumer groups, and supports event replay.

⸻

🔹 Performance & Reprocessing

Q1. How do you handle reprocessing old messages in Kafka?
- 	Why: To fix bugs or rebuild state.
- 	How: Reset consumer group offsets (kafka-consumer-groups.sh --reset-offsets) or start a new consumer group to re-read from the beginning.

Q2. How do you ensure Kafka can handle millions of messages/sec?
- 	Why: Kafka is built for scale, but tuning matters.
- 	How: Increase partitions for parallelism, enable batching (batch.size, linger.ms), and use compression (Snappy, LZ4).

Q3. What’s the tradeoff of having too many partitions?
- 	Why: Scaling isn’t free.
- 	How: More partitions = higher parallelism, but also more open file handles, metadata overhead, and slower recovery during broker restarts.

Q4. How do you handle backpressure in Kafka consumers?
- 	Why: To prevent lag from overwhelming consumers.
- 	How: Monitor lag, scale consumers horizontally, optimize consumer logic, or throttle producers.

Q5. How do you guarantee message ordering in Kafka?
- 	Why: To ensure sequence-sensitive workflows (e.g., inventory updates).
- 	How: Ordering is per partition. Use partition keys (like userID) so all related events go to the same partition.

⸻

🔹 Core Concepts & Kubernetes Deployment

Q1. What is Kafka and why is it called a distributed log system?
- 	Why: To confirm you understand Kafka’s architecture.
- 	How: Kafka stores records sequentially in partitions (logs). These logs are distributed across brokers, replicated for fault tolerance, and consumed sequentially.

Q2. If I have more pods than partitions, what happens?
- 	Why: To test scaling understanding.
- 	How: Extra pods remain idle because each partition can only be consumed by one consumer in the group.

Q3. How does Kafka prevent two pods from processing the same message?
- 	Why: To confirm exclusivity model.
- 	How: Partition assignment ensures only one consumer in a group reads from a partition. Offsets ensure only one progresses.

Q4. What if you want multiple pods to get the same message (fan-out)?
- 	Why: To check understanding of consumer groups.
- 	How: Use different consumer groups. Each group has its own offset tracking, so all groups get the same data independently.

-----


Kafka Interview Flow (for Go Backend Devs)

⸻

Tier 1: Basics (Testing Fundamentals)

👉 These check if you understand Kafka concepts at a developer’s level.

Q1. What is Kafka and why is it different from a traditional message queue?
- 	Why they ask: To check if you know Kafka isn’t “just RabbitMQ with different syntax.”
- 	How to answer: Kafka is a distributed log. Unlike queues, messages are retained for a configured time and can be replayed. Supports high throughput and multiple consumer groups reading independently.

Q2. How does Kafka guarantee ordering?
- 	Why they ask: To test if you know ordering is not global.
- 	How to answer: Ordering is only guaranteed within a partition. To preserve order for a key (e.g., userID), use the key when producing so all related messages land in the same partition.

Q3. How does a consumer know which messages it has already processed?
- 	Why they ask: To see if you understand offsets.
- 	How to answer: Each consumer tracks its offset in a partition. Offsets are committed to Kafka (__consumer_offsets). If committed after processing → at-least-once semantics.

⸻

Tier 2: Intermediate (Developer Workflow)

👉 These test your ability to design and implement reliable Kafka consumers/producers in Go.

Q4. What happens if you have more consumers in a group than partitions?
- 	Why they ask: To check if you understand scaling limits.
- 	How to answer: Consumers are assigned partitions exclusively. Extra consumers stay idle. Best practice is consumers ≤ partitions.

Q5. How do you ensure at-least-once delivery in a Go consumer?
- 	Why they ask: To test if you understand offset commit timing.
- 	How to answer: Fetch → process → commit offset. If crash happens before commit → message is replayed. May cause duplicates → must design idempotent consumers.

Q6. What’s the difference between auto-commit and manual commit for offsets?
- 	Why they ask: To test if you know tradeoffs.
- 	How to answer: Auto-commit = convenient but risky (commits before work is done). Manual commit = safer; you control when to commit (after DB write or API call).

Q7. How do you handle failed messages in Kafka consumers?
- 	Why they ask: To test reliability awareness.
- 	How to answer: Retry with exponential backoff; if still failing, send to Dead Letter Queue (DLQ). DLQ can be reprocessed later.

⸻

Tier 3: Advanced (System Reliability & Scaling)

👉 These check deeper understanding of Kafka’s design and how to apply it in Go microservices.

Q8. What is backpressure in Kafka and how do you handle it?
- 	Why they ask: To test if you know consumer lag is a real-world problem.
- 	How to answer: Backpressure happens when consumers can’t keep up. Handled by scaling consumers, batching, worker pools, and monitoring lag.

Q9. How does Kafka handle consumer crashes?
- 	Why they ask: To test if you know failure recovery.
- 	How to answer: Kafka detects heartbeat timeout, triggers rebalance, and reassigns partitions. On restart, consumer resumes from last committed offset.

Q10. How do you guarantee no duplicate payments (idempotency)?
- 	Why they ask: To test if you know at-least-once isn’t enough by itself.
- 	How to answer:
- 	Use idempotent consumer logic (dedupe by transaction ID).
- 	Commit offset only after DB write.
- 	Optionally use Kafka’s exactly-once semantics with idempotent producers + transactions.

Q11. How do you replay messages from Kafka if your logic was buggy yesterday?
- 	Why they ask: To test if you know offsets can be reset.
- 	How to answer: Reset consumer group offsets to a specific timestamp, or start a new consumer group. Must ensure consumer processing is idempotent.

⸻

Tier 4: Real-World Scenarios (System Design in Go)

👉 These test your applied knowledge in building services with Kafka.

Q12. You have a payment service with multiple pods. How do you ensure only one pod processes a message?
- 	Why they ask: To check partition-to-consumer mapping knowledge.
- 	How to answer: In Kafka, each partition is assigned to only one consumer per group. Multiple pods = Kafka balances partitions among them. If more pods than partitions → some pods are idle.

Q13. How do you monitor Kafka consumers in production?
- 	Why they ask: To see if you think about operability.
- 	How to answer: Monitor consumer lag (produced offset – committed offset). Add logging for topic/partition/offset. Expose metrics via Prometheus/Grafana.

Q14. How do you choose serialization format in Kafka?
- 	Why they ask: To see if you balance dev speed vs performance.
- 	How to answer:
- 	JSON: easy, but slower and larger.
- 	Avro/Protobuf: compact, enforce schema, safer for evolution.
- 	With Schema Registry: prevent breaking changes.

Q15. You notice consumer lag spikes at peak traffic. What do you do?
- 	Why they ask: To test scaling & tuning skills.
- 	How to answer: Add more partitions, scale consumers, batch DB writes, optimize consumer logic, or throttle producers.

⸻

✅ Summary
- 	Basic: Topics, partitions, offsets.
- 	Intermediate: Offset commits, idempotency, DLQ.
- 	Advanced: Backpressure, scaling, reprocessing, exactly-once semantics.
- 	Real-world: Payments, multi-pod deployments, monitoring, serialization.



