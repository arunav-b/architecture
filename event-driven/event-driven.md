# Event Driven Architecture

<br/>

## Section 1 - Event Driven Microservices 

### 1. Introduction 

### 2. Communication & Data Contracts

### 3. Integration

### 4. Processing

### 5. Streaming

### 6. Building Workflows

### 7. Function-as-a-Service

### 8. Producer-Consumer Microservices

### 9. Heavyweight & Lightweight Framework Microservices

### 10. Integrating Event-Driven & Request-Response Microservices

### 11. Testing, Deployment & Supportive Tooling

<br/>
<br/>

## Section 2 - Patterns in Event Driven Architecture 

### 1. Event Forwarding Pattern

- **Problem**: Service A sends data to Service B over a messaging queue. Service B consumes the data and persists in the DB. There are 3 points of failure in this flow - 
    1. Service A sends the message to the queue but queue crashes before the message is consumed by Service B
    2. Service B consumes the message and before updating the DB, Service B crashes.
    3. While updating the DB, something fails.
    
- **Solution**: In order to overcome this, we will use the event forwarding pattern by ensuring the following things -
    1. Service A synchronously sends message to the Queue, i.e. Service A waits for the Queue to acknowledge the message is received and persisted.
    2. The message will be persisted in the Queue until it is acknowledged.
    3. Service B needs to acknowledge the message after it has completed processing.
    4. Last participant support for message acknowledgment.
        
- **Pros**:
    1. Message is never lost with this pattern.

- **Cons**:
    1. Service B needs to handle duplicate messages, because after the message is committed in the DB and before the acknowledgment is sent to the Queue is Service B crashes, then the message is still present in the Queue and is not deleted.     
          
<br/>
      
### 2. Thread Delegate Pattern

- Maintains the processing order using an event delegate which routes event to different threads based on a context.
- Event delegates can be sharded across different nodes based on some demographic logic even before event delegate receives the message.
- Useful for super high-speed order based processing like online trading, etc.

- **Pros**:
    1. Maintaining processing order
    2. Increased throughput
    3. Increase performance 
    4. Increase scalability 
    
- **Cons**:
    1. Increased complexity
    2. Possible Thread saturation
    3. Increased cost 
    4. Complex error handling      

<br/>

### 3. Ambulance / Carpool Pattern

- In Carpool Pattern, one thread (let's say thread 1) is dedicated to priority messages only and the other thread remains idle if no priority messages exist.

- In Ambulance Pattern, thread 1 can handle standard requests as well, but always checks priority queue first before getting the next standard message.

- **Pros**:
    1. Message Priority
    2. Throughput
    3. Performance

- **Cons**:
    1. Complexity when different priorities are present    

<br/>

### 4. Watch Notification Pattern

- **Problem**: How to send notification events to services without maintaining a persistent connection.
    - By having a broker doing some external config notification can have the following disadvantages -
        - Broker is the single point of failure
        - Excessive bandwidth utilization when there are huge number of services to notify
        - Can lead to saturation of the number of connections on the broker
        - There is no request mechanism on the broker, it only does notification
    
- **Solution**:    
    - Have an embedded queue in the external config server as well as the service to whom the notification needs to be sent.
    - Watch notification Service will **connect**, **send** & **disconnect** with the external config server.

- **Pros**:
    1. Lower Bandwidth
    2. Fewer connections
    3. Fault tolerance
    4. Brokerless topology
    
- **Cons**:
    1. Complexity
    2. Error Handling
    3. Performance (Slower because it has to connect, send and disconnect)

<br/>
           
### 5. Supervisor Consumer Pattern

- **Problem**: How to handle a varying request load and still maintain a consistent response time ?

- Real-world examples: Akka, Scala, Actor based implementations

- **Solution**: Supervisor constantly monitors the queue. Whenever the queue depth is greater than zero, it spawns up that many number of consumer threads (the logic can be determined based on the queue depth / number of available instances of the service) upto a certain threshold (max or some configurable threshold value of threads that can be supported by a particular instance of that service). Once the consumer threads processes the request and there are no more incoming load, or the load is less than the active consumers, supervisor takes care of deleting those threads there by maintaining an optimal memory usage and thread allocation.

- **Pros**:
    1. Programmatic elasticity
    2. Consistent responsiveness
    3. Optimized thread allocation
    4. Optimized memory usage 

- **Cons**:
    1. Increased complexity
    2. Possible thread saturation
    3. Possible memory errors
    4. Impact on other errors

<br/>

### 6. Contextual Queue Pattern

- **Problem**: How to load balance heterogeneous events to ensure consistent response time ?

- **Solution**: 

- **Pros**:

- **Cons**:

### 7. Multi Broker Pattern

<br/>

## References

- [Building Event Driven Microservices by Adam Bellamere](https://learning.oreilly.com/library/view/building-event-driven-microservices/9781492057888/)
- [Patterns in Event Driven Architecture by Mark Richards](https://learning.oreilly.com/live-training/courses/mastering-patterns-in-event-driven-architecture/0636920422259/)
- [Event Driven Architecture Patterns - Part 1](https://medium.com/wix-engineering/6-event-driven-architecture-patterns-part-1-93758b253f47)
- [Event Driven Architecture Patterns - Part 2](https://medium.com/wix-engineering/6-event-driven-architecture-patterns-part-2-455cc73b22e1)