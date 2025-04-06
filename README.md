# Orchestrated Scalable Streaming Pipeline

**`docker-compose.yml`**: Defines and manages all the services required for the pipeline using Docker.   
**`airflow/`**: Contains files related to the Airflow orchestration:   
    * **`Dockerfile`**: Dockerfile for building the Airflow webserver and scheduler image.   
    * **`requirements.txt`**: Lists Python dependencies for the Airflow environment.   
    * **`dags/`**: Directory containing the Airflow DAG definition (`produce_topic_dag.py`).   
**`spark/`**: Contains the Spark Structured Streaming application:   
    * **`spark_streaming.py`**: The Python script for the Spark streaming job.   

## Prerequisites

* **Docker** ([https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)) and **Docker Compose** ([https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)) installed on your system.

## Getting Started

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/Ajay-Deshpande/Orchestrated-Scalable-Streaming-Pipeline](https://github.com/Ajay-Deshpande/Orchestrated-Scalable-Streaming-Pipeline)
    cd ajay-deshpande-orchestrated-scalable-streaming-pipeline
    ```

2.  **Start the services using Docker Compose:**
    ```bash
    docker-compose up -d
    ```
    This command will build and start all the necessary services defined in the `docker-compose.yml` file, including ZooKeeper, Kafka brokers, Kafka Connect, Schema Registry, Kafka UI, Airflow (webserver, scheduler, and database), Spark master, and Cassandra.

3.  **Access the UIs:**
    * **Airflow UI:** Navigate to `http://localhost:8080` in your web browser. You might need to wait a few minutes for the webserver to start. Log in with the default credentials (username: `admin`, password: `admin`).
    * **Kafka UI:** Navigate to `http://localhost:8888`.
    * **Spark UI:** Navigate to `http://localhost:8085`.
    * **Cassandra:** You can interact with Cassandra using a client tool like `cqlsh` connected to `localhost:9042` with username `cassandra` and password `cassandra`.

## Pipeline Workflow

1.  **Airflow DAG Execution:** The `random_people_names` DAG in Airflow is scheduled to run daily at 01:00 UTC. It consists of a `PythonOperator` that executes the `start_streaming` function.
2.  **Data Fetching and Publishing:** The `start_streaming` function fetches random user data from the `https://randomuser.me/api/?results=1` endpoint. It then formats this data into a JSON and publishes it to the `random_names` Kafka topic. This process runs continuously for 2 minutes each time the DAG is executed.
3.  **Real-time Data Consumption and Processing:** The Spark Structured Streaming application continuously listens to the `random_names` Kafka topic.
4.  **Data Transformation:** The Spark application parses the JSON data received from Kafka and transforms it into a structured format based on the defined schema.
5.  **Data Persistence:** The processed data is then written in real-time to the `random_names` table within the `spark_streaming` keyspace in the Cassandra database.

## Configuration

* **Kafka:** The Kafka cluster consists of three brokers (`kafka1`, `kafka2`, `kafka3`). The advertised listeners are configured for both internal and external access.
* **Airflow:** The Airflow environment is configured to use a PostgreSQL database and the Local executor. DAGs are mounted from the `./airflow/dags` directory.
* **Spark:** The Spark master is configured to connect to the Kafka brokers and the Cassandra instance. The necessary Kafka and Cassandra connector JARs are included in the Spark session configuration.
* **Cassandra:** A single Cassandra instance is used for storing the processed data.
* **Kafka UI:** Configured to connect to the Kafka cluster and the Schema Registry.

## Further Development

* **Error Handling:** Implement more robust error handling mechanisms in the Airflow DAG and the Spark streaming application.
* **Data Transformation Logic:** Extend the Spark streaming application to perform more complex data transformations and enrichments.
* **Scalability Enhancements:** Explore options for scaling the Spark streaming application and the Cassandra cluster for higher data volumes.
* **Monitoring and Alerting:** Integrate monitoring tools and alerting mechanisms for the entire pipeline.
* **Testing:** Add unit and integration tests for the different components of the pipeline.
* **More Sophisticated Data Generation:** Implement more realistic and varied data generation.
