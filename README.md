# Cyber AI Agent Demo (Zeek + PostgreSQL + Google ADK)

This repository demonstrates a fully reproducible pipeline for ingesting network traffic into a relational database and utilizing a Multi-Agent AI system powered by Google ADK to autonomously hunt for MITRE ATT&CK techniques.

**PCAP → Zeek logs → PostgreSQL → AI Agent Analysis (Google ADK & MCP)**

The goal is to support experimentation and research by allowing an AI agent team to query network telemetry using SQL and provide automated threat hunt reports.

---

## Overview

This demo:

1. Processes a `.pcap` file using Zeek
2. Generates structured network logs (`conn`, `dns`, `http`, etc.)
3. Loads those logs into a PostgreSQL database
4. Starts a localized Multi-Agent system orchestrated by Google ADK
5. Exposes a Web UI where you can task the "Crew Lead" agent to hunt using pre-built analytics

### The AI Agent Team
- **Crew Lead**: Coordinates the hunt, interprets results, and writes the formal Threat Hunt Report.
- **Analytic Support Officer (ASO)**: Uses Retrieval-Augmented Generation (RAG) to find existing Python analytic scripts matching a given MITRE ATT&CK technique, runs them against the database, and debugs logic errors.
- **Data Engineer**: Inspects the live PostgreSQL schema to help the ASO resolve SQL mapping and database errors via Model Context Protocol (MCP) tools.

---

## Requirements

To run this demo on a base Ubuntu VM, you only need `make` and Docker. No local installation of Zeek or PostgreSQL is required. 

### Ubuntu Setup Script
```bash
sudo apt update
sudo apt install -y make docker.io docker-compose-v2
# Ensure your user is in the docker group to run docker without sudo
sudo usermod -aG docker $USER
newgrp docker
```

You also need a **Google Gemini API Key** to power the ADK Agents.

---

## Quick Start

1. **Set your Google API Key**:
   ```bash
   export GOOGLE_API_KEY="your_api_key_here"
   ```

2. **Run the full demo**:
   ```bash
   make demo
   ```

   This will:
   * Reset any existing containers/volumes
   * Process the PCAP with Zeek
   * Load the logs into PostgreSQL
   * Spin up the MCP Servers, PostgreSQL database, and the Google ADK Agent container

3. **Access the ADK Web Interface**:
   Open your browser and navigate to: `http://localhost:8080`
   *(Note: It may take ~30 seconds for the web interface to become available while the agent container installs ADK dependencies)*

---

## Using the Agents

In the ADK Web UI, you can send plain-language queries to the Crew Lead, such as:
- *"Hunt the network traffic for T1071.001 occurrences."*
- *"Find any HTTP Connections with IP Address as Host Header."*

The agents will autonomously search for relevant scripts, execute them, resolve schema issues, and return a comprehensive threat hunt report.

---

## Manual Pipeline Steps

If you prefer to run the data pipeline step-by-step without the AI interface:

1. **Reset and Start PostgreSQL**:
   ```bash
   make reset
   ```
2. **Generate Zeek Logs**:
   ```bash
   make zeek
   ```
3. **Load Logs into PostgreSQL**:
   ```bash
   make load
   ```
4. **Query the Data Manually**:
   ```bash
   make query
   ```

---

## Project Structure

```
.
├── agents/             # Google ADK agent code and MCP server setup
├── pcaps/              # Input PCAP files to process
├── rag_ingest/         # Knowledge base and pre-built analytic scripts (RAG database)
├── scripts/            # Log ingestion + utility scripts
├── sql/                # Table schemas, indexes, and SQL setup files
├── docker-compose.yml  # Docker environment definition
├── Makefile            # Convenience commands
└── README.md
```

---

## Reproducibility

This demo is fully containerized. No local installation of Zeek or PostgreSQL is required.

All results are reproducible from the included PCAP file.

---

## PCAP Files

The example PCAP file is from [Palo Alto Unit 42's Github.]https://github.com/pan-unit42/Wireshark-quizzes/tree/main)
