# CRUD APP/ MonitoringServer_GP - Monitoring Stack[Prometheus, Alertmanager, Blackbox Exporter, and Grafana]

This repository contains a comprehensive monitoring stack for your applications and infrastructure, built using **Prometheus**, **Alertmanager**, **Blackbox Exporter**, and **Grafana**, all orchestrated with **Docker Compose**.

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup and Installation](#setup-and-installation)
- [Configuration Details](#configuration-details)
  - [Prometheus (`prometheus.yml`)](#prometheus-prometheusyml)
  - [Alert Rules (`alert.rules.yml`)](#alert-rules-alertrulesyml)
  - [Blackbox Exporter (`blackbox.yml`)](#blackbox-exporter-blackboxyml)
  - [Alertmanager (`alertmanager.yml`)](#alertmanager-alertmanageryml)
- [Accessing the Monitoring Stack](#accessing-the-monitoring-stack)
- [Monitoring an External Application](#monitoring-an-external-application)
- [Troubleshooting](#troubleshooting)

## Overview

This project provides a robust solution for:
- Collecting metrics from your servers (e.g., CPU, memory, disk usage) using Node Exporter.
- Monitoring the uptime and availability of your HTTP-based applications (e.g., your CRUD app) using Blackbox Exporter.
- Storing and querying these metrics using Prometheus.
- Defining and evaluating alerting rules based on collected metrics.
- Sending alert notifications (e.g., via email) using Alertmanager.
- Visualizing your metrics and dashboards using Grafana.

## Project Structure
```bash
├── monitoring-stack/
│   ├── alertmanager/
│   │   └── alertmanager.yml         # Alertmanager configuration for routing and notifications.
│   ├── blackbox/
│   │   └── blackbox.yml             # Blackbox Exporter configuration defining probe modules.
│   ├── docker-compose.yml           # Defines and orchestrates all monitoring services.
│   └── prometheus/
│       ├── alert.rules.yml          # Prometheus alert rules for various conditions.
│       └── prometheus.yml           # Main Prometheus configuration for scraping targets and alerting.
```

## Features

* **Prometheus:** Powerful time-series database and alerting engine.
* **Node Exporter Integration:** Monitor host-level metrics (CPU, Memory, Disk) of your Linux/Windows servers.
* **Blackbox Exporter Integration:** Probe HTTP/HTTPS, TCP, ICMP, DNS endpoints to check uptime and latency of your applications.
* **Alertmanager:** Handles deduplicating, grouping, and routing of alerts to the correct receiver.
* **Grafana:** Comprehensive dashboarding and visualization tool.
* **Containerized Environment:** All components run as Docker containers for easy deployment and management.
* **Persistent Data:** Uses Docker volumes to ensure your Prometheus, Grafana, and Alertmanager data persists across container restarts.
* **Email Alerting:** Configured to send email notifications for critical alerts.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

* [**Docker**](https://docs.docker.com/get-docker/): Latest stable version.
* [**Docker Compose**](https://docs.docker.com/compose/install/): Latest stable version.

## Setup and Installation

1.  **Clone the Repository:**
    ```bash
    git clone [https://github.com/your-username/your-repo-name.git](https://github.com/your-username/your-repo-name.git)
    cd your-repo-name/monitoring-stack
    ```

2.  **Configure Your Files:**
    * **Prometheus (`prometheus/prometheus.yml`):**
        * Update the `node_exporter` target with the actual IP and port of your Node Exporter instance (`18.208.141.241:9100` is currently configured).
        * Update the `blackbox_app_http` target to reflect the exact URL of your application that you want to monitor (e.g., `http://18.208.141.241:8000/docs`).
    * **Alertmanager (`alertmanager/alertmanager.yml`):**
        * Update the SMTP configuration (`smtp_smarthost`, `smtp_from`, `smtp_auth_username`, `smtp_auth_password`) with your email provider's details.
        * Change `to: 'accessworldtec@gmail.com'` to your desired recipient email address.
    * **Alert Rules (`prometheus/alert.rules.yml`):** Review and adjust alert thresholds (`for`, `expr` values) as needed for your environment.

3.  **Start the Monitoring Stack:**
    Navigate to the `monitoring-stack` directory (where `docker-compose.yml` is located) and run:
    ```bash
    docker-compose up -d
    ```
    This command will build (if necessary), create, and start all services in detached mode.

4.  **Verify Services:**
    You can check the status of your running containers:
    ```bash
    docker-compose ps
    ```
    All services should be `Up`.

## Configuration Details

### Prometheus (`prometheus/prometheus.yml`)

This file defines which targets Prometheus scrapes and where it sends alerts.
- Scrapes its own metrics.
- Scrapes `node_exporter` on `18.208.141.241:9100` for host metrics.
- Scrapes `blackbox_exporter` (running within the Docker network) which then probes `http://18.208.141.241:8000/docs` to check your application's HTTP availability.
- Configured to send alerts to `alertmanager` service within the `monitoring_net`.

### Alert Rules (`prometheus/alert.rules.yml`)

Contains definitions for various alerts:
- **Node Exporter Alerts:**
    - `HighCpuUsage`: Triggers if CPU usage exceeds 85% for 1 minute.
    - `HighMemoryUsage`: Triggers if memory usage exceeds 85% for 1 minute.
    - `HighDiskUsage`: Triggers if disk usage exceeds 90% for 1 minute on any non-tmpfs filesystem.
    - `NodeExporterDown`: Triggers if Node Exporter is unreachable for 1 minute.
- **Application Uptime Alerts:**
    - `ApplicationDown`: Triggers if the Blackbox Exporter probe to your application fails for 1 minute (e.g., application is down or returns non-2xx status).

### Blackbox Exporter (`blackbox/blackbox.yml`)

Defines the probing modules. Currently configured with:
- `http_2xx`: An HTTP prober that expects a `2xx` status code. This is used by Prometheus to check the health of your application's HTTP endpoint.

### Alertmanager (`alertmanager/alertmanager.yml`)

Manages alerts received from Prometheus.
- Configured to use SMTP for email notifications.
- All alerts are routed to the `default-receiver` which sends emails to `accessworldtec@gmail.com`.
- Alerts are grouped by `alertname` and `instance` to reduce notification spam.
- Includes `group_wait`, `group_interval`, and `repeat_interval` to fine-tune alert notification timing.
- Sends "resolved" notifications when an alert condition clears.

## Accessing the Monitoring Stack

Once the services are up:

* **Prometheus UI:** `http://localhost:9090`
* **Alertmanager UI:** `http://localhost:9093`
* **Blackbox Exporter UI:** `http://localhost:9115`
* **Grafana UI:** `http://localhost:3000`
    * **Default Credentials:** `admin` / `admin` (change immediately after first login!)

## Monitoring an External Application

To monitor your external CRUD application (or any HTTP endpoint), ensure:
1.  **Blackbox Exporter Probe Target:** In `prometheus/prometheus.yml`, the `targets` under `job_name: 'blackbox_app_http'` should point to the correct, accessible URL of your application (e.g., `'http://18.208.141.241:8000/docs'`).
2.  **Network Reachability:** The Docker container running `blackbox_exporter` must be able to reach your application's IP and port. Ensure any firewalls or security groups allow inbound traffic on port 8000 (or whatever your app uses) from the host running Docker.

## Troubleshooting

* **Check Docker Logs:**
    ```bash
    docker-compose logs -f [service_name]
    # Example: docker-compose logs -f prometheus
    ```
* **Prometheus Target Status:** Access the Prometheus UI (`http://localhost:9090`), navigate to "Status" -> "Targets" to see if your scrape targets are `UP`.
* **Prometheus Alert Status:** In the Prometheus UI, navigate to "Alerts" to see the status of your configured alerts.
* **Alertmanager Status:** Access the Alertmanager UI (`http://localhost:9093`) to check active alerts, silences, and configurations.
* **Firewall Rules:** Ensure necessary ports (9090, 9093, 9115, 3000, and your application's port like 8000) are open on your host machine and any cloud security groups if applicable.
* **SMTP Configuration:** Double-check your `alertmanager.yml` for correct SMTP server, port, username, and password. Test sending a manual email from the server running Docker to confirm outbound connectivity.


