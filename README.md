# sdp-customization-sumologic

## Objective:
Allow LogFowarder to send logs to SumoLogic Securely

### Issue:
At this time SumoLogic does not support receiving TLS data directly with a Syslog Source

### Resolution:
Customization will install SumoLogic collector on the LogForwarder. This will enable logs to be sent to 127.0.0.1 without TLS, looping back to the collector which then digests the data and sends it to SumoLogic directly using the installation token and any other encryption methods implemented by SumoLogic

## Requirements:
- LogFowarder has outbound internet access to the SumoLogic download address 
	* default: https://collectors.sumologic.com/rest/download/linux/64
	* List of all options: https://help.sumologic.com/03Send-Data/Installed-Collectors/05Reference-Information-for-Collector-Installation/02Download-a-Collector-from-a-Static-URL
- A SumoLogic installation token that has been generated for use by this tool ( https://help.sumologic.com/Manage/Security/Installation_Tokens ) 
- Administrative access to the AppGate Admin Portal

## Configuration:
### Update Package For Your Environment
- Unzip the attached package
- Open ‘/data/settings.config’
	* Update line 30 with desired name for SumoLogic Collector
	* Update line 31 with the installation token retrieved from SumoLogic
	* Optional: Update line 32 true/false if collector should be installed as ephemeral. default true is reccommended 
- Move readme file into the data folder (or delete)
- Zip files into a folder, be careful that the zip process does not nest an additional folder within. Mac users must also avoid adding __MACOSX & .DS_STORE files. Required structure is
	<pre><code>
   folder.zip
    start
    status
    stop
    data/..etc..
   </code></pre>
   NOT
   <pre><code>
   folder.zip
     folder
       start
       status
       stop
       data/.etc..
    </code></pre>

### Apply Package To AppGate
- Navigate to the AppGate Admin Portal -> Scripts -> Appliance Customizations -> Add record containing the zip file
- Navigate to System -> Appliances -> Select the appliance you wish for the tool to run on
- While viewing the appliance scroll down and select customization previous uploaded -> Save
- Allow time for SumoLogic installation process to complete. The appliance may show ‘busy’ in the dashboard

### Configure LogForwarder
- Add the LogForwarder role to the appliance
- Add new ‘TCP Client’
	* Name:  your choice
	* Hostname or IP Address: 127.0.0.1
	* Port: 514
	* Format: Syslog
	* Encryption Method: None

### Optional Configuration:
- The collector is installed with the provided source file. Any changes to the source definition will only persist if done in the ‘/data/syslog.json’ file of the customization
- This file can also be replaced/renamed by updating line 36 in “/data/settings.config”

## Note:
When collector is reinstalled it creates a new instance with a unique id in SumoLogic. The 'source' still has the originally provided name so the data can still be aggregated in searches
