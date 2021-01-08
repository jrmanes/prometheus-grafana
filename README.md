# Contents

- Introduction
  - [Overview](#a-prometheus--grafana-docker-compose-stack)
  - [Pre-requisites](#pre-requisites)
  - [Installation & Configuration](#installation--configuration)
    - [Add Datasources & Dashboards](#add-datasources-and-dashboards)
  	- [Alerting](#alerting)
  	- [Test Alerts](#test-alerts)
    - [Add additional Datasources](#add-additional-datasources)

# A Prometheus & Grafana docker-compose stack

Infrastructure monitoring system with [Prometheus](http://prometheus.io/) stack containing Prometheus, Grafana and Node scraper to monitor your Docker infrastructure.

# Pre-requisites
Before we get started installing the Prometheus stack. Ensure you install the latest version of docker and docker-compose on your Docker host machine.

# Installation & Configuration
Clone the project locally to your Docker host and execute:
```bash
docker-compose up --build
```

If you would like to change which targets should be monitored or make configuration changes edit the yaml file prometheus.yml inside the prometheus folder or add a new one if you want to split the configuration in files, keep in mind that you will have to add it in the prometheus.yml


The Grafana Dashboard is now accessible via: `http://<Host IP Address>:3000` for example http://192.168.10.1:3000

	username - admin
	password - admin123. (Password is stored in the `/grafana/config.monitoring` env file)

## Add Datasources and Dashboards
Grafana version 5.0.0 has introduced the concept of provisioning. This allows us to automate the process of adding Datasources & Dashboards. The `/grafana/provisioning/` directory contains the `datasources` and `dashboards` directories. These directories contain YAML files which allow us to specify which datasource or dashboards should be installed. 

*If you would like to automate the installation of additional dashboards just copy the Dashboard JSON file to /grafana/provisioning/dashboards and it will be provisioned next time you stop and start Grafana.*

## Alerting
Alerting has been added to the stack with Slack integration, email and rocketchat. 2 

Alerts              - `prometheus/alert.rules`
Slack, Eamil, RocketChat configuration - `alertmanager/config.yml`

The Slack configuration requires to build a custom integration.
* Open your slack team in your browser `https://<your-slack-team>.slack.com/apps`
* Click build in the upper right corner
* Choose Incoming Web Hooks link under Send Messages
* Click on the "incoming webhook integration" link
* Select which channel
* Click on Add Incoming WebHooks integration
* Copy the Webhook URL into the `alertmanager/config.yml` URL section
* Fill in Slack username and channel

View Prometheus alerts `http://<Host IP Address>:9090/alerts`
View Alert Manager `http://<Host IP Address>:9093`

### Test Alerts
A quick test for your alerts is to stop a service. Stop the node_exporter container and you should notice shortly the alert arrive in Slack. Also check the alerts in both the Alert Manager and Prometheus Alerts just to understand how they flow through the system.

High load test alert: 
```bash
docker run --rm -it busybox sh -c "while true; do :; done"
```

Let this run for a few minutes and you will notice the load alert appear. Then Ctrl+C to stop this container.

### Add Additional Datasources
In order to create the Prometheus Datasource in order to connect Grafana to Prometheus 
* Click the `Grafana` Menu at the top left corner (looks like a fireball)
* Click `Data Sources`
* Click the green button `Add Data Source`.

---
Jose Ramón Mañes