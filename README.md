# EFK (Elastic Search + Filebeat + Kibana)
Project for studying EFK + Docker + logback-appender

## Example project (SpringBoot + Docker)

1. Clone project [SpringBoot project with logback-appender + Docker](https://github.com/augustomarinho/springboot-fluentd-appender)
2. mvn clean install docker:build
3. docker run -d --name springboot-app --label elastic_index=springboot --label send.logs=true -p 8002:8080 augustomarinho/springboot-fluentd-appender

#### Testing App
1. Call endpoint for testing: curl -X GET http://localhost:8002/query/cpf/12345678909 -v
2. Verify logs as a json format: docker logs springboot-app
3. Check output format (must be similar to below example)
```json
{"@timestamp":"2019-04-13T18:38:51.525+00:00","@version":1,"message":"Recendo requisicao para CPF 12345678909","logger_name":"com.am.study.application.controllers.ControllerExample","thread_name":"http-nio-8080-exec-5","level":"INFO","level_value":20000,"CPF":"12345678909"}
```
