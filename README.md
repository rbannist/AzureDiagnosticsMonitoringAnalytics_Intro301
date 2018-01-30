# Azure Diagnostics, Monitoring, and Analytics - Intro (301) - VM/IaaS Focus

This lab is focused on the operational aspects of working with Azure.  The subject areas in this lab were scoped with ITIL Service Operation practices in the areas of Problem Management, Event Management, and Incident Management in mind.

<br><br>

## Exercise 1 - Inspect Azure Activity Logs

In this exercise we will look at the Activity Log in Azure.  The Activity Log provides insight into the operations that have been performed on resources in your subscription using Resource Manager.  For example, creating a virtual machine or deleting a Public IP will show up in the Activity Log.

You created a Virtual Machine in a previous lab so there should be logs related to the creation of a Virtual Machine resource and other supporting resources such as a NIC, a Storage Account, an OS disk, etc. in your Activity Log.  So, let's take a look at your Activity Log entries related to the resources created during that lab.

<br>

### Step 1 - Browse to the Activity Log screen

Click 'All services' in the top left corner of your browser window, scroll down to the 'MONITORING + MANAGEMENT' section, and select Activity Log.

![Visit the Activity Log](images/1_ActivityLogVisit.png?raw=true)

<br>

### Step 2 - Filter the log entries

Give it a few seconds and the page will load in full.  We will create a filter ad-hoc rather than building and saving a query.  Start by using the drop-down options to filter the logs in a way that is suitable in order to see activity related to the provisioning of your VM.  For example, the screenshot below shows a filter that captures my log entries by selecting the Resource Group that contains my VM and coupling that with extending the 'Timespan' to 1 week as my VM was created more than 1 day ago:

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

Stay where you are within the Azure Portal because in the next exercise we will be configuring an Activity Log Alert.

<br><br>

## Exercise 2 - Setting up Alerts

The goal of the Alerts experience is to be the single place to view and manage all your Azure alerts.  In this exercise, before visiting the main Alerts page, we will start by setting up an alert in advance as we have an option to do so on the screen that we're currently on.

You can configure and get information about alert rules using Azure Portal, PowerShell, Azure CLI, and the Azure Monitor REST API.

This guide shows the use of Azure Portal.

There are two ways to trigger an alert in Azure:
- Metric values - The alert triggers when the value of a specified metric crosses a threshold you assign in either direction
- Activity log events - An alert can trigger on every event, or, only when certain events occurs

We will setup a simple Activity log alert.

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

![Create EMail Alert Add Alert Name](images/8_AlertActionName.png?raw=true)

<br>

...and click 'OK' to finish and wait a few seconds until:

![Create Alert and Action Group notification](images/9_AlertandActionGroupCreated.png?raw=true)

<br>

Lastly, check your e-mail inbox - you should see a new e-mail:

![Create Alert and Action Group notification](images/10_ActionGroupCreatedEmail.png?raw=true)

<br>

### Step 2 - Navigate to the main Alerts page and take a look around

Navigate using the same method as before:

![Navigate to Alerts page](images/11_AlertsNavigate.png?raw=true)

<br>

You will be taken to a page that should list the new alert that you have just created.  You should also see an 'Add activity log alert' button (which you'll not press but, FYI, that's how you create an alert from the main Alerts page vs. at the Activity Log level).

![Alerts page](images/11_AlertsNavigate.png?raw=true)

<br>

### Step 3 - Trigger and validate the Alert created in Step 1

In order to trigger the alert you need to make a change to a Network Interface.  You'll make a change to the Network Interface of the VM that you created earlier.  The change that you'll make is to move from Dynamic IP assignment to Static (keeping the same IP address).

Start by navigating to the Resource Group that the VM resides in and then click the NIC that is listed:

![VM NIC](images/12_AlertsVMNIC.png?raw=true)

<br>

Then click on 'IP configurations' followed by 'ipconfig1'.

![VM NIC IPConfig](images/13_AlertsVMNICIPConfig.png?raw=true)

<br>

Lastly, click on 'Static' on the radio button under 'Private IP address settings' - 'Assignment' followed by 'Save' (ensuring that the IP address is present/remains the same).

![VM NIC IPConfig Static](images/14_AlertsVMNICIPConfigStatic.png?raw=true)

<br>

An e-mail should now arrive in the inbox of the account entered when the Alert Action was added.  This is what it should look like:

![Action E-mail](images/15_AlertsActionEmail.png?raw=true)

<br>

You have now configured an Alert and verified that it's being triggered under the right circumstances and that the action that is triggered successfully completes.

<br>

### Step 4 - An introduction to Azure Monitor

So far, we have worked with the Acitivity Log and Alerts.  Many of the operational tools available in Azure have been aggregated into one place.  This aggregation is within something called Azure Monitor.  Azure Monitor provides base-level infrastructure metrics and logs for most services in Microsoft Azure.

It has a landing page that helps you:
- Understand the monitoring capabilities offered by Azure
- Discover, configure, and on-board Azure’s platform and premium monitoring capabilities

While the Azure Monitor service is released, the landing overview page is in preview.

The page is a starting point for navigation, including on-boarding.  It shows curated notable issues from different services and allows you to navigate to them in context.

To introduce yourself to Azure Monitor, start by navigating to it:

![Azure Monitor Navigate](images/16_AzureMonitorNavigate.png?raw=true)

<br>

The Azure Monitor Overview page provides a nice snapshot view of what's going on in a given Subscription.  See the 'Alert fired' count and the list of Alerts that have been fired:

![Azure Monitor Overview](images/17_AzureMonitorOverview.png?raw=true)

<br>

Click into the 'Activity Log' and 'Alerts' pages... look familiar?

![Azure Monitor Activity Log](images/18_AzureMonitorActivityLog.png?raw=true)

<br>

![Azure Monitor Alerts](images/19_AzureMonitorAlerts.png?raw=true)

<br>

You will be introduced to other operational tools/solutions that are aggregated by Azure Monitor during the rest of this guide but feel free to click around now and take a look now.

<br>

#### END OF EXERCISE 2

<br><br>

## Exercise 3 - Inspecting Boot Diagnostics Logs and Guest OS Diagnostics Logs

VM Boot Diagnistics logs and Guest OS-level Diagnostics Logs fall under 'Event Management' from an ITIL viewpoint and visibility of these logs can aid efforts related to maintaining the health of IT services.  VM Boot Diagnostics logs are Resource-level diagnostics logs.

Azure resource-level diagnostic logs are logs emitted by a resource.  The content of these logs varies by resource type.  Resource-level diagnostic logs provide insight into operations that were performed within that resource itself.  For example, getting a secret from a Key Vault would be logged in a Resource-level log.

Guest OS-level diagnostic logs differ from Resource-level diagnostics logs. Guest OS diagnostic logs are those collected by an agent running inside of a virtual machine or other supported resource type.  Resource-level diagnostic logs require no agent and capture resource-specific data from the Azure platform itself, while guest OS-level diagnostic logs capture data from the operating system and applications running on a virtual machine.

In this exercise you will be checking that boot diagnostics logs are being collected on a given/your VM (which they will be because you configured your VM to do so in a previous lab) and then inspecting the logs using Azure Portal, a manual download from the backing Storage Account, and then lastly with Azure CLI and PowerShell.  You will then enable Guest OS-level diagnostic logging on your VM and inpsect the output.

<br><br>

### Step 1 - Ensure that Boot Diagnostics and Guest OS Diagnostics logging is setup

Browse to your previously-created VM in the Azure Portal by clicking on 'Virtual machines' and then filtering to find the relevant VM or by clicking on 'Resource groups', filtering by your resource group name, and then finding your VM within the relevant resource group.

Once you have clicked on your VM you should see 'Boot diagnostics' on the left side.  Click that.

![Boot diagnostics](images/1_BootDiagnosticsScreen.png?raw=true)

You will see that boot diagnostics is already turned on and the logs are being stored in a dedicated Storage Account (in blob/object storage). 

![Boot diagnostics screen](images/2_BootDiagnosticsOn.png?raw=true)

Now, on the same screen, click 'Serial log':

![Boot diagnostics Serial log](images/3_BootDiagnosticsSeriallog.png?raw=true)

Here you will see the serial log for the VM in question.  The Serial log is populated by the Azure VM Agent or when events are printed to the terminal during boot.

The serial log is stored as a ? file in the backing Storage Account and can be downloaded directly from the page shown after clicking 'Boot diagnostics' (see 'Download serial log').  However, we're going to go and take a look around the backing Storage Account.  Make a mental note of the Storage Account name that was displayed when you checked that the services was on.  We will now navigate to that Storage Account and download the serial log for your VM.  Please click 'Storage Accounts' on the left panel:

You now need to filter/find the diagnnostics Storage Account:


Once you've clicked on the relevant Storage Account, click 'Blob', followed by the '' container.

## Exercise 4 - Leveraging Event Grid

Event Grid helps you build reactive, event-driven apps with a fully managed event routing service.  You can create richer app scenarios by connecting serverless logic to events from multiple sources inc. Azure services.

In this exercise we will be...

## Exercise 5 - Gather information from Azure Monitor

Azure Monitor operates as a consolidated pipeline for all monitoring data from Azure services.  It gives you access to performance metrics and events that describe the operation of the Azure infrastructure and any Azure services you are using.  Azure Monitor is a monitoring data pipeline for your Azure environment, and offers that data directly into Log Analytics as well as 3rd party tools where you can gain insight into that data and combine it with data from on premises or other cloud resources.

The data presented by Azure Monitor can be accessed using the Azure Portal, Azure CLI, and a RESTful API.

## Exercise 6 - Gather information from Azure Advisor to control spend

Azure Advisor is a personalised cloud consultant that helps you follow best practices to optimise your Azure deployments.  It analyses your resource configuration and usage telemetry.  It then recommends solutions to help improve the performance, security, and high availability of your resources while looking for opportunities to reduce your overall Azure spend.

With Advisor, you can:

- Get proactive, actionable, and personalised best practices recommendations.
- Improve the performance, security, and high availability of your resources, as you identify opportunities to reduce your overall Azure spend.
- Get recommendations with proposed actions inline.

The recomendations that Azure Advisor provides can be accessed using the Azure Portal.

In this exercise we will be using the Azure Portal view high availability recommendations.




## Exercise 7 - Gather information from Log Analytics

Log Analytics (formerly known as/aka OMS Log Analytics) is an Azure service that ingests log and metric data from Azure services (via Azure Monitor), Azure VMs, and on-premises or other cloud infrastructure and offers flexible log search and out-of-the box analytics on top of this data.  It provides rich tools to analyse data across sources, allows complex queries across all logs, and can proactively alert on specified conditions.  You can even collect custom data into its central repository so you can query and visualise it.  You can also take advantage of Log Analytic's built-in solutions to immediately gain insights into the security and functionality of your infrastructure.

In this exercise we will be... 


## Exercise 8 - Explore App Insights and Security Center

Application Insights offers application performance monitoring and user analytics.  It monitors the code that you've written and applications that you've deployed on Azure, on-premises, or other clouds.  By instrumenting your application with the Application Insights SDK you can get access to a range of data including response times of dependencies, exception traces, debugging snapshots, and execution profiles.  It provides powerful tools for analysing this application telemetry while developing and operating your application.  It deeply integrates with Visual Studio to enable you to get right to the problem line(s) of code so you can fix it, and offers usage analytics to analyse customer usage of your applications for product managers as well.

In this exercise we will be...

