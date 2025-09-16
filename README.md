# Monitoring Dashboard Setup

## Prerequisites

Before setting up the dashboard, you must configure **Custom Logs** in OCI Logging to collect diagnostic information from your custom applications.

---

## 1. Install Oracle Unified Monitoring Agent

To publish custom logs from OKE worker nodes or other compute instances, install the Oracle Unified Monitoring Agent:

```sh
sudo yum install -y unified-monitoring-agent-ol-8-<version>.rpm
```

After installation, enable and start the agent service:

```sh
sudo systemctl enable unified-monitoring-agent
sudo systemctl start unified-monitoring-agent
```

Verify the agent is running:

```sh
systemctl status unified-monitoring-agent
```

---

## 2. Configure OCI Logging for Custom Logs

### Step 1: Create a Dynamic Group or User Group

- Create a Dynamic Group or User Group to identify the hosts or compute instances for log collection.
- Work with your team to define dynamic and matching rules.
- Example matching rule:
  ```
  All {instance.compartment.id = '<your-compartment-ocid>'}
  ```

### Step 2: Create a Log Group

- In the OCI Console, go to **Logging > Log Groups**.
- Create a new log group (e.g., `<Log group Name>`) and select the correct compartment.

<img width="1000" height="403" alt="image" src="https://github.com/user-attachments/assets/399d5bf0-72bf-4ac3-a6b6-eefd35a7d1e5" />


---

## 3. Agent Configuration

Agent configuration determines which custom logs are ingested from your hosts or clusters.

- Specify the hosts, log inputs, and log destination settings.

<img width="1090" height="469" alt="image" src="https://github.com/user-attachments/assets/982a9df9-102d-4a24-af54-1b6196d9b905" />

<img width="1090" height="458" alt="image" src="https://github.com/user-attachments/assets/68c63c4d-6f34-44ff-a956-99c9147a5fa0" />

- 
- Select the compartment to list available log groups and log names.
- Define the input type (as set in the log group) and provide the file path for container or host server logs.
- Submit the configuration and wait for the status to show **Active**.

- <img width="1090" height="326" alt="image" src="https://github.com/user-attachments/assets/5770e95d-48d3-42f1-b410-d03c19874f71" />

---

## 4. Monitoring and Searching Logs

- In the OCI Console, navigate to **Logging > Search**.
- Use either **Basic** or **Advanced** mode to search and analyze log entries.
- 
<img width="1090" height="459" alt="image" src="https://github.com/user-attachments/assets/796663df-059a-4835-b450-cb8e2935e387" />

---

**Note:** Ensure all configuration steps are completed for successful log collection and monitoring.


## Prashast Production Log Queries

Use the following queries in the OCI Logging Search to monitor API usage and errors.

### 1. Total Logs for All APIs

```sql
'Log Source' = 'OCI Unified Schema Logs' and 'Log Group' = PRASHAST_PROD_SERVER 
| timestats count as logrecords by 'Log Source' 
| sort -logrecords
```

### 2. API Total Hits

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 
| stats count as logrecords by 'Source URL', 'Status Code' 
| eventstats sum(logrecords) as 'Total Records' by 'Source URL' 
| sort -'Total Records'
```

### 3. Total Requests

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 
| stats count as total_records by 'Source URL' 
| eventstats sum(total_records) as 'Total Records' by 'Source URL' 
| sort -'Total Records'
```

### 4. 2XX Success Count

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' = 200 
| timestats count as logrecords by 'Log Source' 
| sort -logrecords
```

### 5. 4XX Error Count

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' not in (200, 204, 500, 302, 502, 201, 504) 
| stats count as errorcount by 'Source URL', 'Status Code' 
| sort -errorcount
```

### 6. API Error Count

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' not in ('200', '204', '201') 
| stats count as errorcount by 'Source URL', 'Status Code' 
| eventstats sum(errorcount) as total_failed_hits 
| eventstats sum(errorcount) as total_hits 
| eval percentage = round((errorcount / total_hits) * 100, 2) 
| sort -errorcount 
| fields 'Source URL', 'Status Code', errorcount, percentage, total_hits
```

### 7. API Success Count

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' not in (499,504,409) and 'Status Code' in (401,429,404,400,403,413,500,412,302,502,418,415) 
| stats count as logrecords by 'Status Code' 
| eventstats sum(logrecords) as total_records 
| eval percentage = round((logrecords / total_records) * 100, 2) 
| sort -percentage 
| fields Status, percentage
```

### 8. API Errors

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' not in (200, 204, 201) 
| stats count as errorcount by 'Source URL' 
| sort -errorcount
```

### 9. 2XX Success Count Graph

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' in (200, 204, 201) 
| timestats count as errorcount by 'Source URL'
```

### 10. 4XX Error Count Graph

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' in (429,401,404,400,403,413,412,418) 
| timestats count as errorcount by 'Source URL'
```

### 11. 5XX Error Count

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Status Code' in (500,502,504) 
| stats count as errorcount by 'Source URL', Status 
| sort -errorcount

---

## **How to View Logs in Log Explorer for Prashast Production**

Follow these steps to view and analyze logs in the OCI Log Explorer for Prashast production:

---

### 1. Run the Query

Open **Log Explorer** and run the following query:

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Log Group' = PRASHAST_PROD_SERVER 
| timestats count as logrecords by 'Log Source' 
| sort -logrecords

---

### 1. Run the Query

Open **Log Explorer** and run the following query:

```sql
'Log Source' = com.oraclecloud.logging.custom.k8 and 'Log Group' = PRASHAST_PROD_SERVER 
| timestats count as logrecords by 'Log Source' 
| sort -logrecords
```
<img width="940" height="275" alt="image" src="https://github.com/user-attachments/assets/59335cfb-2cb2-4fc8-af65-049cf77a5465" />

---

### 2. Use the Left Panel to Refine Results

- On the **left side option panel**, you will see sections like **Pinned**, **Compute**, and **Others**.
- Click on **Others** to expand additional log fields.

<img width="839" height="842" alt="image" src="https://github.com/user-attachments/assets/c2d02afc-2620-48cf-8293-72fe8360da93" />


