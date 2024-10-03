# ELK-home-lab

# Elasticsearch Cluster Setup with Docker Compose (Home Lab)

This repository provides a Docker Compose setup for running an Elasticsearch cluster with three nodes (es01, es02, es03) and a Kibana instance. This configuration is tailored for use in a home lab environment to simulate a production-level Elasticsearch setup. It's designed to be scalable, efficient, and secure, making it ideal for learning, experimenting, and working with real-time data.

## **Table of Contents**

1. [Overview](#overview)
2. [Why This Setup?](#why-this-setup)
3. [Components Breakdown](#components-breakdown)
4. [Prerequisites](#prerequisites)
5. [Setup Instructions](#setup-instructions)
6. [Connecting to Live Data](#connecting-to-live-data)

---

## **Overview**

This Docker Compose setup spins up an Elasticsearch cluster with three nodes and a Kibana instance for visualization. Each node has its own persistent volume for data storage and runs in isolation while communicating internally through a custom Docker network. The cluster is designed to provide the robustness, fault tolerance, and high availability typically found in production environments.

The setup is built using:

- Elasticsearch OSS 7.10.2
- Kibana OSS 7.10.2
- Docker Compose

---

## **Why This Setup?**

### **1. Multi-Node Cluster**

- **Why?** A three-node Elasticsearch cluster is ideal for experimenting with distributed indexing and search functionalities. It ensures high availability—if one node goes down, the others keep the cluster operational.
- **Better than single-node?** A single-node cluster might be easier to set up, but it doesn't simulate real-world scenarios of handling failure or distributing load.

### **2. Separate Volumes for Each Node**

- **Why?** Each node has its own dedicated volume for storing data (`es-data-es01`, `es-data-es02`, etc.). This ensures that data from different nodes is stored separately, making it easier to manage and maintain.
- **Better than shared storage?** Shared storage between nodes could lead to data conflicts and inconsistent reads. Separate volumes prevent such issues and align with best practices.

### **3. Custom Docker Network**

- **Why?** Using a custom network (`elasticsearch-net`) ensures that all services (Elasticsearch nodes and Kibana) can communicate efficiently using internal service names (`es01`, `es02`, etc.), without relying on external IP addresses.
- **Better than the default network?** The default network doesn’t guarantee isolation or efficient internal communication. A custom network improves security and performance.

### **4. Increased Java Heap Size**

- **Why?** The Java heap size is set to `512m` for each node to optimize Elasticsearch's memory usage, making it more capable of handling larger data sets.
- **Better than the default heap size?** The default heap size of `256m` can be too small for real-world data processing, leading to out-of-memory errors or reduced performance.

### **5. Improved Health Checks**

- **Why?** The health check is configured to ensure that each node not only responds but also reports a healthy cluster state (`green` status), indicating all shards are active.
- **Better than basic checks?** A basic check only confirms that the container is running, but an improved health check ensures that the entire cluster is functional.

### **6. Locked Memory for Performance**

- **Why?** Memory locking (`memlock: soft: -1, hard: -1`) prevents Elasticsearch from swapping to disk, improving performance.
- **Better than no memory lock?** Without memory locking, swapping can lead to degraded performance, especially under heavy load, as Elasticsearch heavily relies on memory for performance.

---

## **Components Breakdown**

### **1. Elasticsearch Nodes**

- **es01, es02, es03**: Each node runs in its own container, with different ports mapped (9200, 9201, 9202) to avoid conflicts on the host machine. They communicate with each other internally to form a cluster.

### **2. Kibana**

- Kibana is used for visualizing Elasticsearch data. It's configured to wait for the Elasticsearch nodes to be fully operational before starting, using `depends_on` to manage dependencies.

### **3. Volumes**

- Each Elasticsearch node stores its data in a dedicated Docker volume. This setup ensures that data is persisted across container restarts and can be accessed later.

---

## **Prerequisites**

Before setting up the Elasticsearch cluster, ensure that your system has the following installed:

- Docker: Get Docker
- Docker Compose: Install Docker Compose
- At least 2GB of RAM available for the containers to run properly.

---
## **Setup Instructions**

Follow these steps to set up Elasticsearch in your home lab:

1. **Clone the repository**:
```
	git clone https://github.com/Zeeyad-Sayed/ELK-home-lab.git
	cd ELK-home-lab
```
2. **Run Docker Compose**: Start the Elasticsearch cluster and Kibana with the following command:
```
	docker-compose up -d
```
3. **Check if the services are running**: After the services start, check the status of the containers:
```
	docker-compose ps
```
4. **Access Elasticsearch**: Visit `http://localhost:9200` in your browser or use `curl`:
```
	curl http://localhost:9200
```
5. **Access Kibana**: Open your browser and go to `http://localhost:5601`. You can use Kibana to visualize and interact with your Elasticsearch data.

---

## **Connecting Elasticsearch to Live Data**

### **1. Ingesting Data**

To connect Elasticsearch with live data, you can use a variety of methods:

- **Logstash**: Set up Logstash to ingest logs or structured data into Elasticsearch. [Logstash Documentation](https://www.elastic.co/guide/en/logstash/current/index.html)
- **Beats (Filebeat, Metricbeat)**: Use Beats to send data directly from your system to Elasticsearch. For example, Filebeat can ship log files, while Metricbeat can collect system metrics. [Beats Documentation](https://www.elastic.co/beats/)

### **2. API**

You can send data to Elasticsearch directly using its REST API. For example, to index a document into an index named `my-index`, you can use the following `curl` command:
```
curl -X POST "localhost:9200/my-index/_doc/1" -H 'Content-Type: application/json' -d'
{
  "message": "Hello, Elasticsearch!"
}
'
```

### **3. Kibana Dev Tools**

Kibana provides a Console (under Dev Tools) where you can directly interact with your Elasticsearch cluster using Elasticsearch APIs. This is useful for indexing data, querying, and running administrative commands.

---

### **Next Steps**

Once your cluster is set up and you’ve ingested data, you can start experimenting with various Elasticsearch features like:

- Full-text search
- Aggregations
- Analyzers
- Scaling the cluster

Feel free to customize the setup further depending on your home lab goals. Make sure to keep track of resource usage to avoid performance bottlenecks.

---

### **Contributing**

If you have any suggestions or improvements for this setup, feel free to submit a pull request.

### **License**

This project is licensed under the MIT License.

---

### Steps to Set Up in Your Home Lab

1. **Install Docker & Docker Compose**: Follow the installation links above.
2. **Clone this repository** and navigate to the project directory.
3. **Run `docker-compose up -d`** to start the Elasticsearch cluster.
4. **Access Elasticsearch and Kibana** from your local machine to ensure the setup is working.
5. **Optional: Set up Logstash or Beats** to ingest live data, or use the REST API to manually index documents.

### Connecting to Live Data:

For real-world scenarios, you can set up data shippers like Filebeat (to send system logs) or Metricbeat (to monitor system metrics). More on these later in this repo, so stay tuned. 
Configure these tools to point to your Elasticsearch nodes (e.g., `http://localhost:9200`) and let them ingest data into your cluster.
