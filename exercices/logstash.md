# logstash

This part will dive into logstash and presents the main input, output and filtering elements through simple exercices.

#### Files location

This section simply highlights the different directories to find all the logstash instances and files.

Logstash directory
> /usr/share/logstash

Logstash pipeline directories
> /usr/share/logstash/pipeline

Logstash configuration directory (logstash.yml)
> /usr/share/logstash/config/


#### Launch logstash applications

For testing if Logstash is correctly installed:
> bin/logstash -e 'input { stdin { } } output { stdout {} }'

Launch a logstash conf file
> /usr/share/logstash/bin/logstash -f pipeline.conf

Logstash config:

```
 --config.test_and_exit      => Check the config file passed to logstash
 --config.reload.automatic   => automatically reload configs without a restart of logstash
```

#### Launch multiple logstash pipelines -- add different data destinations

> /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/your_pipeline.conf --path.data /your_directory_to_store_queues

### Plugins

Logstash has a collections of filter, input, output plugins
Here the command to list or install plugins:
```
bin/logstash-plugin list
bin/logstash-plugin install pluginName
```

The different plugins can be found via:
> https://www.elastic.co/guide/en/logstash/current/index.html

### Getting started
For launching a logstash instance, a pipelineName.conf needs to be defined with the input listener and the output destination. Optionally, a filtering section can be added for performing intermediary processing.

By default, Logstash is running on **port 5044**.

## 1.1 => Install the input and output plugins for your logstash instance.

In order to connect to your logstash instance:
```
docker ps
docker exec -it -u root logstash-container-id bash
```


### Create and launch pipeline in debug mode
In a debug-logstash.conf, write the following config:

```
input { stdin { } }
output { stdout {} }
```

Launch the pipeline:
```
/opt/logstash/bin/logstash --path.data /tmp/logstash/data -f pipeline/debug_logstash.conf
```

### HTTP input logging

In a second conf file, use an http listenner input in order to send a json thanks to the following curl :

```
curl -H "content-type: application/json" -XPUT 'http://0.0.0.0:8080/test/1' -d '{ "key1": "val1"}'
```

### Filtering

- check the data3.log file
- ingest data from a logstash-exo.log file thanks to the file input plugin
- use the grok filter plugin to look for patterns in the incoming data (https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html). Use the %{COMBINEDAPACHELOG} grok pattern.
- use the date (for matching the timestamp) and geoip (source clusterip) to add information on the data

### Output

Add a second output on the previous pipeline in order to send data to elasticsearch (https://www.elastic.co/guide/en/logstash/current/plugins-outputs-elasticsearch.html). Do not forget to set up the user and password.
