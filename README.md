
******************************** Logging Monitoing Creation   ****************************

Monitoring Dashboard Creation
Before we setup the Dashboard we need to set up the CUSTOM LOG in logging to fetch logs.
Custom logs are logs that contain diagnostic information from custom applications.
Unified Monitoring Agent Installation 
For publishing the custom logs from the OKE worker nodes (or any other compute instance), it is required to have the Oracle Unified Monitoring Agent installed. 
To Install Oracle Unified Monitoring Agent run below command.
sudo yum install -y unified-monitoring-agent-ol-8-<version>.rpm
once the installation complete we have enable and start agent service.
sudo systemctl enable unified-monitoring-agent
sudo systemctl start unified-monitoring-agent
Use above commands enable ad start the service.
You can check if the agent is installed and running by executing this .
systemctl status unified-monitoring-agent
Setting up OCI Logging for Custom Logs
Step 1 :
First create a new Dynamic Group or a User Group, this is to identify the hosts / compute instances where logs should be collected.
With the help of Airtel team we can create Dynamic rules and matching rules. 
Ensure I matching rules we need to add the hosts / compute instances / OKE cluster id. 
All {instance.compartment.id = '<your-compartment-ocid>'} 
Create a new log group
Under "Logging - Log Groups" we can create a new group named "<Log group Name>”  . When you create ensure you select the correct compartment.

<img width="480" height="194" alt="Picture1" src="https://github.com/user-attachments/assets/fece9627-673d-404a-a5f3-ceccdb0cf155" />


Agent Configuration :
Agent Configuration plays a Vital role to fecth what custom logs you want ingest across your host / Cluster.
Specify which hosts you want to collect logs from, log inputs, and log destination settings.

 <img width="524" height="226" alt="Picture2" src="https://github.com/user-attachments/assets/7f18d215-c0fc-43e4-ac86-1af620974b30" />

 <img width="524" height="220" alt="Picture3" src="https://github.com/user-attachments/assets/3d4f9c13-3d4e-4fa4-ac68-c6a3bef927da" />

 
What marked in the snippet all inputs are important to configure. If you selected the Compartment it will list out the custom log group and the log name . 
Input types is we have already defined in the logging log-group. File path we have to given the container log paths or the Host server log path.
Then submit the form and wait until the Agent Configuration shows status "Active".

<img width="524" height="157" alt="Picture4" src="https://github.com/user-attachments/assets/26f0e179-ca30-477e-8689-e90e06f44d34" />

 
Monitoring and Searching Log Entries
OCI Console - under "Logging - Search" you can use two modes to search for log entries - basic and advanced mode.

<img width="524" height="221" alt="Picture5" src="https://github.com/user-attachments/assets/cfa22d6e-7e94-4759-8f28-ccfaab88791f" />

 
