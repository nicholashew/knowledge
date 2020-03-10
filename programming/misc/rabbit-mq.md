# RabbitMQ 

RabbitMQ is an open-source message-broker software that originally implemented the Advanced Message Queuing Protocol and 
has since been extended with a plug-in architecture to support Streaming Text Oriented Messaging Protocol, 
Message Queuing Telemetry Transport, and other protocols.

## How to Enable Management Interface

To enable the management interface, run the `RabbitMQ Command Prompt (sbin dir)`, you can find it in **Start** menu.

```bat
rabbitmq-plugins enable rabbitmq_management
```

The next thing is that we need to restart the Rabbit MQ server so that it can load the new plugings we just enabled and here is how we'll do that:

```bat
rabbitmq-server -detached
```

After this you can access the management interface locally from the browser by pointing to `http://<host>:15672` in your local machine . 

Opening the URL in the browser, you'll see a login page, enter `guest` for both `username` and `password` to access the administrative parts.

# Reference

- [Downloading and Installing RabbitMQ](https://www.rabbitmq.com/download.html)
- [RabbitMQ Tutorials](https://www.rabbitmq.com/getstarted.html)
- [How to enable RabbitMQ management interface](https://www.codementor.io/bosunbolawa/how-to-enable-rabbitmq-management-interface-owc5lzg7f)
