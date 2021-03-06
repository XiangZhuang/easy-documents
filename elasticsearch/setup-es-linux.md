# ElasticSearch Setup (Linux)

### 1. Preparation

Use a non-root user, here I use `es` with user folder at `/home/es`

### 2. Download ElasticSearch Archive under user folder
```
cd /home/es
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.0-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.17.0-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.17.0-linux-x86_64.tar.gz
```

### 3. Launch ElasticSearch
```
cd elasticsearch-7.17.0/bin
elasticsearch -d
```

### 4. Check ElasticSearch Status
```
ps -ef | grep elasticsearch
```

### 5. Setup Security
Security is disabled by default.

Edit `config/elasticsearch.yml`
- Add `discovery.type: single-node` if running ES on only one instance
- Add `xpack.security.enabled: true` to enable security
- After restarting ES, launch a new terminal
  - run `./bin/elasticsearch-setup-passwords interactive` to set up the password for built-in users

<br/>

### Memory Consumption

ElasticSearch by default consumes 50% of memory.

To set memory consumption manually:
```
cd config/jvm.options.d
touch <some_name>.options
```
Inside `<some_name>.options`, add for example:
```
-Xms1g
-Xmx1g
```

<br/>

### Common Problems

#### 1. Cannot find java jdk
- Explicitly set the JAVA_HOME `export JAVA_HOME=<path_to_java>`

#### 2. Can not run elasticsearch as root
- Create a user for elasticsearch if there is no such user
- Use that user to launch elasticsearch

#### 3. `[Elasticsearch: Could not find or load main class org.elasticsearch.tools.launchers.JavaVersionChecker]`
- Do not have the elasticsearch package under root folder
- In my case, I have it downloaded under `/home/es`
