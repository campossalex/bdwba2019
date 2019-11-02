Confifguraciones:

GetKafka 
ZooKeeper Connection String : 10.0.0.160:2181,10.0.0.137:2181,10.0.0.208:2181 
Topic Name : www.access_log  
Zookeeper Commit Frequency: 60 secs 
Batch Size: 1000 
Message Demarcator: \n 
Kafka Communications Timeout: 30 secs 
ZooKeeper Communications Timeout: 30 secs 

Extract Text 
Character Set: UTF-8 
access_log: ^(\S+) \S+ \S+ \[([^\[]+)\] "(\w+) (\S+) (\S+)" (\d+) (\d+) "([^"]+)" "([^"]+)".* 
ip_origen: ^(\S+) 
method: ^\S+ \S+ \S+ \[[^\[]+\] "(\w+) 
time_local: ^\S+ \S+ \S+ \[([^\[]+)\] 


ReplaceText 
Search Value: (?s)(^.*$) 
Replacement Value: ${access_log.1}|${access_log.2}|${access_log.3}|${access_log.4}|${access_log.5}|${access_log.6}|${access_log.7}|${access_log.8}|${access_log.9} 
Replacement Strategy: Regex Replace 
Evaluation Mode: Entire text 


ConvertCSVToAvro
Record schema: {
  "type": "record",
  "name": "AccesLogRecord",
  "fields" : [
  	{"name": "ip", "type": ["null", "string"]},
  	{"name": "time_local", "type": ["null", "string"]},
  	{"name": "method", "type": ["null", "string"]},
  	{"name": "uri", "type": ["null", "string"]},
  	{"name": "protocol", "type": ["null", "string"]},
  	{"name": "status", "type": ["null", "string"]},
  	{"name": "bytes_sent", "type": ["null", "string"]},
  	{"name": "referer", "type": ["null", "string"]},
  	{"name": "useragent", "type": ["null", "string"]}
  ]
}
CSV delimiter: |
Use CSV header line: false


PutParquetTrx
Hadoop Configuration Resources:/demo/core-site.xml,/demo/hdfs-site.xml
Record Reader:AvroReader
Directory:hdfs://ip-10-0-0-65.ec2.internal:8020/user/hive/warehouse/ecommerce.db/access_log
Compression Type: SNAPPY
Overwrite Files: false



Database Connection Pooling Service: DBCPConnectionPool
Table Name: Transaction
Maximum-value Columns : InvoiceNo
Output Empty FlowFile on Zero Results: false

Database Connection Pooling Service: Database Connection Pooling Service
Normalize Table/Column Names: false
Use Avro Logical Types: false
Compression Format: NONE
Default Decimal Precision: 10 
