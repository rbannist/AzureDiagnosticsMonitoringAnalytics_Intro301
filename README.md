# Azure Diagnostics, Monitoring, and Analytics - Intro (301) - VM/IaaS Focus

This lab is focused on the operational side of Azure with ITIL Service Operation practices in the areas of Problem Management, Event Management, and Incident Management serving a source of guidance in regard to what will be covered.


## Exercise 1 - Inspecting Boot Diagnostics Logs and Guest OS Diagnostics Logs

VM Boot Diagnistics logs fall under 'Event Management' and visibility of them can aid efforts to maintain the health of IT services.

In this exercise we will be checking that boot diagnistics logs are being collected on a given VM (which they will be because we configured our VM to do so in a previous lab) and the inspecting the logs using Azure Portal, manual download of the backing Storage Account, and then lastly via CLI and PowerShell commands.

### Step 1 - Ensure that Boot Diagnostics and Guest OS Diagnostics is setup

Browse to your previously created VM in the Azure Portal by clicking on 'Virtual machines' and filtering to find the relevant VM or by clicking on 'Resource groups', filtering by your resource group name, and then finding your VM within the relevant resource group.

Once you have clicked your VM name you should see 'Boot diagnostics' on the left side.  Click that.

![Boot diagnostics](images/1_BootDiagnosticsScreen?raw=true)

You will see that boot diagnostics is already on and logs are being stored in a dedicated Storage Account (in blob/object storage). 

![Boot diagnostics screen](images/2_BootDiagnosticsOn.png?raw=true)

Now, on the same screen, click 'Serial log':

![Boot diagnostics Serial log](images/3_BootDiagnosticsSeriallog.png?raw=true)

Here you will see the serial log from the last time that the VM booted up.

The serial log is stored as a ? file in the backing Storage Account.  Make a mental note of the Storage Account name that was displayed when you checked that the services was on.  We will now navigate to that Storage Account and download the serial log for your VM.  Please click 'Storage Accounts' on the left panel:

You now need to filter/find the diagnnostics Storage Account:


Once you've clicked on the relevant Storage Account, click 'Blob', followed by the '' container.




## Exercise 2 - Setting an Alert

The goal of the Alerts experience is to be the single place to view and manage all your Azure alerts.  

In this exercise we will be...

## Exercise 3 - Leveraging Event Grid

Event Grid helps you build reactive, event-driven apps with a fully managed event routing service.  You can create richer app scenarios by connecting serverless logic to events from multiple sources inc. Azure services.

In this exercise we will be...

## Exercise 4 - Gather information from Azure Monitor

Azure Monitor operates as a consolidated pipeline for all monitoring data from Azure services.  It gives you access to performance metrics and events that describe the operation of the Azure infrastructure and any Azure services you are using.  Azure Monitor is a monitoring data pipeline for your Azure environment, and offers that data directly into Log Analytics as well as 3rd party tools where you can gain insight into that data and combine it with data from on premises or other cloud resources.

The data presented by Azure Monitor can be accessed using the Azure Portal, Azure CLI, and a RESTful API.

## Exercise 5 - Gather information from Azure Advisor to control spend

Azure Advisor is a personalised cloud consultant that helps you follow best practices to optimise your Azure deployments.  It analyses your resource configuration and usage telemetry.  It then recommends solutions to help improve the performance, security, and high availability of your resources while looking for opportunities to reduce your overall Azure spend.

With Advisor, you can:

- Get proactive, actionable, and personalised best practices recommendations.
- Improve the performance, security, and high availability of your resources, as you identify opportunities to reduce your overall Azure spend.
- Get recommendations with proposed actions inline.

The recomendations that Azure Advisor provides can be accessed using the Azure Portal.

In this exercise we will be using the Azure Portal view high availability recommendations.




## Exercise 6 - Gather information from Log Analytics

Log Analytics (formerly known as/aka OMS Log Analytics) is an Azure service that ingests log and metric data from Azure services (via Azure Monitor), Azure VMs, and on-premises or other cloud infrastructure and offers flexible log search and out-of-the box analytics on top of this data.  It provides rich tools to analyse data across sources, allows complex queries across all logs, and can proactively alert on specified conditions.  You can even collect custom data into its central repository so you can query and visualise it.  You can also take advantage of Log Analytic's built-in solutions to immediately gain insights into the security and functionality of your infrastructure.

In this exercise we will be... 


## Exercise 7 - Explore App Insights and Security Center

Application Insights offers application performance monitoring and user analytics.  It monitors the code that you've written and applications that you've deployed on Azure, on-premises, or other clouds.  By instrumenting your application with the Application Insights SDK you can get access to a range of data including response times of dependencies, exception traces, debugging snapshots, and execution profiles.  It provides powerful tools for analysing this application telemetry while developing and operating your application.  It deeply integrates with Visual Studio to enable you to get right to the problem line(s) of code so you can fix it, and offers usage analytics to analyse customer usage of your applications for product managers as well.

In this exercise we will be...

