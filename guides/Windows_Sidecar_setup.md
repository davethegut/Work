

# Setting up Windows Sidecar in Graylog

## Intro
- "Graylog Sidecar is a lightweight configuration management system for different log collectors, also called Backends." We wanted to create a short step-by-step breakdown of how to get the Windows Sidecar service installed and running on your choice Windows Machine, and have it to be able to successfully send logs to your Graylog instance via the Graylog Sidecar. 

## Pre-reqs
- Graylog 3.0 or newer

## Notes before getting started

- When choosing what version of Sidecar to install, you must choose the version based off the graylog version you are currently using. You can find the windows sidecar install link [here](https://github.com/Graylog2/collector-sidecar/releases/).

| Sidecar version  | Graylog Version |
| ------------- |:-------------:|
|1.1.x     | 3.2.5 or higher     |
| 1.0.x      | 3.0 or higher     |

### Notes for getting started
- Ensure that the Graylog sidecar service is successfully installed inside your Windows OS.
- Have your Graylog instance up and running alongside your Windows OS, as we will be switching back and forth between the Windows machine and Graylog instance.

## Setting up Graylog 
- Before getting started, let's set up our beats input in Graylog to help speed up the install process.  
- In Graylog, go to systems/input and select "Beats" in the bar on the left hand side, then click "launch new input.
- (insert picture) 
- After launching a new input, select "Global" and ensure that your input is set to port "5044" which is where we will be sending our windows sidecar logs to. 
##### Now that we have setup our input, let's go ahead and install the sidecar service on our Windows machine.

## Installation
- Select which version of sidecar to download to your Windows machine based off of your Graylog version.
##### Now that sidecar is succesfully installed on our machine, let's switch back to Graylog and configure our windows sidecar configuration. 

## Creating API-Token
- Go into systems/sidecars, and click on "Create or reuse a token for the graylog-sidecar user" 
- (Enter picture)
- Enter your choice name into the "token name" slot and click "create."
- __ATTENTION: This will be the only time you can copy your "api token," so I suggest that you paste it somewhere safe, just in case you need to use this token again__

## Configuring Sidecar Service in Windows
- Now that we have our API token, it's time to run the Sidecar installer.
    - (insert picture)
- Now, enter in the URL to your graylog api, it should be pre configured as (http://127.0.0.1:9000/api)
- Then name your sidecar instance and enter your API-token that we created earlier. 
- Once finished, you can change or configure your sidecar.yml file, which should be located in "C:\Program Files\Graylog\sidecar\sidecar.yml"
##### Now that our service is fully installed and configured in our Windows machine, let's switch back to our Graylog instance to setup the configuraton there.

## Configuring our Winlogbeat Collector
- Go to systems/Sidecar within graylog instance and select the configuration tab in the left hand corner, and click "create new configuration" tab.
- We are focusing on Winlogbeat with windows, so select that collector within the drop-down.
    - (insert picture)
- This will be the configuration that Graylog pre-builds for you: 
```
# Needed for Graylog
fields_under_root: true
fields.collector_node_id: ${sidecar.nodeName}
fields.gl2_source_collector: ${sidecar.nodeId}
output.logstash:
   hosts: ["<your_graylog_ip>"]
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
- Then give this configuration a name, and even a color if you want, and click "create"
- Once created, we should see our new configuration in our graylog instance
    - (insert picture here)
##### Now that the configuration is ready to go, let's go ahead and install and start our service in our windows machine. 

## Installing and Starting Service
- Open a command prompt window using Admin 
   - Run the following commands: 
        - `& "C:\Program Files\graylog\sidecar\graylog-sidecar.exe" -service install `
        - `& "C:\Program Files\graylog\sidecar\graylog-sidecar.exe" -service start `
- Now, the sidecar service is succesfully started on your windows machine! 
##### Let's switch back to our Graylog instance and connect our windows sidecar to the configuration we made earlier.

## Final Step
- Under systems/sidecar, then under the administration tab, you should see your windows device up and running.
    - (insert photo here)
- Click on the configuration drop down on the left hand side and select the "windows_sidecar" configuration that we set up earlier. 
    - (insert picture here) 
- Once selected, click the "process" drop down and select "start," and as soon as you do that, your graylog should now be collecting logs. 

### Testing
- To ensure that our graylog instance is collecting our windows logs, go to the overview tab underneath Sidecar, and select "show messages" button on the right-hand side, and it should now show the logs that are coming from our Windows Machine. 
    - (Insert Sidecar_test1 here)
    - (insert Sidecar_test2 photo here) 

