```azure
测试有效


```
logstash 的pipline.conf文件
```azure
input {
	beats {
		port => 5044
	}
	tcp {
		port => 5000
	}
}

output {
	elasticsearch {
		hosts => "elasticsearch:9200"
		user => "elastic"
		password => "minong"
		index => "%{[@metadata][-wms]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
	}
}

```
filebeats配置说明(依赖logstash)
```azure
filebeat.inputs:
  - type: log
    enabled: true
    # 开启json解析
    json.keys_under_root: true
    json.add_error_key: true
    # 日志文件路径
    paths:
      - /usr/share/filebeat/logs/*.log

setup.template.settings:
  index.number_of_shards: 1
filebeat.config:
  modules:
    path: ${path.config}/modules.d/*.yml
    reload.enabled: false

# ================================= Processors =================================
processors:
  - decode_json_fields:
      fields: ['@timestamp','level','content','trace','span','duration']
      target: ""
output.logstash:
  hosts: ["logstash:5044"]
```
