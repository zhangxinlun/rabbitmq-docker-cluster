# Clustering RabbitMQ Docker containers

This repository accompanies a blog article/YouTube video I created to walk people through the setup of a RabbitMQ cluster on their machine.
Go to my website to learn more about the setup:

[Set up a RabbitMQ cluster on your laptop using Docker](https://oprea.rocks/blog/set-up-a-rabbitmq-cluster-on-your-laptop-using-docker/)

## Using `docker run`

In this section you will learn the commands used to launch a RabbitMQ cluster using the regular docker engine `run` command.

1. Start master node:
```bash
docker run -d \
    --name="rabbit1" \
    --hostname="rabbit1"\
    -e RABBITMQ_ERLANG_COOKIE="secret string" \
    -e RABBITMQ_NODENAME="rabbit1" \
    --volume=(pwd)/rabbitmq.config:/etc/rabbitmq/rabbitmq.config \
    --volume=(pwd)/definitions.json:/etc/rabbitmq/definitions.json \
    --publish="4369:4369" \
    --publish="5671:5671" \
    --publish="5672:5672" \
    --publish="15671:15671" \
    --publish="15672:15672" \
    --publish="25672:25672" \
    rabbitmq:3-management
```
2. Start slave #1:
```bash
docker run -d \
    --name="rabbit2" \
    --hostname="rabbit2"\
    -e RABBITMQ_ERLANG_COOKIE="secret string" \
    -e RABBITMQ_NODENAME="rabbit2" \
    --volume=(pwd)/rabbitmq.config:/etc/rabbitmq/rabbitmq.config \
    --volume=(pwd)/definitions.json:/etc/rabbitmq/definitions.json \
    --link="rabbit1:rabbit1" \
    rabbitmq:3-management
```

3. Start slave #2:
```bash
docker run -d \
    --name="rabbit3" \
    --hostname="rabbit3"\
    -e RABBITMQ_ERLANG_COOKIE="secret string" \
    -e RABBITMQ_NODENAME="rabbit3" \
    --volume=(pwd)/rabbitmq.config:/etc/rabbitmq/rabbitmq.config \
    --volume=(pwd)/definitions.json:/etc/rabbitmq/definitions.json \
    --link="rabbit1:rabbit1" \
    --link="rabbit2:rabbit2" \
    rabbitmq:3-management
```

4. View container logs individually
```bash
docker logs -f <rabbit#>
```

This will display the logs for the chosen container, and follow them just like `tail -f /log/path` would do.

5. Run producer / consumer

The producer / consumer scripts were created as simple Node.js scripts so they can be executed using regular bash script execution syntax.


## Tools

Very useful tool for simulating message circulation in your RabbitMQ setup.

http://tryrabbitmq.com/

## 下面是自己修改后的，只需要按下面执行命令就可以了
```bash
docker stack deploy -c docker-compose.yml rabbit
```
