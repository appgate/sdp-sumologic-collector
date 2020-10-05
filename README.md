# Disclaimer
``` 
© 2020, Cyxtera Cybersecurity, Inc. d/b/a AppGate.  All rights reserved.
Redistribution and use in source and binary forms, with or without modification,
are permitted provided that the following conditions are met: (a) redistributions
of source code must retain the above copyright notice, this list of conditions
and the disclaimer below, and (b) redistributions in binary form must reproduce
the above copyright notice, this list of conditions and the disclaimer below in
the documentation and/or other materials provided with the distribution.
THE CODE AND SCRIPTS POSTED ON THIS WEBSITE ARE PROVIDED ON AN “AS IS” BASIS AND
YOUR USE OF SUCH CODE AND/OR SCRIPTS IS AT YOUR OWN RISK.  APPGATE DISCLAIMS ALL
EXPRESS AND IMPLIED WARRANTIES, EITHER IN FACT OR BY OPERATION OF LAW, STATUTORY
OR OTHERWISE, INCLUDING, BUT NOT LIMITED TO, ALL WARRANTIES OF MERCHANTABILITY,
TITLE, FITNESS FOR A PARTICULAR PURPOSE, NON-INFRINGEMENT, ACCURACY, COMPLETENESS,
COMPATABILITY OF SOFTWARE OR EQUIPMENT OR ANY RESULTS TO BE ACHIEVED THEREFROM.
APPGATE DOES NOT WARRANT THAT SUCH CODE AND/OR SCRIPTS ARE OR WILL BE ERROR-FREE.
IN NO EVENT SHALL APPGATE BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
RELIANCE, EXEMPLARY, PUNITIVE OR CONSEQUENTIAL DAMAGES, OR ANY LOSS OF GOODWILL, 
LOSS OF ANTICIPATED SAVINGS, COST OF PURCHASING REPLACEMENT SERVICES, LOSS OF 
PROFITS, REVENUE, DATA OR DATA USE, ARISING IN ANY WAY OUT OF THE USE AND/OR
REDISTRIBUTION OF SUCH CODE AND/OR SCRIPTS, REGARDLESS OF THE LEGAL THEORY UNDER
WHICH SUCH LIABILITY IS ASSERTED AND REGARDLESS OF WHETHER APPGATE HAS BEEN ADVISED
OF THE POSSIBILITY OF SUCH LIABILITY.
``` 
# sdp-customization-sumologic

## Objective:
Allow LogFowarder to send logs to SumoLogic Securely

### Issue:
At this time SumoLogic does not support receiving TLS data directly with a Syslog Source

### Resolution:
Customization will install SumoLogic collector on the LogForwarder. This will enable logs to be sent to 127.0.0.1 without TLS, looping back to the collector which then digests the data and sends it to SumoLogic directly using the installation token and any other encryption methods implemented by SumoLogic

## Requirements:
- LogFowarder has outbound internet access to the SumoLogic download address (default: https://collectors.sumologic.com/rest/download/linux/64 , all options listed here )
- A SumoLogic installation token that has been generated for use by this tool ( https://help.sumologic.com/Manage/Security/Installation_Tokens ) 
- Administrative access to the AppGate Admin Portal

## Configuration:
### Update Package For Your Environment
- Unzip the attached package
- Open ‘/data/settings.config’
	* Update line 6 with desired name for SumoLogic Collector
	* Update line 7 with the installation token retrieved from SumoLogic
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
- This file can also be replaced/renamed by updating line 11 in “/data/settings.config”

## Note:
When collector is reinstalled it creates a new instance with a unique id in SumoLogic. The 'source' still has the originally provided name so the data can still be aggregated in searches
