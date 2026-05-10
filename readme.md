# Publisher

### a. How much data your publisher program will send to the message broker in one run?
The publisher sends 5 events in one run. Each event is a UserCreatedEventMessage that contains two fields, user_id and user_name. The five events are for users with id 1 to 5 and names Amir, Budi, Cica, Dira, and Emir. So in total, the publisher pushes 5 messages to the user_created queue every time it is executed.

### b. The url amqp://guest:guest@localhost:5672 is the same as in the subscriber program, what does it mean?
It means the publisher and the subscriber connect to the same RabbitMQ broker, using the same username, password, and port. This is important because the publisher needs to send the events to the same place where the subscriber is listening. If the URL is different, the two programs would connect to different brokers and the subscriber would not receive any message from the publisher.

## Running RabbitMQ as message broker
Below is the screenshot of the running RabbitMQ management UI at http://localhost:15672:

![rabbitmq.png](images/rabbitmq.png)



