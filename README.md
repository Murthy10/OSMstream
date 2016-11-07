# OSMstream
The repository provides a docker container which collects the Augmented diffs (change sets) from OpenStreetMap.
The original "diffs" are provided in XML format and are accessed over the overpass API. For use this codes converts theses diffs into JSON format and publishes them using Apache Kafka.
After that the data is ready for a stream based consumer of your choice.

![Overview](img/streaming_db.png)

##Installation
Pull the repository, build the docker container and run the container.

###Docker
```shell
cd OSMstream
docker run -d --name osmstream -p 2181:2181 geometalab/osmstream
```

If you'd like to verify if there is something going on, use the following command.
```shell
docker exec -it osmstream bash
/opt/kafka_2.11-0.10.0.1/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic osm --from-beginning
```


##Usage
The idea is now to test your streaming database with the OSM diff data provided by Kafka.

###Kafka settings
- Host:     localhost
- Port:     2181
- Topic:    osm, benchmark

###Benchmak
Benchmarking is a very difficult topic and strongly depends on various parameters like the underlying hardware.
So we decide to make this as hardware independent as possible. The idea is to produce Kafka messages with "a lot of text" and for the consumer the goal is to count the words of the text and track the used processing time.
The we repeat this process with the double text length and so on. After a few iteration you can relate the processing time and the text length.

Start the message producer: 
```shell
docker exec -it osmstream bash
python3 /opt/OSMstream/benchmark.py -r 10
```
(the parameter -r is the number of produced messages)

##Links
- http://wiki.openstreetmap.org/wiki/Overpass_API/Augmented_Diffs
- http://wiki.openstreetmap.org/wiki/Planet.osm/diffs
- http://wiki.openstreetmap.org/wiki/OsmChange

## Development

Update and develop the new version, when satisfied, push to Dockerhub:

```
docker build -t geometalab/osmstream .
docker push geometalab/osmstream
```

To tag a specific version, for example having a clustered version of the image:

```
docker build -t geometalab/osmstream:clustered .
docker push geometalab/osmstream:clustered
```
