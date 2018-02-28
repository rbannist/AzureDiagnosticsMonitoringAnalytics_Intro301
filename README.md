# Azure Diagnostics, Monitoring, and Analytics - Intro (301) - VM/IaaS Focus

This lab is focused on the operational aspects of working with Azure.  The subject areas in this lab were scoped with ITIL Service Operation practices in the areas of Problem Management, Event Management, and Incident Management in mind.

<br><br>

## What is it?

This intro lab serves to guide you on the operational toolset that's available on Azure.  This includes:

* Activity Logs.
* Alerts.
* Azure Monitor.
* Boot Diagnostics Logs.
* Guest OS Diagnostics Logs.
* Log Analytics.
* Network Watcher.
* Azure Advisor.
* App Insights.
* Security Center.

<br><br>

## Preparing for this lab

For this Lab you will require:

* Access to an Azure Subscription.
* Access to Azure Portal using a web browser.
* Access to an e-mail account.

<br>

Note.  When using the Azure CLI or PowerShell (inc. using Cloud Shell within the Portal), after logging in, if you have more than one subscripton, you may need to set the subscription to perform actions against.

<br>

To do this with Azure CLI:

```
az account set --subscription "<subnameorguid>"
```

<br>

To do this with PowerShell:

```
Get-AzureRmSubscription -SubscriptionName '<subname>'  | Select-AzureRmSubscription
```

or

```
Get-AzureRmSubscription -SubscriptionId '<subguid>'  | Select-AzureRmSubscription
```

<br><br>

## Exercise 1 - Inspect Azure Activity Logs

In this exercise you will look at the Activity Log in Azure.  The Activity Log provides insight into the operations that have been performed on resources in your subscription using Resource Manager.  For example, creating a virtual machine or deleting a Public IP will show up in the Activity Log.

Activity logs have a 19-day data retention period.  For general information on Activity Logs [click here](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) and [click here](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#export-the-activity-log-with-a-log-profile) if you wish to understand how to store logs for more than 19 days.

You created a Virtual Machine in a previous lab so there should be logs related to the creation of a Virtual Machine resource and other supporting resources such as a Network Interface, a Storage Account, an OS disk, etc. in your Activity Log.  So, let's take a look at your Activity Log entries related to the resources created during that lab.

<br>

### Step 1 - Browse to the Activity Log screen

Click 'All services' in the top left corner of your browser window, scroll down to the 'MONITORING + MANAGEMENT' section, and select 'Activity Log'.

![Visit the Activity Log](images/1_ActivityLogVisit.png?raw=true)

<br>

### Step 2 - Filter the log entries

Give it a few seconds and the page will load in full.  We will create an ad-hoc filter rather than building and saving a query.  Start by using the drop-down options to filter the logs in a way that is suitable in order to see activity related to the provisioning of your VM/one of your VMs (to be used during all of this lab).  For example, the screenshot below shows a filter that captures my log entries based on me selecting the Resource Group that contains my VM and then coupling that with an extension to the 'Timespan' to 'Last week' as my VM was created more than 1 day ago:

![Filter the Activity Log](images/2_ActivityLogFilter.png?raw=true)

<br>

### Step 3 - Inspect the log entries related to your virtual machine's provisioning

We're specifically interested in the creation of a Virtual Machine resource so expand the associated log entry.  Here you will see some of the major changes related to the creation of your Virtual Machine resource.

![Activity Log VM entries](images/3_ActivityLogVM.png?raw=true)

<br>

As an example of gathering information from the log entries, I have expanded an 'Write-NetworkInterfaces' line and selected 'JSON'.  I am able to see the Public IP that was bound to the Network Interface of the VM at this time.  You should now find an equivalent 'Write-NetworkInterfaces' line in your log entries and expand on it/click on it.

![Activity Log VM write NIC with PIP](images/4_ActivityLogWriteNICPIP.png?raw=true)

<br>

Take time to browse around Activity Logs and get familiar with the layout and what information is at hand.

<br>

Activity Logs can fit in with integration scenarios such as:

* Create an email or webhook alert that triggers off an Activity Log event.
* Stream into an Event Hub for ingestion by a third-party service or custom analytics solution such as PowerBI.
* Analyse in PowerBI using the PowerBI content pack.
* Save to a Storage Account for archival or manual inspection. You can specify the retention time (in days) using Log Profiles.
* Query and view in the Azure portal.
* Query via PowerShell Cmdlet, CLI, or REST API.
* Export the Activity Log with Log Profiles to log Analytics.

<br>

![](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/media/monitoring-overview-activity-logs/activity_log_overview_v3.png)

<br>

Here's an example of the Power BI [Azure Audit Logs Content Pack](https://docs.microsoft.com/en-us/power-bi/service-connect-to-azure-audit-logs) that takes only a few minutes to get setup against a subscription:

![Power BI Azure Audit Logs Content Pack](images/PowerBIAuditLogsContentPack.png?raw=true)

<br><br>

#### END OF EXERCISE 1

Return to the 'Write NetworkInterfaces' entry under 'Write VirtualMachines' that you visited a moment ago within the Azure Portal because in the next exercise you will be configuring an Activity Log Alert that addresses the first *scenarios* bullet point above.

<br><br>

## Exercise 2 - Setting up Alerts

The goal of the Alerts experience is to be the single place to view and manage all your Azure alerts.  In this exercise, before visiting the main Alerts page, you will start by setting up an alert in advance as you have an option to do so on the screen that you're currently on.

You can configure and get information about alert rules using Azure Portal, PowerShell, Azure CLI, and the Azure Monitor REST API.

This guide shows the use of Azure Portal.

There are two ways to trigger an alert in Azure:
- Metric values - The alert triggers when the value of a specified metric crosses a threshold that you assign in either direction.
- Activity log events - An alert can trigger on every event, or, only when certain events occurs.

You will setup a simple Activity log alert.

<br>

### Step 1 - Create an Activity log alert

Click 'Add activity log alert' on the page that you're currently on.

![Create Alert in Activity Log](images/5_AlertscreatefromActivitylog.png?raw=true)

<br>

Now, add names and change the settings:

1. Choose the resource group that you setup for VM creation earlier rather than creating a new 'Default' one for action group association (this helps with clearing up later + keeping environments separate).
2. Limit the 'Resource type' to 'Network Interfaces (Microsoft.Network/networkinterfaces)'.
3. As the alert is limited to the logged-in user by default, we'll expand both the 'Resource Group' and 'Resource' in scope to 'All' in order to capture any event of this type performed by you-alone.
4. Change the 'Operation name' value to 'Create or Update Network Interface (networkinterfaces)'.
5. Choose 'Informational' under the 'Level' drop-down menu.
6. Create a new Action Group by adding an appropriate name and a short name.
7. Add an action.

Re. actions, amongst a few options, you can configure a metric alert to do the following:
* Send email notifications.
* Call a webhook.
* Start execution of an Azure Automation runbook.

Here is a capture of the action options as of 01/18:

![Create EMail Alert NIC New or Change Any Resource](images/6_AlertActions.png?raw=true)

<br>

In this example, you will create an e-mail alert.   Select 'Email' under 'ACTION TYPE', enter an e-mail address that you can access the account/mailbox of, and then click 'OK'.

![Create EMail Alert Enter Email Address](images/7_EMailAlertNICNewChangeAny.png?raw=true)

<br>

Name your action `Network Interface Write` ...

![Create EMail Alert Add Alert Name](images/8_AlertEmailActionName.png?raw=true)

<br>

...then click 'OK' to finish and wait a few seconds until:

![Create Alert and Action Group notification](images/9_AlertandActionGroupCreated.png?raw=true)

<br>

Lastly, check your e-mail inbox.  You should see a new e-mail in there:

![Create Alert and Action Group notification](images/10_ActionGroupCreatedEmail.png?raw=true)

<br>

### Step 2 - Navigate to the main Alerts page and take a look around

Navigate using the same method as before:

![Navigate to Alerts page](images/11_AlertsNavigate.png?raw=true)

<br>

You will be taken to a page that should list the alert that you just created.  You should also see an 'Add activity log alert' button (which you'll not click on but, FYI, that's how you create an alert from the main Alerts page vs. at the Activity Log level).

![Alerts page](images/12_AlertsDashboard.png?raw=true)

<br>

### Step 3 - Trigger and validate the Alert created in Step 1

In order to trigger the alert you need to make a change to a Network Interface.  You'll make a change to the Network Interface of the VM that you created earlier.  The change that you'll make is to move from Dynamic IP assignment to Static (keeping the same IP address).

Start by navigating to the Resource Group that the VM resides in and then click the NIC that is listed:

![VM NIC](images/13_AlertsVMNIC.png?raw=true)

<br>

Click on 'IP configurations' followed by 'ipconfig1':

![VM NIC IPConfig](images/14_AlertsVMNICIPConfig.png?raw=true)

<br>

Lastly, click 'Static' on the toggle switch under 'Private IP address settings' - 'Assignment' followed by 'Save' (ensuring that the IP address is present/remains the same).

![VM NIC IPConfig Static](images/15_AlertsVMNICIPConfigStatic.png?raw=true)

<br>

An e-mail should now arrive in the inbox of the account entered when the Alert Action was added.  This is what it should look like:

![Action E-mail](images/16_AlertsActionEmail.png?raw=true)

<br>

You have now configured an Alert, verified that it's being triggered under the right circumstances, and verified that the triggered action successfully completes.

<br>

### Step 4 - A quick introduction to Azure Monitor (more to follow later)

So far, you have worked with the Acitivity Log and Alerts.  Many of the operational tools available in Azure have now been consolidated into one place.  The consolidated view is provided by Azure Monitor.  Azure Monitor provides base-level infrastructure metrics and logs for most services in Microsoft Azure.

It has a landing page that helps you:
* Understand the monitoring capabilities offered by Azure.
* Discover, configure, and on-board Azure’s platform and premium monitoring capabilities.

While the Azure Monitor service is released, the landing overview page is in preview.

The page is a starting point for navigation, including on-boarding.  It shows curated notable issues from different services and allows you to navigate to them in context.

To introduce yourself to Azure Monitor, start by navigating to it:

![Azure Monitor Navigate](images/17_AzureMonitorNavigate.png?raw=true)

<br>

The Azure Monitor Overview page provides a nice snapshot view of what's going on in a given Subscription.  See the 'Alert fired' count and the list of Alerts that have been fired:

![Azure Monitor Overview](images/18_AzureMonitorOverview.png?raw=true)

<br>

Click into the 'Activity Log' and 'Alerts' pages... look familiar?

![Azure Monitor Activity Log](images/19_AzureMonitorActivityLog.png?raw=true)

<br>

![Azure Monitor Alerts](images/20_AzureMonitorAlerts.png?raw=true)

<br>

#### END OF EXERCISE 2

<br><br>

## Exercise 3 - Inspecting Boot Diagnostics Logs and Guest OS Diagnostics Logs

VM Boot Diagnostics logs and Guest OS-level Diagnostics Logs fall under 'Event Management' from an ITIL viewpoint and visibility of these logs can aid efforts related to maintaining the health of IT services.

VM Boot Diagnostics logs are resource-level diagnostics logs.  Azure resource-level diagnostic logs are logs emitted by a resource.  The content of these logs varies by resource type.  Resource-level diagnostic logs provide insight into operations that were performed within that resource itself.  For example, an event such as getting a secret from a Key Vault will be logged in a resource-level log.

Guest OS-level diagnostic logs differ from resource-level diagnostics logs. Guest OS diagnostic logs are those collected by an agent running inside of a virtual machine or other supported resource type.  Resource-level diagnostic logs require no agent and capture resource-specific data from the Azure platform itself while guest OS-level diagnostic logs capture data from the operating system and applications running on a virtual machine.

The Azure diagnostics extension is needed (Windows or Linux) to collect most application level metrics and logs. The types include:
* Performance counters.
* Application Logs.
* Windows Event Logs.
* .NET Event Source.
* IIS Logs.
* Manifest based ETW.
* Crash Dumps.
* Customer Error Logs.

Without the diagnostics extension, only a few metrics - like CPU usage - are available.

In this exercise you will be checking that boot diagnostics log entries are being collected on a given/your VM (which they will be because you configured your VM to do so in a previous lab) and then you'll inspect the log using Azure Portal, a manual download from the backing Storage Account, and then lastly with Azure CLI.  You will then enable Guest OS-level diagnostic logging on your VM.

<br><br>

### Step 1 - Ensure that Boot Diagnostics and Guest OS Diagnostics logging is setup

Browse to your previously-created VM in the Azure Portal by clicking on 'Virtual machines' and then filtering to find the relevant VM or by clicking on 'Resource groups', filtering by your resource group name, and then finding your VM within the relevant resource group.

Once you have clicked on your VM you should see 'Boot diagnostics' on the left side.  Click that followed by 'Settings'.

![Boot diagnostics](images/21_BootDiagnosticsScreen.png?raw=true)

You will see that boot diagnostics is already turned on and the logs are being stored in a dedicated Storage Account (in blob/object storage). 

![Boot diagnostics screen](images/22_BootDiagnosticsOn.png?raw=true)

<br>

If your Boot Diagnostics isn't switched on then please move the toggle switch to 'On', choose an existing Storage Account or create a new one, ensure that the 'Boot diagnostics' check box is selected, and then click 'Save'.  Note. I advise opening up the Azure Portal in another tab/window and then reboot the VM from there before moving on (or do this using Azure CLI or PowerShell).

<br>

### Step 2 - View the Serial log

Close settings and click 'Serial log':

![Boot diagnostics Serial log](images/23_BootDiagnosticsSeriallog.png?raw=true)

Here you will see the serial log for the VM in question.  The Serial log is populated by the Azure VM Agent or when events are printed to serial console during boot.

The serial log is stored as a .log file in a backing Storage Account alongside, in the case of Windows, a latest .bmp format screenshot image.  The log can be downloaded directly from the page that you're on ('Boot diagnostics') by clicking 'Download serial log'.

However, this guide is going to take you to have a look around the backing Storage Account with you downloading the log file from there.  Make a mental note of the Storage Account name that was displayed when you checked that the services was on (or go back and note down if needed).

<br>

### Step 3 - Download the Serial log

Navigate to 'Storage Accounts':

![All Storage Accounts](images/24_StorageAccounts.png?raw=true)

<br>

Filter on anything unique in your Diagnostics Storage Account's name and click on the relevant Storage Account:

![Filter and Select Diagnostics Storage Account](images/25_StorageAccountsFilterandEnter.png?raw=true)

<br>

Enter the Storage Account's Blobs Service:

![Enter Diagnostics Storage Account](images/26_StorageAccountBlobs.png?raw=true)

<br>

Here you will see a container that is holding your VM's diagnostics log and screenshot image.  Click it:

![Enter Diagnostics Storage Account Blobs Container](images/27_StorageAccountBlobContainer.png?raw=true)

<br>

You should now see two objects.  A .log log file and a .bmp screenshot image file.  Click the .log file entry followed by the 'Download' button.

![.log Blob](images/28_StorageAccountBlobClick.png?raw=true)

<br>

![.log Blob](images/29_StorageAccountBlobDownload.png?raw=true)

<br>

Depending on your browser [configuration], you'll either see the .log file open in a new tab or you will need to download it and then open it.  Once one of those things has happened, take a look at the contents of the log file and familiarise yourself with what is included in it.

<br>

![.log Blob](images/29_StorageAccountBlobDownload2.png?raw=true)

<br>

We took the approach of accessing the backing Storage Account to show how straightforward it is to work with a log file.  As it's sat in its native format in a Storage Account, all of the activities that you can perform with blobs are available to you.  This includes building an archive of your logs in a separate Storage Account by copying from one Storage Account to another or simply being able to 'publish' the blob using a SAS + URL.

Note. Re. the point above about archiving, there is a retention policy that can be set natively under 'Diagnostics settings'.  A retention policy can be any number of days between 1 and 2147483647. Retention policies are applied per-day, so at the end of a day, logs from the day that is now beyond the retention policy will be deleted.

<br>

Please see below for a full view of the options available when working with diagnostics logs:

![Logs output](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/media/monitoring-overview-of-diagnostic-logs/diagnostics_logs_actions.png?raw=true)

So, you can:
* Save them to a Storage Account for auditing or manual inspection. You can specify the retention time (in days) using resource diagnostic settings.
* Stream them to Event Hubs for ingestion by a third-party service or custom analytics solution such as PowerBI.
* Analyse them with Log Analytics (which you'll be introduced to later).

<br>

### Step 3 - Download the Serial log using Azure CLI

Richard/James to demonstrate as this doesn't work when using the Cloud Shell.

CLI:

```
az vm boot-diagnostics get-boot-log -g <rgname> -n <vmname>
```

<br>

PowerShell:

```
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName "<rgname>" -Name "<vmname>" -Windows -LocalPath "<localdirectory>"
```

<br>

### Step 4 - Enable Guest-OS diagnostics logs

Firsly, navigate to your VM in the Azure Portal and enter the 'Diagnostics settings' blade.

![Diagnostics Settings](images/30_DiagnosticsSettingsNavigate.png?raw=true)

<br>

Now, turn on the Guest OS logging by clicking on 'Enable guest-level monitoring' and wait until the notification that tells you that the action is complete.

![Guest Monitoring On](images/31_DiagnosticsSettingsOn.png?raw=true)

<br>

You should be presented with a page like this:

![Guest Monitoring](images/32_DiagnosticsSettingsOptions.png?raw=true)

Here you can see all of the options available to you in respect to guest-level monitoring/logging.  This includes:

* 'Configure performance counters'
  * This takes you to the 'Performance counters' tab to the right of the 'Overview' tab.
  * Here you will see a toggle switch for 'None', 'Basic', 'Custom'.  'None' = No counters, 'Basic' = A default list of counters with fixed sample rates inc. CPU, Memory, Disk, and Network, and 'Custom' = choose a counters based on any metric that's supported and set custom sample rates per-counter.
* 'Configure event logs'
  * This takes you to the 'Logs' tab to the right of the 'Performance counters' tab.
  * This is similar to Performance Counters in that you have a choice of 'None', 'Basic', and 'Custom'.  Here you can select which types and levels of logs to collect.
* 'Configure directories'
  * This takes you to the 'Logs' tab to the right of the 'Performance counters' tab.
  * This is in relation to choosing the IIS logs to collect and the log directories to monitor.
* 'Configure crash dumps'
  * This takes you to the 'Crash dumps' tab to the right of the 'Logs' tab.
  * Here you have a toggle switch for collecting memory dumps when a particular process crashes.
* 'Configure sinks'
  * This takes you to the 'Sinks' tab to the right of the 'Sinks' tab.
  * This is how you configure whether to send diagnostics data to Application Insights or not to provide advanced analytics for apps running on this VM.
* 'Configure agent'
  * This takes you to the 'Agent' tab to the right of the 'Crash dumps' tab.
  * Here you configure additional options for the Azure Diagnostics agent such as Log Level, the backing Storage Account to use (i.e. a different one to what's chosen/created), and the disk quota.
* 'View boot diagnostics' and 'Configure boot diagnostics'
  * This takes you to the settings of the VM that you're working with.
  * This is the settings page that you have visited in a prior exercise.

<br>

Click on the 'Performance counters' tab or 'Configure performance counters' on the 'Overview' tab.

Take a look.  What performance counters are turned on and what are the sample rates?

![Diagnostics Settings Performance Counters](images/33_DiagnosticsSettings.png?raw=true)

<br>

Now, click on the 'Logs' tab or 'Configure event logs' on the Overview tab.

Here, we will change a couple of settings.  Click the 'Audit success' event check box in the 'SECURITY' section and the 'Information' event check box under 'SYSTEM' and then click 'Save'.

![Diagnostics Settings Logs](images/34_DiagnosticsSettingsChangeSecurityAuditSetting.png?raw=true)

<br>

Guest-OS level monitoring is now turned on for your VM.

<br>

#### END OF EXERCISE 3

<br><br>

## Exercise 4 - Gather information from Azure Monitor - Metrics, Log Analytics, Service Health, App Insights, and Network Watcher

As previously mentioned in Exercise 2, Azure Monitor operates as a consolidated pipeline for all monitoring data from Azure services.  It gives you access to performance metrics and events that describe the operation of the Azure infrastructure and any Azure services you are using.

Azure Monitor is a monitoring data pipeline for your Azure environment, and offers that data directly into Log Analytics as well as 3rd party tools where you can gain insight into that data and combine it with data from on premises or other cloud resources.

You can work with Azure Monitor data using the Monitor Azure Portal blade, Monitor PowerShell Cmdlets, Azure CLI, or Azure Monitor REST APIs.

In this exercise you will spend a bit more time with Azure Monitor by starting to use it as a starting point for other activities.

<br>

### Step 1 - Navigate back to Azure Monitor using the same method as before and read the information below

When you open the Azure Monitor page, you can select among the subscriptions that you have read access to.  For a selected subscription, you can see:
* Triggered alerts and alert sources
  * This table shows summary counts, alert sources, and how many times alerts fired for the selected time duration. It applies to both metric and activity log alerts.
* Activity Log Errors
  * If any of your Azure resources log events with error-level severity, you can view a high-level count and click through to the activity log page to investigate each event.
* Azure Service Health
  * You can see a count of Service Health service issues, planned maintenance events, and health advisories.  Azure Service Health provides personalised information when problems in the Azure infrastructure impact your services
* Application Insights.
  * Application Insights is an Azure service that helps you get actionable insights through application performance management and instant analytics.  Within Azure Monitor, you can see KPIs for each AppInsights resource in the current subscription.  The KPIs are optimised for server-side application monitoring across ASP.NET web apps, Java, Node, and General application types.  The KPIs include metrics for request rate, response duration, failure rate, and availability %.

If you have not on-boarded to Log Analytics or Application Insights, or if you have not configured any Azure Alerts in the current subscription, this page provides links to begin your on-boarding process.

<br>

#### Azure Monitor Sources - Compute:

![Azure Monitor Sources Compute](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/media/monitoring-overview-azure-monitor/monitoring_azure_resources-compute_v6.png?raw=true)

<br>

### Step 2 - Take a look at Metrics related to your VM within Azure Monitor

All Azure services track key metrics that allow you to monitor the health, performance, availability and usage of your services.  You can view these metrics in the Azure Portal.  You can also use the REST API or .NET SDK to access the full set of metrics programmatically.

This guide focuses on the Azure Portal.

For some services, you may need to turn on diagnostics in order to see any metrics.  For others, such as virtual machines - which is what this guide focuses on - you will get a basic set of metrics by default.

On the left-side menu of Azure Monitor click 'Metrics' and then filter down to your VM.

![Azure Monitor Metrics](images/40_AzureMonitorMetrics.png?raw=true)

<br>

Let's take a look at the Read and Write activity on your VM's virtual OS disk.  Click the '[Guest] \LogicalDisk(_Total)\Disk Read/sec' and '[Guest] \LogicalDisk(_Total)\Disk Writes/sec' check boxes.

![Azure Monitor Metrics Disk Reads and Writes](images/41_AzureMonitorDiskRRW.png?raw=true)

<br>

This is the type of information that is very useful when dealing with Investigation and Diagnosis within the context of ITIL Problem Management!

<br>

### Step 3 - Log Analytics intro

Log Analytics (formerly known as/aka OMS Log Analytics) is an Azure service that ingests log and metric data from Azure services (via Azure Monitor), Azure VMs, and on-premises or other cloud infrastructure and offers flexible log search and out-of-the box analytics on top of this data.  It provides rich tools to analyse data across sources, allows complex queries across all logs, and can proactively alert on specified conditions.  You can even collect custom data into its central repository so you can query and visualise it.  You can also take advantage of Log Analytic's built-in solutions to immediately gain insights into the security and functionality of your infrastructure.

When it comes to diagnostics logs, here's what you can do if you stream them into Log Analytics:
- Log search - Write advanced queries over your log data, correlate logs from various sources, and even generate charts that can be pinned to your Azure dashboard.
- Alerting - Detect when one or more events match a particular query and become notified with an email or webhook call.
- Solutions - Use pre-built views and dashboards that give you immediate insight into your log data.
- Advanced analytics - Apply machine learning and pattern matching algorithms to identify possible issues revealed by your logs.

Due to some unpredictability in regard to the current state of your Log Analytics setup, Richard/James will lead an unscripted overview of this.

Here's a snapshot of what to expect however:

![Azure Monitor Log Analytics](images/42_AzureMonitorLogAnalytics_b.png?raw=true)

<br>

![Azure Monitor Log Analytics](images/43_AzureMonitorLogAnalyticsOpen_b.png?raw=true)

<br>

### Step 4 - Network Watcher

Network Watcher is a regional service that adds diagnostic and visualisation tools to allow you to monitor and diagnose conditions at a network scenario level in, to, and from Azure.

Network Watcher currently has the following capabilities:

* Topology
  * Provides a network level view showing the various interconnections and associations between network resources in a resource group.
* Variable Packet capture
  * Captures packet data in and out of a virtual machine.  The packet data can be stored in a blob store or on the local disk in .cap format.
* IP flow verify
  * Checks if a packet is allowed or denied based on flow information 5-tuple packet parameters (Destination IP, Source IP, Destination Port, Source Port, and Protocol).
* Next hop
  * Determines the next hop for packets being routed in the Azure Network Fabric.
* Security group view
  * Gets the effective and applied security rules that are applied on a VM.
* NSG Flow logging
  * Flow logs for Network Security Groups enable you to capture logs related to traffic that are allowed or denied by the security rules in the group.
* Virtual Network Gateway and Connection troubleshooting
  * Provides the ability to troubleshoot Virtual Network Gateways and Connections.
* Network subscription limits
  * View your network resource usage against limits.
* Configuring Diagnostics Log
  * A single pane to enable or disable Diagnostics logs for network resources in a resource group.
* Connectivity (Preview)
  * Verifies the possibility of establishing a direct TCP connection from a virtual machine to a given endpoint.

<br>

The introduction to Network Watcher featured in this guide is related to the 'Topology' and 'Next hop' capabilities.

Note. Some of the capabilities rely on a Network Watcher Agent Extension being installed on a given VM.  This is easily achieved at the VM resource level and can be automated using PowerShell and Azure CLI.

<br>

Navigate to the 'Network Watcher' blade:

![Azure Monitor Network Watcher](images/47_AzureMonitorNetworkWatcherNavigate.png?raw=true)

<br>

The landing page should look like this:

![Azure Monitor Network Watcher Expand Subscription](images/48_AzureMonitorNetworkWatcherMain.png?raw=true)

You will see references to where Network Watcher has been enabled under each Subscription and for each Region.  This is the level at which Network Watcher is enabled.

<br>

You now need to enable Network Watcher for the Region that your VM is deployed in and under the same Subscription (in the same Subscription because that's where you definitely have access rights to do so rather than it being a requirement that they're the same).  The process looks like this:

![Azure Monitor Network Watcher Enable](images/49_AzureMonitorNetworkWatcherExpand.png?raw=true)

<br>

Wait until you see a notification that tells you that the Region is enabled for Network Watcher.  After that has appeared, click on 'Topology'.  We will take a look at what that does...

![Azure Monitor Network Watcher Topology](images/50_AzureMonitorNetworkWatcherTopologyNavigateTopology.png?raw=true)

<br>

The topology function doesn't rely on the VM agent extension.  It simply generates a topology drawing based on 3 inputs that you provide.

Those inputs are:
1. Subscription.
2. Resource Group.
3. Virtual Network.

<br>

Let's now do that.  Use the three drop-down menus to select your Subscription, the Resource Group that your VM resides in, and then the Virtual Network that the Network Interface that's attached to your VM resides in.  A diagram will be generated and displayed as soon as the last input is provided.  It should look something like this:

![Azure Monitor Network Watcher Topology Diagram](images/51_AzureMonitorNetworkWatcherTopology.png?raw=true)

Handy, eh?  Each diagram that's generated can be downloaded in .svg format and then stored in a repository.

<br>

Right, that's the Topology function.  Now, we'll take a look at the 'Next Hop' function.  Start by navigating back to the main Network Watcher blade page.

![Azure Monitor Network Watcher Navigate Back](images/52_AzureMonitorNetworkWatcherNavigateBack.png?raw=true)

<br>

Click 'Next Hop'.  Some of the inputs may actually already be what you want them to be but, if not, please go through the 'Subscription', 'Resource group', 'Virtual machine', 'Network interface', and 'Source IP address' inputs in that order.  Each step should go *down a level* to get to the IP address of the VM that you're working with.

Lastly, choose a destination IP address and click 'Next hop'.  I have chosen Google's first DNS Service endpoint (8.8.8.8 with the second being 8.8.4.4).  This should route via *Internet* because I/you haven't applied any UDR to the Subnet that will cause a different forwarding policy to the default.

Here's my output:

![Azure Monitor Network Watcher Next Hop Route](images/54_AzureMonitorNetworkWatcherNextHopRoute.png?raw=true)

<br>

### Step 5 - Service Health and Service Health Alerts

The last thing that we'll take a look at in this exercise is 'Service Health'.  Service Health provides a personalised service health dashboard in the Azure portal.

The service issues view shows any ongoing issues in Azure services that are impacting your resources and you can get the most recent update to understand what Azure is doing to resolve the issue.

There is a link for each issue to use in your problem management system and a downloadable PDF summary of the issue to share with people who don’t have access to the Azure portal.  There is also a list of your resources that might be impacted by this issue in the 'potential impact' tab along with an export action to download the list.

Planned maintenance and health advisories views are available.

Alerts can be configured and we'll be creating one in a moment.  They can be focused on:

* The class of service health notification (Service issues, Planned maintenance, Health advisories).
* The subscription affected.
* The service(s) affected.
* The region(s) affected.

<br>

Let's now explore Service Health and Service Health Alerts... navigate there by clicking on the 'Service Health' link within Azure Monitor:

![Azure Monitor Service Health](images/54_1_ServiceHealthNavigate.png?raw=true)

<br>

You should be presented with a page like this:

![Azure Monitor Service Health](images/54_2_ServiceHealth.png?raw=true)

<br>

Now, whether there are any issues or not at the time of you following this guide is [obviously] completely variable.  If none are listed, you can expand the list of Regions in scope by clicking on the drop-down and then select 'Select All' to expand the scope.  In my case, there was one issue at the time of writing:

![Azure Monitor Service Health](images/54_3_ServiceHealth2.png?raw=true)

Take a look at the information presented in the image above or browse around a live entry if you find one.

<br>

We will now be *proactive* by creating an alert.  The alert can be based on any criteria - related to Azure service health - that you can think of.  This guide will focus on an example based on being alerted if anything goes wrong with Azure Active Directory in relation to the North Europe and West Europe Regions.  Click on 'Create service health alert':

![Azure Monitor Service Health Alert](images/54_4_ServiceHealthAlert1.png?raw=true)

<br>

Give the alert a name and move down to the 'Service' drop-down.  Explore the options and then select only the 'Azure Active Directory' checkbox:

![Azure Monitor Service Health Alert](images/54_5_ServiceHealthAlert2.png?raw=true)

<br>

Move down to the 'Region(s)' drop-down menu and select 'North Europe' and 'West Europe'.

![Azure Monitor Service Health Alert](images/54_6_ServiceHealthAlert3.png?raw=true)

<br>

Put a tick in the box for 'Select All' within the 'Type' drop-down menu.

![Azure Monitor Service Health Alert](images/54_7_ServiceHealthAlert4.png?raw=true)

<br>

Lastly, for ease, you'll reuse the action group that you created earlier to setup an action when this alert triggers.

The action group that you created earlier includes an action called 'Network Interface Write' which is obviously not a name that's associated with what you're working on right now.  However, the actual action - an e-mail to you - is relevant.

Note. Please feel free to create a new action group if you would like to do so.

Click 'Use existing' under 'Alert via' and then select the action group that you created earlier.

![Azure Monitor Service Health Alert](images/54_8_ServiceHealthAlert5.png?raw=true)

<br>

You will see if this if all goes well:

![Azure Monitor Service Health Alert](images/54_9_ServiceHealthAlert6.png?raw=true)

<br>

The alert that you've just created will only trigger if there is indeed an issue with Azure AD related to the North Europe and West Europe Regions.  This is extremely unlikely at anytime.

So, you'll conclude this exercise by verifying that that the alert that you've just created is indeed in place.

Click 'Health alerts (NEW)' to open the service health alerts blade.

![Azure Monitor Service Health Alert](images/54_10_ServiceHealthAlert7.png?raw=true)

<br>

You should see an entry for your alert with all of the settings that you put in place listed.

![Azure Monitor Service Health Alert](images/54_11_ServiceHealthAlert8.png?raw=true)

<br>

#### END OF EXERCISE 4

<br><br>

## Exercise 5 - Gather information from Azure Advisor

Azure Advisor is a personalised cloud consultant that helps you follow best practices to optimise your Azure deployments.  It analyses your resource configuration and usage telemetry.  It then recommends solutions to help improve the performance, security, and high availability of your resources while looking for opportunities to reduce your overall Azure spend.

With Advisor, you can:

- Get proactive, actionable, and personalised best practices recommendations.
- Improve the performance, security, and high availability of your resources, as you identify opportunities to reduce your overall Azure spend.
- Get recommendations with proposed actions inline.

The Advisor dashboard displays personalised recommendations for all your subscriptions. You can apply filters to display recommendations for specific subscriptions and resource types.  The recommendations are divided into four categories:
- High Availability: To ensure and improve the continuity of your business-critical applications
- Security: To detect threats and vulnerabilities that might lead to security breaches
- Performance: To improve the speed of your applications
- Cost: To optimise and reduce your overall Azure spending

In this exercise you will be viewing high availability and security-related recommendations related to your VM.

Note. To use Azure Advisor with a subscription, a subscription Owner must launch the Advisor dashboard.  This action registers the subscription with Advisor.  From that point on, any subscription Owner, Contributor, or Reader can access the Advisor recommendations for the subscription.  The recommendations can sometimes take some time to populate.  If you find that is taking too long then Richard or James will share their screen here.

<br>

### Step 1 - Browse to Azure Advisor

Click 'All services' in the top left corner of your browser window, scroll down to the 'MONITORING + MANAGEMENT' section, and select 'Advisor'.

![Visit the Activity Log](images/55_AdvisorVisit.png?raw=true)

<br>

![Visit the Activity Log 2](images/56_AdvisorVisit2.png?raw=true)

<br>

### Step 2 - Limit the view to the Subscription that you've been working with/in

![Subscription View](images/57_AdvisorFilterSubscription.png?raw=true)

<br>

![Subscription View](images/58_AdvisorFilterSubscription2.png?raw=true)

<br>

### Step 3 - High Availability recommendations

Now you'll take a look at the recommendations that Advisor is giving in the area of *high availability*.  Click on 'High Availability' in one of the two places highlighted below.

![Navigate High Availability](images/59_AdvisorNavigateRecommendationsHA.png?raw=true)

<br>

The view that you're presented with is based on your particular environment.  As you deployed only a single VM in an Availability Set and there was no step to enable Backup of the VM, I anticipate that your view is quite similar to what is shown below:

![High Availability Recommendations](images/60_AdvisorRecommendationsHA.png?raw=true)

If it is not, please work with what is presented or use the upcoming images as a guide.

<br>

Click on 'Add more virtual machines for improved fault tolerance' (or any option available to you).

<br>

You should be presented with a page like what's shown below which will include a list of recommendations, links to guides on how to perform the recommended changes, and an option to snooze each of the recommendation notifications:

![High Availability Linux VM](images/61_AdvisorRecommendationsHALinuxFW.png?raw=true)

In my environment, one suggested change is to add a second VM in an Availabily Set that currently hosts a single Linux VM acting as a firewall.  Note. The name of my Availability Set tells me what it's hosting (i.e. 'lfw' = Linux Firewall)... and I also recall building the environment...

<br>

Now we'll head to back to the main Azure Advisor page to look at what recomendations that it has for us in the area of *Security*.  Click on the 'Advisor Recommensations' link near the top of the page:

![Navigate back to Azure Advisor](images/62_AdvisorBrowseBack.png?raw=true)

<br>

Click on 'Security':

![Azure Advisor Security Recommendations](images/63_AdvisorBrowseSecurity.png?raw=true)

<br>

Here you will [hopefully] see all of Azure Advisor's security-related recommendations for your Subscription.  Click through what is displayed and take time to understand what is presented inc. the option to *fix* from within Azure Advisor.

![Azure Advisor Security Recommendations](images/64_AdvisorRecommendationsSecurity.png?raw=true)

<br>

#### END OF EXERCISE 5

<br><br>

## Exercise 6 - Explore Security Center

Azure Security Center provides security management and advanced threat protection across hybrid cloud workloads.  You can apply security policies across your workloads, limit your exposure to threats, and detect and respond to attacks.  Securiy Center provides:

* Centralised policy management
* Continuous security assessment
* Actionable recommendations
* Advanced cloud defenses inc. just in time access to management ports and whitelisting to control applications running on your VMs
* Prioritised alerts and incidents
* Integrated security solutions inc. collecting, searching, and analysing security data from a variety of sources

<br>

Azure Security Center is linked into/from Azure Advisor:
![Azure Advisor Security Center](images/65_AdvisorSecurityCenter.png?raw=true)

<br>

Richard/James to demonstrate from here.

<br>

#### END OF EXERCISE 6

<br><br>

## Exercise 7 - Explore App Insights

Application Insights offers application performance monitoring and user analytics.  It monitors the code that you've written and applications that you've deployed on Azure, on-premises, or other clouds.  By instrumenting your application with the Application Insights SDK you can get access to a range of data including response times of dependencies, exception traces, debugging snapshots, and execution profiles.  It provides powerful tools for analysing this application telemetry while developing and operating your application.  It deeply integrates with Visual Studio to enable you to get right to the problem line(s) of code so you can fix it, and offers usage analytics to analyse customer usage of your applications for product managers as well.

<br>

Richard/James to demonstrate.

<br>

#### END OF EXERCISE 7

<br><br>

#### END OF ALL EXERCISES