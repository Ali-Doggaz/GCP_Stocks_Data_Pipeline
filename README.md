# Architecture Analysis

![image](https://github.com/Ali-Doggaz/GCP_Stocks_Data_Pipeline/assets/62618334/34e21e21-1586-4e6e-a19f-a261df4eb037)

## Data Source
- **Finnhub API**: Data is ingested from the Finnhub API, and streamed in real-time using Websocket connections, ensuring minimal latency.
- **Batch Stocks Data**: Over a few weeks, we've collected our own stocks data from various private sources and pre-processed them during the collection phase (ETL). We then stored all of them in a cloud storage bucket. The "batch" part of this data pipeline aims to collect this data, post-process it, and then analyze it along with the real-time data.

## Processing
### Real-Time Stream Processing
- **Cloud Functions**: Triggered by incoming data, performs preliminary data parsing and validation.
- **Pub/Sub**: Acts as a message broker, decoupling data collection from processing. Supports scalable message queuing.
- **Dataflow (Stream)**: Processes and transforms data streams, grouping by fixed window sizes of 5 seconds for efficient aggregation.
  
    **Batch**
  ![image](https://github.com/Ali-Doggaz/GCP_Stocks_Data_Pipeline/assets/62618334/92b8d7d6-fbfe-4491-aa9f-f673346d736b)
    **Stream**
  ![image](https://github.com/Ali-Doggaz/GCP_Stocks_Data_Pipeline/assets/62618334/98554a95-cb0d-4377-8d5f-5b2a21dbb1c6)

- **Error Handling**: Errors are extracted, flattened, and stored in a BigQuery 'deadletter' table for later analysis.

### Batch Data Processing
- **Cloud Storage**: Stores batch data in JSON format, each file containing transactional data.
- **Dataflow (Batch)**: Triggered manually to process stored batch data, performing cleansing and transformation.

## Storage
- **BigQuery**: Central data warehousing solution where both streaming and batch processed data are consolidated into a single table for analysis.

## Data Analysis and Presentation
- **Looker Studio**: Connects to BigQuery to visualize and report on the data. Reports are dynamically updated to reflect new data entries.
