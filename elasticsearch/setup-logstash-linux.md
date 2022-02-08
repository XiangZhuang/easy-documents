# Setup Logstash (Linux)

### 1. Download Package

- Download suitable Logstash package at https://www.elastic.co/cn/downloads/logstash and upload it to server through SFTP.
- Or, directly download the package at server using

    `wget https://artifacts.elastic.co/downloads/logstash/logstash-7.17.0-linux-x86_64.tar.gz`
- Unzip the package using

    `tar -xzf logstash-7.17.0-linux-x86_64.tar.gz`

- Launch Logstash
  - Start Logstash with no input and output configuration.

    ```
    cd logstash-7.17.0
    ./bin/logstash -e 'input { stdin { } } output { stdout {} }'
    ```
  - After pipeline starts, enter `hello world`, and you will get some feedback like
    ```
    {
       "message" => "hello world",
       "@timestamp" => 2022-02-08T15:19:54.481Z,
       "@version" => "1",
       "host" => "L2Y81HVHetrBy3ahiEAy"
    }
    ```
  - Nice! Logstash is running good!


## Synchronize with MySQL
### 1. Download Java MySQL Connector

- Java MySQL connector is required in the logstash configuration file providing driver class.
- Download Java MySQL connector at https://dev.mysql.com/downloads/connector/j/.

### 2. Configure `logstash.conf`

### Single Configuration File
```
input {
  jdbc {
    jdbc_driver_library => "<path>/mysql-connector-java-x.x.x.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://<host_ip>:3306/<db_name>"
    jdbc_user => <username>
    jdbc_password => <password>
    jdbc_paging_enabled => true
    tracking_column => "unix_ts_in_secs"
    use_column_value => true
    tracking_column_type => "numeric"
    schedule => "*/5 * * * * *"
    statement => "SELECT *, UNIX_TIMESTAMP(modification_time_field) AS unix_ts_in_secs FROM some_table WHERE (UNIX_TIMESTAMP(modification_time_field) > :sql_last_value AND modification_time_field < NOW()) ORDER BY modification_time_field ASC"
  }
}
filter {
  mutate {
    copy => { "id" => "[@metadata][_id]"}
    remove_field => ["id", "@version", "unix_ts_in_secs"]
  }
}
output {
  elasticsearch {
      index => "some_index"
      document_id => "%{[@metadata][_id]}"
  }
}
```

### Multiple Configuration Files
#### Approach 1
- Create multiple `.conf` files under one directory e.g. `/conf`.
- Inside each `.conf` add tags and conditional statements.
#### Approach 2
- Create multiple pipelines, each pipeline pointing to one specific `.conf` file.
