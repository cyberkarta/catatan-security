# Elastic Stack
Source: Threat Hunting with Elastic Stack
![](attachments/Pasted%20image%2020220922120826.png)
Youtube: https://bit.ly/3wHU6a7
Github: https://github.com/PacktPublishing/Threat-Hunting-with-Elastic-Stack/tree/main/chapter_3_introduction_to_the_elastic_stack

Download elasticsearch: https://www.elastic.co/downloads/elasticsearch
Elasticsearch training: https://www.elastic.co/training/?ultron=B-Stack-Trials-APJ-Exact&gambit=Stack-training&blade=adwords-s&hulk=paid&Device=c&thor=elasticsearch%20training&gclid=CjwKCAjwvsqZBhAlEiwAqAHElbma9b0pqewDNOT9NYDGvvC3Cl1CKwHtHtf8QNkE5p2roiL1BgKnXxoCNgwQAvD_BwE

## Logstash
Elastic product built on Java that provide multiple pipelines to move data into Elasticsearch via input and output plugins. It uses various inputs to collect data, enrich it along the way, and send it tinto Elasticsearch.

### Input plugins
Allow specific datasets to be consumed and processed by Logstash. Common plugins for Logstash:
- Syslog plugin (reads syslog events over the network)
- Kafka plugin
- SNMP plugin (polls network devices using SNMP)

### Filter plugins
Apply filters to data as it travels through the pipeline. Common filter plugins:
- CSV plugin (CSV parser)
- JSON plugin (JSON parser)
- GeoIP plugin (looks up the geographic information of IP addresses)

### Output plugins
Where the data sent once it has completed the pipeline. Can just as easily be other platforms
- Kafka
- MongoDB
- Redis
- S3

Logstash has tremendous value for high-throughput networks that have the need to perform enrichments and specific pipelines for different data types. It does several limitations:
- Requires additional infrastructure for Logstash service
- Requires an existing log collection solution.
- It has a learning curve for the different plugins and filters.


## Elasticsearch
The core of the entire stack. It is a search platform built on Lucene library and developed in Java. Elasticsearch hosts a common JSON over HTTP interface that allows Elasticsearch to act as the search tier for an application frotnend via API. This is a welcome banner and it is displayed when querying Elasticsearch API without any additional parameter.
```json
{
"name" : "packtpub.lan",
"cluster_name" : "cluster",
"cluster_uuid" : "GcrS1m99QIWPjkgT9SKnuA",
"version" : {
"number" : "7.10.2",
"build_flavor" : "default",
"build_type" : "tar",
"build_hash" : "747e1cc71def077253878a59143c1f785afa92b9",
"build_date" : "2021-01-13T00:42:12.435326Z",
"build_snapshot" : false,
"lucene_version" : "8.7.0",
"minimum_wire_compatibility_version" : "6.8.0",
"minimum_index_compatibility_version" : "6.0.0-beta1"
},
"tagline" : "You Know, for Search"
}
```

### Installing Elasticsearch
- Download Elasticsearch: https://www.elastic.co/downloads/elasticsearch
- Edit `elasticsearch.yml` with `sudo vim /etc/elasticsearch/elasticsearch.yml`
	- Add `network.host: 0.0.0.0`
	- Add `discovery.type: single-node`
	- Add `discovery.seed_hosts: ["0.0.0.0"]`
- Fire it up `sudo systemctl start elasticsearch.service`
- Query the HTTP API for Elasticsearch `curl localhost:9200?pretty`
- Create the first index `curl -X PUT "localhost:9200/hello-world-index?pretty"`

Checking Elasticsarch's health
- Checking the first index that was created `curl localhost:9200/_cat/indices`
- Adding some headers `curl localhost:9200/_cat/indices?v=true&pretty`
	- health: yellow, because we only have one replica shard and it is not ideal for production
	- status: open/closed
	- index: name of the index
	- uuid
	- pri: how many primary shards are there
	- rep: how many replica shards are there
	- docs.count: how many documents are there.
	- docs.deleted: how many documents have been deleted
	- store.size: compressed storage size taken by primary and replica shards
	- pri.store.size: the compressed storage size taken only by primary shards.
- You can try these methods in documentation (querying data with API and `search_` endpoint): https://www.elastic.co/guide/en/elasticsearch/reference/current/search-your-data.html

## Beats and Agents
Beats are data shippers that you can install directly on an endpoint to send data through Logstash, other pipelines, and Elasticsearch. Beats contains huge security value. For example:
- WInlogbeat: Collect Windows event logs.
- Packetbeat: Collect network traffic from an endpoint.
- Elastic Agent: framework to wrap all of these Beats together, add functionality, and provide the ability to centrally control the agent configurations with Kibana app called Fleet.

### Filebeat
Ship files into Logstash or directly into Elasticsearch. It uses modules that are preconfigured to ship specific types of logs in a standardized schema that either aligns with Elastic Common Schema (ECS) or uses the same design concepts if the specific ECS fieldset doesn't exist. List of Filebeat modules: https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-modules.html.

Filebeat instance -> send data into Elasticsearch
- Download file beat: https://www.elastic.co/downloads/beats/filebeat
- `vim filebeat.yml`
	- Uncomment proper line for your OS
	- Change the log input type to `enabled: true`

```sh
filebeat.inputs:
# Each - is an input. Most options can be set at the
input level, so
# you can use different inputs for various
configurations.
# Below are the input specific configurations.
- type: log

# Change to true to enable this input configuration.
enabled: true
# Paths that should be crawled and fetched. Glob based
paths.
paths:
- /var/log/*.log
#- c:\programdata\elasticsearch\logs\*
```
- On that file we will be sending files that end in `*.log` inside `/var/log` directory to Elasticsearch.
- Make sure Elasticsearch is up and ready to accept data. `curl loclahost:9200?pretty`
- Test Filebeat and ensure that it's able to send data and properly configured. `filebeat test output`
- Fire up Filebeat and ingest those logs: `./filebeat`
- If we go back and check Elasticsearch like the way we did for Logstash data, we'll see that there is a new index called filebeat-{version}-{date}-{index iteration}

### Packetbeat
Focused on collecting network metadata. The goal of Packetbeat is more focused around application type network traffic. In contrast with something such as Zeek or Suricata do. The meta data collected:
- Source and destination IP
- Network Protocols
- You won't see any specific payloads as you may see in full packet capture

Packet beat can provides metadata of:
- ICMP
- DHCP
- DNS
- HTTP
- AMQP
- Cassandra
- MySQL
- PostgreSQL
- Redis
- Thrift RPC
- MongoDB
- Memcache
- NFS
- TLS
- SIP/SDP

DNS, HTTP, and TLS provides solid value for threat hunting.

Installing Packetbeat
- Download packet beat https://www.elastic.co/downloads/beats/packetbeat
- In Windows, you have to download Npcap https://nmap.org/npcap/dist/npcap-1.10.exe
- Identify what device to capture on. You can use Packetbeat binary to identify this `./packetbeat devices`
- `vim packetbeat.yml` select the network interfaceto sniff the data, on linux we can use "any" to sniff on all connected interfaces.
	- `packetbeat.interface.device: en0`
- Test configuration `./packetbeat test output` dan `./packetbeat test output`

Ingesting previously collected PCAP
- Simulate with tcpdump.
  `tcpdump -i en0 -s 65535 -w local-capture.pcap`
- Replay packet capture into Elasticsearch and will automatically quit once it reaches the end of the PCAP.
  `./packetbeat run -I local-capture.pcap`
It can be used for incident response training. Deploying a proper Network Security Monitoring (NSM) solution is going to give you

(page 61)