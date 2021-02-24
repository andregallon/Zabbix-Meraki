# Overview
 The purpose of this script is to monitor the up status of Meraki devices in Zabbix. This is accomplished by making API calls against the Meraki Dashboard and the external scripts function in Zabbix. Because this script uses external checks exclusively everything is checked on the Zabbix server. This can be rather resource intensive on the Zabbix server so make sure to monitor resource usage closely
## Script Details
 To allow for separation of the Meraki API key from Zabbix, there are two scripts, the meraki_status.sh script pulls data from the API and outputs it to a json file for the meraki.sh that is used by Zabbix for discovery and status purposes.
## Templated Zabbix items and triggers
 The template is configured to create low priority alerts after a few minutes if a device or entire network becomes unreachable, then create increasingly higher priority alerts if the devices remain offline. The discovery process creates a single host within Zabbix for each Meraki network and adds it to a group based off its organization name in Meraki. 
 Alerts for discovered devices are dependent on an alert for the entire site. This is to reduce alerting to a single alarm if an entire site of devices goes offline at the same time. 
# Installation
 1.	Start by downloading the repository and moving both scripts and the orgs.txt file to your Zabbix installation.
 2.	Place the meraki_status.sh script in a secure location on the Zabbix server and limit the read/write privileges on the script to super users to protect your API key. 
 3. Add a list of Meraki Organizations that you want to monitor to the orgs.txt file. 
	i.The organization names need to be exactly the same as they are found in Meraki
 4.	Modify the variables at the top of the meraki_status.sh script
	i. replace orgs.txt with the full path to the org.txt file
	ii.	Add your Meraki API key
	iii. Change the output folder to the location of the externalscripts folder of your Zabbix installation 
 5. Place the meraki.sh script in the externalscripts folder.
 6. Modify the folder variables at the top of the meraki_status.sh script to match your installation 
 7.	Run the meraki_status.sh script to see if a valid json file is created at externalscripts/meraki.json
 8.	Assuming the test was successful, add the following two lines to a superuser crontab that has privileges to read/execute both scripts. Make sure to use the full path to both scripts, instead of the examples below. 

 ```
 * * * * * /home/user/scripts/meraki_status.sh
 */10 * * * * /usr/lib/zabbix/externalscripts/meraki.sh cron.network.discovery  
 ```

 9. Import the template into Zabbix
 10. Create a Host Group with the name of Meraki and give permissions to your users that include subgroups. 
 11. Apply the Meraki Network Discovery template to your Zabbix host, then run the discovery rule. 

