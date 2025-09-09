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

---

## 3. Agent Configuration

Agent configuration determines which custom logs are ingested from your hosts or clusters.

- Specify the hosts, log inputs, and log destination settings.
- Select the compartment to list available log groups and log names.
- Define the input type (as set in the log group) and provide the file path for container or host server logs.
- Submit the configuration and wait for the status to show **Active**.

---

## 4. Monitoring and Searching Logs

- In the OCI Console, navigate to **Logging > Search**.
- Use either **Basic** or **Advanced** mode to search and analyze log entries.

---

**Note:** Ensure all configuration steps are completed for successful log collection and monitoring.


