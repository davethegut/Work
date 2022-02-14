

# Setting up Windows Sidecar in Graylog

## Intro
"Graylog Sidecar is a lightweight configuration management system for different log collectors, also called Backends." We wanted to create a short step-by-step breakdown of how to get the Windows Sidecar service installed and running on your choice Windows Machine, and have it to be able to successfully send logs to your Graylog instance via the Graylog Sidecar. 

## Pre-reqs
- Graylog 3.0 or newer

## Notes before getting started

- When choosing what version of Sidecar to install, you must choose the version based off the Graylog version you are currently using. You can find the windows Sidecar install link [here](https://github.com/Graylog2/collector-sidecar/releases/).

| Sidecar version  | Graylog Version |
| ------------- |:-------------:|
|1.1.x     | 3.2.5 or higher     |
| 1.0.x      | 3.0 or higher     |

### Notes for getting started
- Ensure that the Graylog Sidecar service is successfully installed inside your Windows OS.
- Have your Graylog instance up and running alongside your Windows OS, as we will be switching back and forth between the Windows machine and Graylog instance.

## Setting up Graylog 
- Before getting started, let's set up our beats input in Graylog to help speed up the install process.  
- In Graylog, go to systems/input and select "Beats" in the bar on the left hand side, then click "Launch new input.
- ![Beats Input](https://github.com/davethegut/Work/blob/b5cc7c33ca1be9b87964c42464d2a9af27622959/pictures/Launch_beats_input.png). 
- After launching a new input, select "Global" and ensure that your input is set to port "5044" which is where we will be sending our Windows Sidecar logs to. 
##### Now that we have setup our input, let's go ahead and install the Sidecar service on our Windows machine.

## Installation
- Select which version of Sidecar to download to your Windows machine based off of your Graylog version.
##### Let's switch back to Graylog and configure our Windows Sidecar configuration. 

## Creating API-Token
- Go into systems/sidecars, and click on "Create or reuse a token for the graylog-sidecar user" 
![api_token1](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/Api-Token1.2.png)
- Enter your choice name into the "Token Name" slot and click "Create Token."
![api_token2](https://github.com/davethegut/Work/blob/b0a4848f8b716f2555c34b0717701afe7c0789e0/pictures/api-token2.png)
- __ATTENTION: This will be the only time you can copy your "server API token," so I suggest that you paste it somewhere safe, just in case you need to use this token again__

## Configuring Sidecar Service in Windows
- Now that we have our API token, it's time to run the Windows Sidecar installer.
![Windows_sidecar_installer](https://github.com/davethegut/Work/blob/f6889fbe64fbf3da137f88d07cc0093893eabfeb/pictures/Windows-sidecar-installer.png)
- As seen above, enter in the URL to your Graylog API, it should be pre configured as (http://127.0.0.1:9000/api)
- Then name your Sidecar instance and enter your server API token that we created earlier. 
- Once finished, you can change or configure your sidecar.yml file, which should be located in `C:\Program Files\Graylog\sidecar\sidecar.yml`
##### Now that our service is fully installed and configured in our Windows machine, let's switch back to our Graylog instance to setup the configuraton there.

## Configuring our Winlogbeat Collector
- Go to Systems/Sidecar within your Graylog instance and select the configuration tab in the left hand corner, then click the "Create Configuration" tab.
- We are focusing on Winlogbeat with Windows, so select that collector within the drop-down.
    ![sidecar_configuration](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/selecting_winlogbeat_collector.png)
- This will be the configuration that Graylog pre-builds for you: 
```
# Needed for Graylog
fields_under_root: true
fields.collector_node_id: ${sidecar.nodeName}
fields.gl2_source_collector: ${sidecar.nodeId}
output.logstash:
   hosts: ["<your_graylog_ip>:5044"]
path:
  data: C:\Program Files\Graylog\sidecar\cache\winlogbeat\data
  logs: C:\Program Files\Graylog\sidecar\logs
tags:
 - windows
winlogbeat:
  event_logs:
   - name: Application
   - name: System
   - name: Security
```
- Then give this configuration a name, and even a color if you want, then click "Create"
![Winlogbeat_config](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/Winlogbeat_config.png)
- Once created, we should see our new configuration in our Graylog instance
![graylog_config_page](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/Windows_sidecar_configurations_main_page.png)
##### Now that the configuration is ready to go, let's go ahead and install and start our service in our windows machine. 

## Installing and Starting Service
- Open a command prompt window using administrator rights 
   - Run the following commands: 
        - `& "C:\Program Files\graylog\sidecar\graylog-sidecar.exe" -service install `
        - `& "C:\Program Files\graylog\sidecar\graylog-sidecar.exe" -service start `
- Now, the sidecar service is succesfully started on your windows machine! 
##### Let's switch back to our Graylog instance and connect our Windows Sidecar to the configuration we made earlier.

## Final Step
- Under System/Sidecar, then under the administration tab, you should see your windows device detected.
![adminstration_page_sidecar](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/Windows-Sidecar-Administration-page.png)
- Select the "winlogbeat" collector underneath our Windows Sidecar machine on the left-side, and on the "Configure" drop down on the right-hand side select the "windows_sidecar" configuration that we set up earlier. 
![choosing_config_for_sidecar](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/choosing_sidecar_config.png)
- Once your configuration choice is selected, click the "process" drop down on the right-hand side and select "Start,"
![starting_your_sidecar](https://github.com/davethegut/Work/blob/6668f9450a5d6d2de3aafe99457b7208e432b987/pictures/starting_our_sidecar.png)
-   Now, as soon as you do that your Graylog instance should now be succesfully started and collecting logs from your Windows machine. 
![service_is_started](https://github.com/davethegut/Work/blob/6668f9450a5d6d2de3aafe99457b7208e432b987/pictures/service_is_started.png)

### Testing
- To ensure that our Graylog instance is collecting our windows logs, go to the overview tab underneath Sidecar, and select "Show messages" button on the right-hand side, and it should now show the logs that are coming from our Windows Machine. 
![sidecar_test_pic_1](https://github.com/davethegut/Work/blob/68bde38211fb26a7762c100074c19e6c7b7c5a80/pictures/Sidecar_test1.png)
- Here is a more detailed example of what your search page should look like when you view your incoming logs from your Sidecar. 
![sidecar_test_pic_2](https://github.com/davethegut/Work/blob/3b39678c460ae73355d8ce36afd4fbd1f3331eb2/pictures/Sidecar_test2.png) 
