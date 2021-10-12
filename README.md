## What is this ??

It's a docker-compose script which was inspired by this guide https://github.com/debezium/debezium-examples/blob/master/apache-pulsar/README.md.
I've added in a health check on the pulsar container because it needs to be up before debezium server comes up. Additionally I've included a flink job manager and task manager.

Here is the list of all containers:
    - Debezium server (checkout the conf directory for the configs)
    - Postgres (as a source for debezium)
    - Apache Pulsar (as a sink for change data capture data (the inventory schemas are whitelisted for CDC))
    - Flink job manager
    - Flink data manager

```
       Name                      Command                  State                                             Ports
--------------------------------------------------------------------------------------------------------------------------------------------------------
flink_debezium_1      /debezium/run.sh                 Up             0.0.0.0:9080->8080/tcp,:::9080->8080/tcp, 8443/tcp, 8778/tcp
flink_jobmanager_1    /docker-entrypoint.sh jobm ...   Up             6123/tcp, 0.0.0.0:8081->8081/tcp,:::8081->8081/tcp
flink_postgres_1      docker-entrypoint.sh postgres    Up             0.0.0.0:5432->5432/tcp,:::5432->5432/tcp
flink_pulsar_1        bin/pulsar standalone            Up (healthy)   0.0.0.0:6650->6650/tcp,:::6650->6650/tcp, 0.0.0.0:8080->8080/tcp,:::8080->8080/tcp
flink_taskmanager_1   /docker-entrypoint.sh task ...   Up             6123/tcp, 8081/tcp
```

All of the containers are linked together in the same docker network and can reference one another using their docker-compose names. This manz that your flink jobs should have a reference to pulsar by hitting `pulsar:8080` or `pulsar:6650`

After running `docker-compose up` you can navigate to `localhost:8081` and upload your very own flink jar !