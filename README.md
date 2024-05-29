
# Security Log Analysis Tool 🕵️

## Overview
This project is a Security Log Analysis Tool that collects, processes, and analyzes security logs, sending the data to Elasticsearch and visualizing it in Kibana. This tool is ideal for monitoring and analyzing security events in real-time, providing valuable insights into system activities.

## Features
- Collect and parse security logs.
- Store logs in Elasticsearch for efficient querying and analysis.
- Visualize log data in Kibana with customizable dashboards.
- Real-time monitoring of security events.

## Requirements
- Ubuntu 24.04 LTS
- Python 3
- Elasticsearch 7.x
- Logstash 7.x
- Kibana 7.x

## Installation

### Step 1: Update the System
```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2: Install Dependencies
```bash
sudo apt install -y python3 python3-pip python3-venv
```

### Step 3: Install and Configure Elasticsearch, Logstash, and Kibana
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo sh -c 'echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" > /etc/apt/sources.list.d/elastic-7.x.list'
sudo apt update
sudo apt install -y elasticsearch logstash kibana

# Start and enable Elasticsearch and Kibana
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
sudo nano /etc/kibana/kibana.yml
# Uncomment and configure the line:
elasticsearch.hosts: ["http://localhost:9200"]
sudo systemctl start kibana
sudo systemctl enable kibana
```

### Step 4: Set Up Python Virtual Environment
```bash
mkdir ~/log_analyzer
cd ~/log_analyzer
python3 -m venv log_analyzer_env
source log_analyzer_env/bin/activate
pip install elasticsearch
```

### Step 5: Create and Configure Sample Log File
```bash
nano ~/log_analyzer/example.log
```
Add the content:
```plaintext
2024-05-28 12:00:00 INFO User logged in
2024-05-28 12:05:00 ERROR Failed login attempt
2024-05-28 12:10:00 INFO User logged out
2024-05-28 12:15:00 INFO User logged in
2024-05-28 12:20:00 WARNING Disk space low
2024-05-28 12:25:00 INFO User uploaded a file
```

### Step 6: Create Logstash Configuration File
```bash
nano ~/log_analyzer/logstash.conf
```
Add the content:
```conf
input {
  file {
    path => "/home/your_username/log_analyzer/example.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
}
```

### Step 7: Test and Move Logstash Configuration File
```bash
sudo mv ~/log_analyzer/logstash.conf /etc/logstash/conf.d/
sudo /usr/share/logstash/bin/logstash -t -f /etc/logstash/conf.d/logstash.conf
sudo systemctl restart logstash
sudo systemctl status logstash
sudo journalctl -u logstash -f
```

### Step 8: Configure Kibana and Explore Data
1. Open Kibana in your web browser:
   ```plaintext
   http://localhost:5601
   ```

2. Create an Index in Kibana:
   - In the side menu, click "Management".
   - Under "Management", select "Stack Management".
   - Under "Kibana", click "Index Patterns".
   - Click "Create index pattern".
   - In the "Index pattern" field, enter `logs-*`.
   - Click "Next step".
   - Select `@timestamp` as the time field (if prompted).
   - Click "Create index pattern".

3. Explore Data in Kibana:
   - In the side menu, click "Discover".
   - Select the `logs-*` index pattern.
   - Explore the logs processed by Logstash.

4. Create Visualizations and Dashboards in Kibana:
   - In the side menu, click "Visualize".
   - Click "Create new visualization".
   - Choose a visualization type (e.g., bar chart, line chart).
   - Select the `logs-*` index pattern.
   - Configure the visualization parameters (axes, filters, etc.).
   - Save the visualization.

   - In the side menu, click "Dashboard".
   - Click "Create new dashboard".
   - Add the visualizations you created.
   - Save the dashboard.

## License
This project is licensed under the MIT License.
