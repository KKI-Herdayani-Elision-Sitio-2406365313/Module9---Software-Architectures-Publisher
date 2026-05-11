# Publisher

### a. How much d
ata your publisher program will send to the message broker in one run?
The publisher sends 5 events in one run. Each event is a UserCreatedEventMessage that contains two fields, user_id and user_name. The five events are for users with id 1 to 5 and names Amir, Budi, Cica, Dira, and Emir. So in total, the publisher pushes 5 messages to the user_created queue every time it is executed.

### b. The url amqp://guest:guest@localhost:5672 is the same as in the subscriber program, what does it mean?
It means the publisher and the subscriber connect to the same RabbitMQ broker, using the same username, password, and port. This is important because the publisher needs to send the events to the same place where the subscriber is listening. If the URL is different, the two programs would connect to different brokers and the subscriber would not receive any message from the publisher.

## Running RabbitMQ as message broker
Below is the screenshot of the running RabbitMQ management UI at http://localhost:15672:

![img.png](images/img.png)

## Sending and processing event

Screenshot below shows the publisher console (left) sending 5 events, and the subscriber console (right) receiving and processing them:

![img_1.png](images/img_1.png)
![helo.png](images/imgrun.png)
When cargo run is executed in the publisher directory, the publisher opens a connection to RabbitMQ at amqp://guest:guest@localhost:5672, serializes 5 UserCreatedEventMessag and publishes them to the user_created queue. The subscriber, through its UserCreatedHandler::handle callback and prints the received message. 

## Monitoring chart based on publisher
![cargorun.png](images/cargorun.png)
Each spike on the message rate chart corresponds to a cargo run instruction of the publisher. 

## Simulation slow subscriber

![slowsubsriber.png](images/slowsubsriber.png)

After uncommenting thread::sleep(ten_millis) in the handler, the subscriber takes about 1 second to process each message. When I run the publisher several times in a row, the publisher sends events much faster than the subscriber can process them, so the messages start to pile up in the queue.

In my run, the queue reached N messages at the highest point. The number matches the total number of events sent by the publisher, which is the number of cargo run times 5, minus the events that the subscriber already finished processing during that time. In the tutorial example the peak was 20, which fits 4 publisher runs (4 × 5 = 20) executed faster than the subscriber could keep up. This is the reason why we use a message broker. The queue holds the events while the subscriber is still slow, so the publisher does not need to wait and no event is lost.

## Reflection — Running at least three subscribers

![run3timeterminal.png](images/run3timeterminal.png)
![run3time.png](images/run3time.png)


When I open three subscriber consoles, all of them connect to the same `user_created` queue. RabbitMQ then distributes the events between the three subscribers in a round-robin way, so each event is only handled by one subscriber. As a result, the work is divided between three programs instead of one, and the queue is processed faster. The peak on the chart is also lower than the previous run with only one subscriber, because the events are taken out of the queue more quickly.

### Things that can be improved in the code
- The subscriber main function ends with an empty loop, which keeps the CPU busy for no reason.
- The struct UserCreatedEventMessage is written twice, in the publisher and in the subscriber.
