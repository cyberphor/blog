
```
# Download Elastic PGP key on SIEM
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# Download dependencies on SIEM
sudo apt install apt-transport-https

# Add Elastic repo on SIEM
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | \
sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

# Sync Elastic repo on SIEM
sudo apt update

# Install the Elastic Stack on SIEM
sudo apt install elasticsearch logstash kibana

# Configure Elasticsearch on SIEM
cluster.name: sky.net
node.name: siem1.sky.net
network.host: 192.168.2.48
discovery.type: single-node

# Configure Logstash on SIEM
node.name: siem1.sky.net
http.host: '192.168.2.48'

# Configure Kibana on SIEM
elasticsearch.url: 'http://192.168.2.48:9200'
server.name: siem1.sky.net
server.host: '192.168.2.48'
server.port: 443
logging.dest: /var/log/kibana.log

# Download/Configure Winbeat on Windows Event Collector
INSERT CODE HERE

# Download/Configure Auditbeat on Linux Web Server
INSERT CODE HERE
```
