# EFK (Elastic Search + Filebeat + Kibana)
Project for studying EFK + Docker + logback-appender

## Example project (SpringBoot + Docker)
### Building infrastructure
1. Clone project [SpringBoot project with logback-appender + Docker](https://github.com/augustomarinho/springboot-fluentd-appender)
2. Building application and docker image: ```mvn clean install docker:build```
3. Building docker container: ```docker run -d --name springboot-app --label elastic_index=springboot --label send.logs=true -p 8002:8080 augustomarinho/springboot-fluentd-appender```
#### Testing App
1. Calling endpoint for testing: ```curl -X GET http://localhost:8002/query/cpf/12345678909 -v```
2. Verify logs as a json format: docker logs springboot-app
3. Check output format (must be similar to below example)
```json
{"@timestamp":"2019-04-13T18:38:51.525+00:00","@version":1,"message":"Recendo requisicao para CPF 12345678909","logger_name":"com.am.study.application.controllers.ControllerExample","thread_name":"http-nio-8080-exec-5","level":"INFO","level_value":20000,"CPF":"12345678909"}
```
### Running Elastic Search and Kibana
1. [Downloading docker-compose file](https://github.com/augustomarinho/efk/blob/master/devops/elasticsearch/docker-compose.yml) - Credits for docker-compose file: https://alysivji.github.io/elasticsearch-kibana-with-docker-compose.html
2. Execute on terminal this command: sudo sysctl -w vm.max_map_count=262144 - [more details about it:](https://www.elastic.co/guide/en/elasticsearch/reference/current/_maximum_map_count_check.html)
3. Running Elastich Search and Kibana: docker-compose -f docker-compose.yml  up -d --force-recreate
4. Link Kibana: http://localhost:5601 | Link Elasticsearch: http://localhost:8100

### Running Filebeat
1. ```docker pull docker.elastic.co/beats/filebeat:7.0.0```
2. Download file filebeat.yml from https://github.com/augustomarinho/efk/blob/master/devops/filebeat/filebeat.yml
3. Copy filebeat.yml to /filebeat directory
4. Creating a filebeat directory: mkdir /filebeat
5. docker run -d \
  --name=filebeat \
  --user=root \
  --volume="/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  --volume="/var/log:/var/log:ro" \
  --volume="/filebeat/data:/usr/share/filebeat/data:rw" \
  docker.elastic.co/beats/filebeat:7.0.0 filebeat -e -strict.perms=false -E beat.name=\`hostname -I | cut -d' ' -f1\` -E output.elasticsearch.hosts=["\`hostname -I | cut -d' ' -f1\`:9200"]
