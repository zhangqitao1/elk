安装 使用docker-compose
======================

```
#docker-compose.yml
#docker-compose up -d

version: '3'
services:

  elasticsearch:
    image: elastic/elasticsearch:7.17.3
    environment: 
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
      - discovery.type=single-node 
    ports:
      - "9200:9200"
      - "9300:9300"


    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
  kibana:
    image: elastic/kibana:7.17.3
    ports:
      - "5601:5601"
      
      
es地址：http://127.0.0.1:9200/    
kibana：http://localhost:5601/
```

添加数据
==============
```
curl --location --request PUT 'http://127.0.0.1:9200/events/aa/02' \
--header 'Content-Type: application/json' \
--data-raw ' {
            "appid" : "1189032567",
            "event" : "sdk_udpate_channel",
            "uuid" : "10273881",
            "udid" : "b3b741fc46a25116",
            "server_id" : "4",
            "os" : "android",
            "version" : "11",
            "screen" : "720_1600",
            "channel" : "Unattributed",
            "subchannel" : "RF_EU_AND_OBT3_AAA_CEO_LV5_20220401-0419-0430",
            "subchannel_" : "AAA-CEO-LV5",
            "subchannel__" : "3627013e7cfae84157008fe4fbca5025",
            
            "ip" : "83.185.35.26",
            "params" : 
            {
                    "time" : "1651377392",
                    "sourde" : "adjust_callback",
                    "sdk_version" : "7.10.0",
                    "game_version" : "1.2.5"
            },

            "status" :  
                {
                    "roleid" : "10273881"
                }

        }'
```

查询数据
========
```
curl --location --request GET 'http://127.0.0.1:9200/events/_search'
```


sql 查询
=========
```
docker-compose exec elasticsearch /usr/share/elasticsearch/bin/elasticsearch-sql-cli

sql> show tables;
    catalog    |             name              |     type      |     kind
---------------+-------------------------------+---------------+---------------
docker-cluster |.apm-agent-configuration       |TABLE          |INDEX
docker-cluster |.apm-custom-link               |TABLE          |INDEX
docker-cluster |.async-search                  |TABLE          |INDEX
docker-cluster |.kibana                        |VIEW           |ALIAS
docker-cluster |.kibana_7.17.3                 |VIEW           |ALIAS
docker-cluster |.kibana_7.17.3_001             |TABLE          |INDEX
docker-cluster |.kibana_task_manager           |VIEW           |ALIAS
docker-cluster |.kibana_task_manager_7.17.3    |VIEW           |ALIAS
docker-cluster |.kibana_task_manager_7.17.3_001|TABLE          |INDEX
docker-cluster |events                         |TABLE          |INDEX
docker-cluster |kibana_sample_data_logs        |TABLE          |INDEX
docker-cluster |test                           |TABLE          |INDEX

sql> select * from events;
     appid     |    channel    |      event       |      ip       |      os       |params.game_version|params.sdk_version| params.sourde |  params.time  |    screen     |   server_id   | status.roleid |                 subchannel                  |  subchannel_  |          subchannel__          |      udid      |     uuid      |    version
---------------+---------------+------------------+---------------+---------------+-------------------+------------------+---------------+---------------+---------------+---------------+---------------+---------------------------------------------+---------------+--------------------------------+----------------+---------------+---------------
1189032567     |Unattributed   |sdk_udpate_channel|83.185.35.26   |android        |1.2.5              |7.10.0            |adjust_callback|1651377392     |720_1600       |4              |10273881       |RF_EU_AND_OBT3_AAA_CEO_LV5_20220401-0419-0430|AAA-CEO-LV5    |3627013e7cfae84157008fe4fbca5025|b3b741fc46a25116|10273881       |11
1189032567     |Unattributed   |sdk_udpate_channel|83.185.35.26   |android        |1.2.5              |7.10.0            |adjust_callback|1651377392     |720_1600       |4              |10273881       |RF_EU_AND_OBT3_AAA_CEO_LV5_20220401-0419-0430|AAA-CEO-LV5    |3627013e7cfae84157008fe4fbca5025|b3b741fc46a25116|10273881       |11

sql>
```
