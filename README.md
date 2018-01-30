# Azure Diagnostics, Monitoring, and Analytics - Intro (301) - VM/IaaS Focus

This lab is focused on the operational aspects of working with Azure.  The subject areas in this lab were scoped with ITIL Service Operation practices in the areas of Problem Management, Event Management, and Incident Management in mind.

<br><br>

## Exercise 1 - Inspect Azure Activity Logs

In this exercise you will look at the Activity Log in Azure.  The Activity Log provides insight into the operations that have been performed on resources in your subscription using Resource Manager.  For example, creating a virtual machine or deleting a Public IP will show up in the Activity Log.

You created a Virtual Machine in a previous lab so there should be logs related to the creation of a Virtual Machine resource and other supporting resources such as a Network Interface, a Storage Account, an OS disk, etc. in your Activity Log.  So, let's take a look at your Activity Log entries related to the resources created during that lab.

<br>

### Step 1 - Browse to the Activity Log screen

Click 'All services' in the top left corner of your browser window, scroll down to the 'MONITORING + MANAGEMENT' section, and select Activity Log.

![Visit the Activity Log](images/1_ActivityLogVisit.png?raw=true)

<br>

### Step 2 - Filter the log entries

Give it a few seconds and the page will load in full.  We will create a filter ad-hoc rather than building and saving a query.  Start by using the drop-down options to filter the logs in a way that is suitable in order to see activity related to the provisioning of your VM.  For example, the screenshot below shows a filter that captures my log entries by selecting the Resource Group that contains my VM and coupling that with extending the 'Timespan' to 'Last week' as my VM was created more than 1 day ago:

![Filter the Activity Log](images/2_ActivityLogFilter.png?raw=true)

<br>

### Step 3 - Inspect the log entries related to your virtual machine's provisioning

We're specifically interested in the creation of a Virtual Machine resource so expand the associated log entry.  Here you will see some of the major changes related to the creation of your Virtual Machine resource.

![Activity Log VM entries](images/3_ActivityLogVM.png?raw=true)

<br>

As an example of gathering information from the log entries, I have expanded an 'Write-NetworkInterfaces' line and selected 'JSON'.  I am able to see the Public IP that was bound to the NIC of the VM at this time.

![Activity Log VM write NIC with PIP](images/4_ActivityLogWriteNICPIP.png?raw=true)

<br><br>

#### END OF EXERCISE 1

Stay where you are within the Azure Portal because in the next exercise you will be configuring an Activity Log Alert.

<br><br>

## Exercise 2 - Setting up Alerts

The goal of the Alerts experience is to be the single place to view and manage all your Azure alerts.  In this exercise, before visiting the main Alerts page, you will start by setting up an alert in advance as you have an option to do so on the screen that you're currently on.

You can configure and get information about alert rules using Azure Portal, PowerShell, Azure CLI, and the Azure Monitor REST API.

This guide shows the use of Azure Portal.

There are two ways to trigger an alert in Azure:
- Metric values - The alert triggers when the value of a specified metric crosses a threshold you assign in either direction
- Activity log events - An alert can trigger on every event, or, only when certain events occurs

You will setup a simple Activity log alert.

<br>

### Step 1 - Create an Activity log alert

On the screen that you're currently on, click 'Add activity log alert'.

![Create Alert in Activity Log](images/5_AlertscreatefromActivitylog.png?raw=true)

<br>

Now, add names and change the settings:

- Choose the resource group that you setup for VM creation earlier rather than creating a new 'Default' one (this helps with clearing up later + keeping environments separate)
- Limit the 'Resource type' to 'Network Interfaces (Microsoft.Network/networkinterfaces)'
- As the alert is limited to the logged-in user by default, we'll expand both the 'Resource Group' and 'Resource' in scope to 'All' in order to capture any event of this type performed by you-alone
- Change the 'Operation name' value to 'Create or Update Network Interface (networkinterfaces)'
- Choose 'Informational' under the 'Level' drop-down menu
- Create a new Action Group by adding an appropriate name and a short name
- Add an action

Re. actions, amongst a few options, you can configure a metric alert to do the following:
- Send email notifications
- Call a webhook (e.g. a Logic App or Function HTTP trigger)
- Start execution of an Azure Automation runbook

Here is the full list of action options as of 01/18:

![Create EMail Alert NIC New or Change Any Resource](images/6_AlertActions.png?raw=true)

<br>

In this example, you will create an e-mail alert.   Select 'Email' under 'ACTION TYPE', enter an e-mail address that you can access the account/mailbox of, and then click 'OK'.

![Create EMail Alert Enter Email Address](images/7_EMailAlertNICNewChangeAny.png?raw=true)

<br>

Name your action `Network Interface Write` ...

![Create EMail Alert Add Alert Name](images/8_AlertEmailActionName.png?raw=true)

<br>

...and click 'OK' to finish and wait a few seconds until:

![Create Alert and Action Group notification](images/9_AlertandActionGroupCreated.png?raw=true)

<br>

Lastly, check your e-mail inbox.  You should see a new e-mail in there:

![Create Alert and Action Group notification](images/10_ActionGroupCreatedEmail.png?raw=true)

<br>

### Step 2 - Navigate to the main Alerts page and take a look around

Navigate using the same method as before:

![Navigate to Alerts page](images/11_AlertsNavigate.png?raw=true)

<br>

You will be taken to a page that should list the new alert that you just created.  You should also see an 'Add activity log alert' button (which you'll not press but, FYI, that's how you create an alert from the main Alerts page vs. at the Activity Log level).

![Alerts page](images/12_AlertsDashboard.png?raw=true)

<br>

### Step 3 - Trigger and validate the Alert created in Step 1

In order to trigger the alert you need to make a change to a Network Interface.  You'll make a change to the Network Interface of the VM that you created earlier.  The change that you'll make is to move from Dynamic IP assignment to Static (keeping the same IP address).

Start by navigating to the Resource Group that the VM resides in and then click the NIC that is listed:

![VM NIC](images/13_AlertsVMNIC.png?raw=true)

<br>

Then click on 'IP configurations' followed by 'ipconfig1'.

![VM NIC IPConfig](images/14_AlertsVMNICIPConfig.png?raw=true)

<br>

Lastly, click on 'Static' on the toggle switch under 'Private IP address settings' - 'Assignment' followed by 'Save' (ensuring that the IP address is present/remains the same).

![VM NIC IPConfig Static](images/15_AlertsVMNICIPConfigStatic.png?raw=true)

<br>

An e-mail should now arrive in the inbox of the account entered when the Alert Action was added.  This is what it should look like:

![Action E-mail](images/16_AlertsActionEmail.png?raw=true)

<br>

You have now configured an Alert and verified that it's being triggered under the right circumstances and that the action that is triggered successfully completes.

<br>

### Step 4 - An introduction to Azure Monitor

So far, you have worked with the Acitivity Log and Alerts.  Many of the operational tools available in Azure have been aggregated in one place.  This aggregation is provided by something called Azure Monitor.  Azure Monitor provides base-level infrastructure metrics and logs for most services in Microsoft Azure.

It has a landing page that helps you:
- Understand the monitoring capabilities offered by Azure
- Discover, configure, and on-board Azure’s platform and premium monitoring capabilities

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

VM Boot Diagnostics logs and Guest OS-level Diagnostics Logs fall under 'Event Management' from an ITIL viewpoint and visibility of these logs can aid efforts related to maintaining the health of IT services.  VM Boot Diagnostics logs are Resource-level diagnostics logs.

Azure resource-level diagnostic logs are logs emitted by a resource.  The content of these logs varies by resource type.  Resource-level diagnostic logs provide insight into operations that were performed within that resource itself.  For example, getting a secret from a Key Vault would be logged in a Resource-level log.

Guest OS-level diagnostic logs differ from Resource-level diagnostics logs. Guest OS diagnostic logs are those collected by an agent running inside of a virtual machine or other supported resource type.  Resource-level diagnostic logs require no agent and capture resource-specific data from the Azure platform itself, while guest OS-level diagnostic logs capture data from the operating system and applications running on a virtual machine.

The Azure diagnostics extension is needed (Windows or Linux) to collect most application level metrics and logs. The types include:
- Performance counters
- Application Logs
- Windows Event Logs
- .NET Event Source
- IIS Logs
- Manifest based ETW
- Crash Dumps
- Customer Error Logs

Without the diagnostics extension, only a few metrics like CPU usage are available.

In this exercise you will be checking that boot diagnostics logs are being collected on a given/your VM (which they will be because you configured your VM to do so in a previous lab) and then inspecting the logs using Azure Portal, a manual download from the backing Storage Account, and then lastly with Azure CLI and PowerShell.  You will then enable Guest OS-level diagnostic logging on your VM and inpsect the output.

<br><br>

### Step 1 - Ensure that Boot Diagnostics and Guest OS Diagnostics logging is setup

Browse to your previously-created VM in the Azure Portal by clicking on 'Virtual machines' and then filtering to find the relevant VM or by clicking on 'Resource groups', filtering by your resource group name, and then finding your VM within the relevant resource group.

Once you have clicked on your VM you should see 'Boot diagnostics' on the left side.  Click that followed by 'Settings'.

![Boot diagnostics](images/21_BootDiagnosticsScreen.png?raw=true)

You will see that boot diagnostics is already turned on and the logs are being stored in a dedicated Storage Account (in blob/object storage). 

![Boot diagnostics screen](images/22_BootDiagnosticsOn.png?raw=true)

### Step 2 - View the Serial log

Close settings and click 'Serial log':

![Boot diagnostics Serial log](images/23_BootDiagnosticsSeriallog.png?raw=true)

Here you will see the serial log for the VM in question.  The Serial log is populated by the Azure VM Agent or when events are printed during boot.

The serial log is stored as a .log file in the backing Storage Account alongside the latest .bmp format screenshot image.  The log can be downloaded directly from the page that you're on ('Boot diagnostics') by clicking 'Download serial log'.

However, this guide is going to take you to have a look around the backing Storage Account and download a log file from there.  Make a mental note of the Storage Account name that was displayed when you checked that the services was on.

<br>

### Step 3 - Download the Serial log

Navigate to Storage Accounts:

![All Storage Accounts](images/24_StorageAccounts.png?raw=true)

<br>

Filter on anything unique in your Diagnostics Storage Account's name and click on the relevant Storage Account:

![Filter and Select Diagnostics Storage Account](images/25_StorageAccountsFilterandEnter.png?raw=true)

<br>

Enter the Storage Account's Blobs Service:

![Enter Diagnostics Storage Account](images/26_StorageAccountBlobs.png?raw=true)

<br>

Here you will see a container that is holding your VM's diagnostics log and screenshot image - click it:

![Enter Diagnostics Storage Account Blobs Container](images/27_StorageAccountBlobContainer.png?raw=true)

<br>

You should now see two objects.  A .log log file and a .bmp screenshot image file.  Click the .log file entry followed by the 'Download' button.

![.log Blob](images/28_StorageAccountBlobClick.png?raw=true)

<br>

![.log Blob](images/29_StorageAccountBlobDownload.png?raw=true)

<br>

Depending on your browser [configuration], you'll either see the .log file open in a new tab or need to download it and then open it.  Once one of those things has happened, take a look at the contents of the log file and familiarise yourself with what is included in it.

<br>

### Step 4 - Download the Serial log using PowerShell



<br>

### Step 4 - Download the Serial log using Azure CLI



<br>

### Step 5 - Enable Guest-OS diagnostics logs and inspect



<br>

#### END OF EXERCISE 3

<br><br>

## Exercise 4 - Gather information from Azure Monitor - Metrics, Log Analytics, Service Health, App Insights, and Network Watcher

As previously mentioned, Azure Monitor operates as a consolidated pipeline for all monitoring data from Azure services.  It gives you access to performance metrics and events that describe the operation of the Azure infrastructure and any Azure services you are using.

Azure Monitor is a monitoring data pipeline for your Azure environment, and offers that data directly into Log Analytics as well as 3rd party tools where you can gain insight into that data and combine it with data from on premises or other cloud resources.

You can work with Azure Monitor data using the Monitor Azure Portal blade, Monitor PowerShell Cmdlets, Azure CLI, or Azure Monitor REST APIs.

In this exercise you will spend a bit more time with Azure Monitor by starting to use it as a starting point for other activities.

<br>

### Step 1 - Navigate back to Azure Monitor using the same method as before and read the information below

When you open the Azure Monitor page, you can select among the subscriptions that you have read access to.  For a selected subscription, you can see:
- Triggered alerts and alert sources - This table shows summary counts, alert sources, and how many times alerts fired for the selected time duration. It applies to both metric and activity log alerts. <Edit: Alerts (Preview) with unified experience also shown for all alerts - events, metrics and logs
- Activity Log Errors - If any of your Azure resources log events with error-level severity, you can view a high-level count and click through to the activity log page to investigate each event
- Azure Service Health - You can see a count of Service Health service issues, planned maintenance events, and health advisories. Azure Service Health provides personalized information when problems in the Azure infrastructure impact your services
- Application Insights - See KPIs for each AppInsights resource in the current subscription. The KPIs are optimised for server-side application monitoring across ASP.NET web apps, Java, Node, and General application types. The KPIs include metrics for request rate, response duration, failure rate, and availability %

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

![Azure Monitor Metrics](images/30_AzureMonitorMetrics.png?raw=true)

<br>

Let's take a look at then Read and Write activity on your VM's virtual OS disk.  Click the '[Guest] \LogicalDisk(_Total)\Disk Read/sec' and '[Guest] \LogicalDisk(_Total)\Disk Writes/sec' check boxes.

![Azure Monitor Metrics Disk Reads and Writes](images/31_AzureMonitorDiskRRW.png?raw=true)

<br>

This is the type of information that is very useful when dealing with Investigation and Diagnosis under the framing of ITIL Problem Management!

<br>

### Step 3 - Log Analytics intro

Log Analytics (formerly known as/aka OMS Log Analytics) is an Azure service that ingests log and metric data from Azure services (via Azure Monitor), Azure VMs, and on-premises or other cloud infrastructure and offers flexible log search and out-of-the box analytics on top of this data.  It provides rich tools to analyse data across sources, allows complex queries across all logs, and can proactively alert on specified conditions.  You can even collect custom data into its central repository so you can query and visualise it.  You can also take advantage of Log Analytic's built-in solutions to immediately gain insights into the security and functionality of your infrastructure.

Due to some unpredictability in regard to the current state of Log Analytics setup in the subscription being used, Richard will lead an unscripted overview of this.

Here's a snapshot of what to expect however:

![Azure Monitor Log Analytics](images/32_AzureMonitorLogAnalytics.png?raw=true)

<br>

![Azure Monitor Log Analytics](images/33_AzureMonitorLogAnalyticsOpen.png?raw=true)

<br>

## Exercise 5 - Gather information from Azure Advisor to control spend

Azure Advisor is a personalised cloud consultant that helps you follow best practices to optimise your Azure deployments.  It analyses your resource configuration and usage telemetry.  It then recommends solutions to help improve the performance, security, and high availability of your resources while looking for opportunities to reduce your overall Azure spend.

With Advisor, you can:

- Get proactive, actionable, and personalised best practices recommendations.
- Improve the performance, security, and high availability of your resources, as you identify opportunities to reduce your overall Azure spend.
- Get recommendations with proposed actions inline.

The recomendations that Azure Advisor provides can be accessed using the Azure Portal.

In this exercise you will be viewing high availability and security-related recommendations.

<br>

### Step 1 - Navigate to Azure Advisor





## Exercise 6 - Explore App Insights and Security Center

Application Insights offers application performance monitoring and user analytics.  It monitors the code that you've written and applications that you've deployed on Azure, on-premises, or other clouds.  By instrumenting your application with the Application Insights SDK you can get access to a range of data including response times of dependencies, exception traces, debugging snapshots, and execution profiles.  It provides powerful tools for analysing this application telemetry while developing and operating your application.  It deeply integrates with Visual Studio to enable you to get right to the problem line(s) of code so you can fix it, and offers usage analytics to analyse customer usage of your applications for product managers as well.

In this exercise we will be...


## Exercise 10 - Leveraging Event Grid

Event Grid helps you build reactive, event-driven apps with a fully managed event routing service.  You can create richer app scenarios by connecting serverless logic to events from multiple sources inc. Azure services.

In this exercise we will be...