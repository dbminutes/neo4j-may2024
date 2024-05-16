Setting up and using Grafana for alerts and alarms for Neo4j involves several steps, including installing the necessary tools, configuring data sources, and creating dashboards and alerts. 

### Step 1: Install Necessary Tools

#### 1.1 Install Prometheus
Prometheus is needed to scrape metrics from Neo4j and store them.

```bash
# Download and extract Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.31.1/prometheus-2.31.1.linux-amd64.tar.gz
tar xvfz prometheus-*.tar.gz
cd prometheus-*

# Start Prometheus
./prometheus --config.file=prometheus.yml
```

#### 1.2 Install Neo4j Prometheus Plugin
Download and install the Neo4j Prometheus plugin to expose metrics.

```bash
# Download the plugin
wget https://github.com/neo4j-contrib/neo4j-prometheus/releases/download/0.5.0/neo4j-prometheus-plugin-0.5.0.zip

# Move the plugin to the plugins directory of your Neo4j installation
unzip neo4j-prometheus-plugin-0.5.0.zip -d /path/to/neo4j/plugins
```

Edit your Neo4j `conf/neo4j.conf` file to enable the Prometheus endpoint:

```properties
dbms.unmanaged_extension_classes=org.neo4j.metrics.prometheus.prometheus/metrics
```

Restart Neo4j to apply changes:

```bash
neo4j restart
```

#### 1.3 Install Grafana
Grafana will be used to visualize the metrics and set up alerts.

```bash
# Add Grafana GPG key and repository
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

# Install Grafana
sudo apt update
sudo apt install grafana

# Start Grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### Step 2: Configure Prometheus to Scrape Neo4j Metrics

Edit the `prometheus.yml` file in your Prometheus directory to add the Neo4j target:

```yaml
scrape_configs:
  - job_name: 'neo4j'
    static_configs:
      - targets: ['localhost:2004'] # Neo4j Prometheus endpoint
```

Restart Prometheus:

```bash
./prometheus --config.file=prometheus.yml
```

### Step 3: Configure Grafana

#### 3.1 Add Prometheus as a Data Source
1. Open Grafana (default port: http://localhost:3000).
2. Log in (default credentials: admin/admin).
3. Go to **Configuration** > **Data Sources** > **Add data source**.
4. Select **Prometheus** and configure it with the following settings:
   - **URL**: `http://localhost:9090` (Prometheus endpoint)
   - Click **Save & Test** to verify the connection.

#### 3.2 Create Dashboards and Panels
1. Go to **Create** > **Dashboard**.
2. Click **Add new panel**.
3. Select metrics to visualize Neo4j data (e.g., `neo4j_page_cache_hit_ratio`).

#### 3.3 Set Up Alerts
1. In the Grafana panel, click on the panel title and select **Edit**.
2. Go to the **Alert** tab.
3. Click **Create Alert** and configure the alert rule:
   - **Name**: Give a name to your alert.
   - **Conditions**: Set conditions based on your metrics (e.g., if `neo4j_page_cache_hit_ratio` falls below 0.8).
   - **Notifications**: Configure where to send the alerts (email, Slack, etc.).
4. Click **Save** to apply the alert configuration.

### Step 4: Configure Notification Channels
1. Go to **Alerting** > **Notification channels** > **Add channel**.
2. Select and configure the type of notification channel (email, Slack, etc.).
3. Click **Save** to save the notification channel.

### Step 5: Testing Alerts
1. Ensure the metrics you are monitoring are actively being scraped by Prometheus.
2. Trigger an alert condition by manipulating the Neo4j environment (e.g., reducing available memory to affect cache hit ratio).
3. Verify that Grafana sends alerts to the configured notification channels.

### Best Practices
- **Regularly Review Alerts**: Ensure that alerts are still relevant and accurately set.
- **Tune Alert Thresholds**: Adjust alert thresholds based on the normal behavior of your Neo4j instance.
- **Use Dashboards**: Regularly monitor dashboards for insights and trends.

------------------

**Some of these urls may be outdated, please refer to latest version of softwares from their official website. We highly recommend using docker containers for setting all these tools**