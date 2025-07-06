# [CRUD APP/ MonitoringServer_GP] - Monitoring Stack

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
