﻿<a name="HOLTop"></a>
# Introduction to Azure IoT #

---

<a name="Overview"></a>
## Overview ##

**Azure IoT Hub** is an Azure service that enables secure and reliable bi-directional communications between your application back end and millions of devices. It allows the application back end to receive telemetry at scale from your devices, route that data to a stream event processor, and also to send cloud-to-device commands to specific devices.

In this module, you'll learn to connect devices to Microsoft Azure, and to implement great IoT solutions taking advantage of Microsoft Azure advanced analytic services such as Azure Stream Analytics.


<a name="Objectives"></a>
### Objectives ###
In this module, you'll see how to:

- Configure Azure IoT Hub to send telemetry from your Windows 10 IoT core device
- Use Azure Stream Analytics to process the data from your device
- Create a dashboard in Power BI to analyze the data
- Consume the IoT hub data from an Azure Web Site

<a name="Prerequisites"></a>
### Prerequisites ###

The following is required to complete this module:

- Windows 10 with [developer mode enabled][1]
- [Visual Studio Community 2015][2] with [Update 1][3] or greater
- [Windows IoT Core Project Templates][4]
- [Azure Device Explorer][7].
- (Optional) [Raspberry PI board with Windows IoT Core image][5]
- (Optional) [GHI FEZ HAT][6] (for exercises 3 and 4)

[1]: https://msdn.microsoft.com/library/windows/apps/xaml/dn706236.aspx
[2]: https://www.visualstudio.com/products/visual-studio-community-vs
[3]: http://go.microsoft.com/fwlink/?LinkID=691134
[4]: https://visualstudiogallery.msdn.microsoft.com/55b357e1-a533-43ad-82a5-a88ac4b01dec
[5]: https://ms-iot.github.io/content/en-US/win10/RPI.htm
[6]: https://www.ghielectronics.com/catalog/product/500
[7]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

> **Note:** You can take advantage of the [Visual Studio Dev Essentials]( https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx) subscription in order to get everything you need to build and deploy your app on any platform.

<a name="Setup"></a>
### Setup ###
In order to run the exercises in this module, you'll need to set up your environment first.

1. Open Windows Explorer and browse to the module's **Source** folder.
1. Right-click **Setup.cmd** and select **Run as administrator** to launch the setup process that will configure your environment and install the Visual Studio code snippets for this module.
1. If the User Account Control dialog box is shown, confirm the action to proceed.

> **Note:** Make sure you've checked all the dependencies for this module before running the setup.

<a name="CodeSnippets"></a>
### Using the Code Snippets ###

Throughout the module document, you'll be instructed to insert code blocks. For your convenience, most of this code is provided as Visual Studio Code Snippets, which you can access from within Visual Studio 2015 to avoid having to add it manually.

>**Note**: Each exercise is accompanied by a starting solution located in the **Begin** folder of the exercise that allows you to follow each exercise independently of the others. Please be aware that the code snippets that are added during an exercise are missing from these starting solutions and may not work until you have completed the exercise. Inside the source code for an exercise, you'll also find an **End** folder containing a Visual Studio solution with the code that results from completing the steps in the corresponding exercise. You can use these solutions as guidance if you need additional help as you work through this module.

---

<a name="Exercises"></a>
## Exercises ##
This module includes the following exercises:

1. [Setting up the app to send data to Azure](#Exercise1)
1. [Using Stream Analytics to process the data](#Exercise2)
1. [Analyzing telemetry data using Power BI](#Exercise3)
1. [(Optional) Consuming the IoT Hub data from a Website](#Exercise4)

Estimated time to complete this module: **60 minutes**

>**Note:** When you first start Visual Studio, you must select one of the predefined settings collections. Each predefined collection is designed to match a particular development style and determines window layouts, editor behavior, IntelliSense code snippets, and dialog box options. The procedures in this module describe the actions necessary to accomplish a given task in Visual Studio when using the **General Development Settings** collection. If you choose a different settings collection for your development environment, there may be differences in the steps that you should take into account.

<a name="Exercise1"></a>
### Exercise 1: Setting up the app to send data to Azure ###

In this exercise, you'll prepare an Universal application to send telemetry data (temperature and light level) to Azure IoT Hub.

<a name="Ex1Task1"></a>
#### Task 1 - Creating an IoT Hub ####

In this task, you'll create an IoT Hub for communicating with your device (the device will be simulated locally using the Universal app).

1. Go the Azure portal, by navigating to http://portal.azure.com

1. Create a new IoT Hub. To do this, click **New** in the jumpbar, then click **Internet of Things**, then click **Azure IoT Hub**.

1. Configure the **IoT hub** with the desired information:

 - Enter a **Name** for the hub e.g. _iot-workshop_,
 - Select a **Pricing and scale tier** (_F1 Free_ tier is enough),
 - Create a new resource group, or select and existing one. For more information, see [Using resource groups to manage your Azure resources](https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/).
 - Select the **Region** _West US_ for where the service will be located.

	![New IoT Hub Settings](Images/new-iot-hub-settings.png?raw=true "New IoT Hub settings")

	_New IoT Hub Settings_

1. It can take a few minutes for the IoT hub to be created. Once it's ready, open the blade of the new IoT hub, take note of the **hostname**. Select the key icon at the top to access the shared access policy settings:

	![IoT hub shared access policies](Images/iot-hub-shared-access-policies.png?raw=true "IoT hub shared access policies")

	_IoT hub shared access policies_

1. Select the Shared access policy called **iothubowner**, and take note of the **Primary key** and **connection string** in the right blade.  You should copy these into a text file since you'll need them later.

	![Get IoT Hub owner connection string](Images/get-iot-hub-owner-connection-string.png?raw=true "Get IoT Hub owner connection string")

	_Get IoT Hub owner connection string_

<a name="Ex1Task2"></a>
#### Task 2 - Registering your device ####

You must register your device in order to be able to send and receive information from the Azure IoT Hub. This is done by registering a [Device Identity](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/#device-identity-registry) in the IoT Hub.

1. Open the Device Explorer app (C:\Program Files (x86)\Microsoft\DeviceExplorer\DeviceExplorer.exe), and fill the **IoT Hub Connection String** field with the connection string of the IoT Hub you created in previous steps, and click **Update**.

	![Configure Device Explorer](Images/configure-device-explorer.png?raw=true "Configure Device Explore")

	_Configure Device Explore_

1. Go to the **Management** tab and click **Create**. The Create Device popup will be displayed. Fill the **Device ID** field with a new Id for your device (_myRaspberryDevice_ for example) and click **Create**:

	![Creating a Device Identity](Images/creating-a-device-identity.png?raw=true "Creating a Device Identity")

	_Creating a Device Identity_

1. Once the device identity is created, it will be displayed on the grid. Right click the identity you just created, select **Copy connection string for selected device** and take note of the value copied to your clipboard, since it will be required to connect your device with the IoT Hub.

	![Copying Device connection information](Images/copying-device-connection-information.png?raw=true "Copying Device connection information")

	_Copying Device connection information_

	> **Note:** The device identities registration can be automated using the Azure IoT Hubs SDK. An example of how to do that can be found [here](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-csharp-csharp-getstarted/#create-a-device-identity).


<a name="Ex1Task4"></a>
#### Task 3 - Sending telemetry data to the Azure IoT hub ####

Now that the device is configured, you'll see how to make an application read the values of the FEZ HAT sensors, and then send those values to an Azure IoT Hub.

This task uses an existing Universal application that simulates the sensor values so you can run it locally. Alternatively, you can switch the **UseMockedSensors** flag to **false** and deploy the app to your Raspberry Pi device and use the real FEZ HAT sensors.

1. Open in Visual Studio the **IoTWorkshop.sln** solution located at **Source\Ex1\Begin** folder.

1. In **Solution Explorer**, right-click the **IoTWorkshop** project, and then click **Manage NuGet Packages**.

1. In the **NuGet Package Manager** window, search for **Microsoft Azure Devices**, click Install to install the **Microsoft.Azure.Devices.Client** package, and accept the terms of use.

    This downloads, installs, and adds a reference to the [Microsoft Azure IoT Service](https://www.nuget.org/packages/Microsoft.Azure.Devices/) SDK NuGet package.

1. Add the following _using_ statements at the top of the **MainPage.xaml.cs** file:

	(Code Snippet - _AzureIoT - Using_)

	````C#
	using Microsoft.Azure.Devices.Client;
	````

1. Add the following field to the **MainPage** class, replace the placeholder value with the device connection string you've created in the previous task:

	(Code Snippet - _AzureIoT - DeviceClientConn_)

	````C#
	private DeviceClient deviceClient = DeviceClient.CreateFromConnectionString("{device connection string}");
	````

1. Add the following method to the **MainPage** class to create and send messages to the IoT hub:

	(Code Snippet - _AzureIoT - SendMessage_)

	````C#
	public async void SendMessage(string message)
	{
		 // Send message to an IoT Hub using IoT Hub SDK
		 try
		 {
			  var content = new Message(Encoding.UTF8.GetBytes(message));
			  await deviceClient.SendEventAsync(content);

			  Debug.WriteLine("Message Sent: {0}", message, null);
		 }
		 catch (Exception e)
		 {
			  Debug.WriteLine("Exception when sending message:" + e.Message);
		 }
	}
	````

1. Add the following code to the **Timer_Tick** method to send a message with the temperature and another with the light level:

	(Code Snippet - _AzureIoT - TempAndLightMessages_)

	````C#
	// send data to IoT Hub
	var jsonMessage = string.Format("{{ displayname:null, location:\"USA\", organization:\"Fabrikam\", guid: \"41c2e437-6c3d-48d0-8e12-81eab2aa5013\", timecreated: \"{0}\", measurename: \"Temperature\", unitofmeasure: \"C\", value:{1}}}",
		 DateTime.UtcNow.ToString("o"),
		 temp);

	this.SendMessage(jsonMessage);

	jsonMessage = string.Format("{{ displayname:null, location:\"USA\", organization:\"Fabrikam\", guid: \"41c2e437-6c3d-48d0-8e12-81eab2aa5013\", timecreated: \"{0}\", measurename: \"Light\", unitofmeasure: \"L\", value:{1}}}",
		 DateTime.UtcNow.ToString("o"),
		 light);

	this.SendMessage(jsonMessage);
	````

1. Press **F5** to run and debug the application.

	The information being sent can be monitored using the Device Explorer application. Run the application and go to the **Data** tab and select the name of the device you want to monitor (_myRaspberryDevice_ in your case), then click on **Monitor**

	![Monitoring messages sent](Images/monitoring-messages-sent.png?raw=true "Monitoring messages sent")

	_Monitoring messages sent_

    Additionally, you can change the **UseMockedSensors** constant value to **false** and deploy the app to a Raspberry Pi device with the FEZ HAT to use real sensors.

<a name="Exercise2"></a>
### Exercise 2: Using Stream Analytics to process the data ###

You have seen how to use the **Device Explorer** to peek at the data being sent to the Azure IoT Hub. However, the Azure IoT suite offers many different ways to generate meaningful information from the data gathered by the devices.

In this exercise you will use Azure Stream Analytics to process the data and generate meaningful information.

<a name="Ex2Task1" />
#### Task 1 - Create a Service Bus Consumer Group ####
In order to allow several consumer applications to read data from the IoT Hub independently, a **Consumer Group** must be configured for each one. If all of the consumer applications (the Device Explorer, Stream Analytics / Power BI, the Web site you'll configure in the next section) read the data from the default consumer group, only one application will retain the lease and the others will be disconnected.

In this task, you'll create a new Consumer Group that will be used by a Stream Analytics job.

1. Open the Azure Portal (https://portal.azure.com/), and select the IoT Hub you created.

1. From the settings blade, click **Messaging**.

1. At the bottom of the **Messaging** blade, type the name of the new **Consumer Group**: "PowerBI"

    ![Create Consumer Group](Images/create-consumer-group.png?raw=true "Create Consumer Group")

	_Create Consumer Group_

1. From the top menu, click **Save**.

<a name="Ex2Task2"></a>
#### Task 2 - Creating a Stream Analytics Job ####
Before the information can be consumed (for instance using **Power BI**), it must be processed by a **Stream Analytics Job**. To do so, an input for that job must be provided. As the Raspberry devices are sending information to an IoT Hub, we'll use these as the input for the job.

> **Note:** You will create the Stream Analytics job using the [classic Azure Portal](https://manage.windowsazure.com) since the _Power BI_ sink for Stream Analytics output is not available in the new Azure Portal yet.

1. Go to the classic Azure Portal (https://manage.windowsazure.com/), and click **NEW** on the bottom bar.

1. Then select **DATA SERVICES > STREAM ANALYTICS > QUICK CREATE** and enter the required fields:
	- **Job Name**: Enter a job name.
	- **Region**: Select the region where you want to run the job. Consider placing the job and the event hub in the same region to ensure better performance and to ensure that you will not be paying to transfer data between regions.
	- **Subscription**: If you have more than one subscription associated to your account, select the one where you created the IoT hub.
	- **Regional Monitoring Storage Account**: Choose the Azure storage account that you would like to use to store monitoring data for all Stream Analytics jobs running within this region. You have the option to choose an existing storage account or to create a new one.

	![Creating Stream Analytics job](Images/new-stream-analytics.png?raw=true "Creating Stream Analytics job")

	_Creating Stream Analytics job_

1. The new job will be shown with a status of **Created**. Notice that the Start button is disabled. You must configure the job **Input**, **Output**, and **Query** before you can start the job.

<a name="Ex2Task3"></a>
#### Task 3 - Defining the Stream Analytics Job Input ####

In this task, you'll specify a job Input using the IoT Hub you previously created.

1. Go to your Stream Analytics job, click on the **INPUTS** tab and then in the **ADD AN INPUT** button.

1. In the **Add an input to your job** popup, select the **Data Stream** option and click **Next**. In the following step, select the option **IoT Hub** and click **Next**. Lastly, in the **IoT Hub Settings** screen, provide the following information:
	- **Input Alias**: TelemetryHUB
	- **Subscription:** Use IoT Hub from Current Subscription
	- **Choose an IoT Hub**: _Use the one you used for the IoT Hub you created_
	- **IoT Hub Shared Access Policy Name**: iothubowner
	- **Shared access policy key**: _The primary key of the iothubowner access policy_
	- **IoT Hub Consumer Group**: powerbi

	![Creating Stream Analytics Input](Images/new-stream-analytics-input.png?raw=true "Creating Stream Analytics Input")

	_Creating Stream Analytics Input_


4. Click **Next**, and then **Complete** (leave the Serialization settings as they are).

<a name="Ex2Task4" />
#### Task 4 - Defining the Stream Analytics Job Output ####
The output of the Stream Analytics job will be **Power BI**.

1. In your Stream Analytics job click **OUTPUTS** tab, and click the **ADD AN OUTPUT** link. 

1. In the **Add an output to your job** popup, select the **POWER BI** option and the click the **Next button**.

1. In the following screen you will setup the credentials of your Power BI account in order to allow the job to connect and send data to it. Click the **Authorize Now** link. You will be redirected to the Microsoft login page.

1. Enter your Power BI account email and password and click **Continue**. If the authorization is successful, you will be redirected back to the **Microsoft Power BI Settings** screen.

1. In this screen you will enter the following information:

	- **Output alias**: PowerBI
	- **Dataset Name**: Raspberry
	- **Table Name**: Telemetry
	- **Workspace**: My Workspace

1. Click **Complete** to create the output.

    <!-- TODO: update this screenshot when Power BI sink available in new portal -->

	![Creating Stream Analytics Output](Images/new-stream-analytics-output.png?raw=true "Creating Stream Analytics Output")

	_Creating Stream Analytics Output_

<a name="Ex2Task5"></a>
#### Task 5 - Defining the Stream Analytics Job Query ####
Now that the job's inputs and outputs are configured, the Stream Analytics Job needs to know how to transform the input data into the output data source. To do so, you'll create a new Query.

1. Go to the Stream Analytics Job **QUERY** tab and replace the query with the following statement:

	````SQL
	SELECT
		 iothub.iothub.connectiondeviceid displayname,
		 location,
		 guid,
		 measurename,
		 unitofmeasure,
		 Max(timecreated) timecreated,
		 Avg(value) AvgValue
	INTO
		 [PowerBI]
	FROM
		 [TelemetryHUB] TIMESTAMP by timecreated
	GROUP BY
		 iothub.iothub.connectiondeviceid, location, guid, measurename, unitofmeasure,
		 TumblingWindow(Second, 10)
	````

	The query takes the data from the input (using the alias defined when the input was created **TelemetryHUB**) and inserts into the output (**PowerBI**, the alias of the output) after grouping it by using a 10 second window.

	![Creating Stream Analytics Query](Images/stream-analytics-query.png?raw=true "Creating Stream Analytics Query")

	_Creating Stream Analytics Query_

1. Click on the **SAVE** button and **YES** in the confirmation dialog.

<a name="Ex2Task6"></a>
#### Task 6 - Starting the Stream Analytics Job ####
In this task, you'll run the Stream Analytics job and view the output in Visual Studio.

1. Click the **START** button at the bottom bar.

1. In the **Start Output** popup, select the **JOB START TIME** option. After clicking **OK** the job will be started. The job status will change to Starting; it can take several minutes to start.

    Once the job starts it creates the Power BI datasource associated with the given subscription.

<a name="Exercise3"></a>
### Exercise 3: Analyzing the telemetry data using Power BI ###
Power BI is a cloud based data analysis, which can be used for reporting and data analysis from wide range of data source. Power BI is simple and user friendly enough that business analysts and power users can work with it and get benefits of it.

In this exercise, you'll create reports in Power BI to visualize the telemetry data processed by Stream Analytics and sent by the device through the Azure IoT hub.

<a name="Ex3Task1"></a>
#### Task 1 - Creating a Light By Time report ####
After some minutes of the job running, you'll see that the dataset that you configured as an output for the Job, is now displayed in the Power BI workspace Datasets section.

1. Go to [PowerBI.com](https://powerbi.microsoft.com/) and login with your work or school account. If the Stream Analytics job query outputs results, you'll see your dataset is already created:

	![Power BI: New Datasource](Images/power-bi-new-datasource.png?raw=true "Power BI: New Datasource")

	_Power BI: New Datasource_

	> **Note:** The Power BI dataset will only be created if the job is running and if it is receiving data from the IoT Hub input, so check that the Universal App is running and sending data to Azure to ensure that the dataset be created.

1. Once the datasource becomes available you can start creating reports. To create a new Report click on the **Raspberry** datasource:

	![Power BI: Report Designer](Images/power-bi-report-designer.png?raw=true "Power BI: Report Designer")

	_Power BI: Report Designer_

	The Report designer will open showing the list of available fields for the selected datasource and the different visualizations supported by the tool.

1. To create the _Average Light by time_ report, select the following fields:

	- avgvalue
	- timecreated

	As you can see the **avgvalue** field is automatically set to the **Value** field and the **timecreated** is inserted as an axis. Now change the chart type to a **Line Chart**:

	![Selecting the Line Chart](Images/select-line-chart.png?raw=true "Selecting the Line Chart")

	_Selecting the Line Chart_

1. To create a filter to show only the Light sensor data, drag the **measurename** field to the **Filters** section, and then select the **Light** value:

	![Select Report Filter](Images/select-report-filter.png?raw=true "Select Report Filter")
	![Select Light sensor values](Images/select-light-sensor-values.png?raw=true "Select Light sensor values")

	_Selecting the Report Filters_

1. Now the report is almost ready. Click **Save** and set _Light by Time_ as the name for the report.

	![Light by Time Report](Images/light-by-time-report.png?raw=true "Light by Time Report")

	_Light by Time Report_

<a name="Ex3Task2"></a>
#### Task 2 - Creating a Power BI dashboard ####
In this task, you'll create a new Dashboard, and pin the _Light by Time_ report to it.

1. Click the plus sign (+) next to the **Dashboards** section to create a new dashboard.

1. Set _Raspberry Telemetry_ as the **Title** and press Enter.

1. Go back to your report, and click the pin icon to add the report to the recently created dashboard.

	![Pin a Report to the Dashboard](Images/pin-a-report-to-the-dashboard.png?raw=true "Pin a Report to the Dashboard")

	_Pinning a Report to the Dashboard_

<a name="Ex3Task3"></a>
#### Task 3 - Creating a Temperature report ####
In this task, you'll create a second chart with the information of the average Temperature.

1. Click the **Raspberry** datasource to create a new report.

1. Select the **avgvalue** field

1. Drag the **measurename** field to the filters section and select **Temperature**

1. Now change the visualization to a **gauge** chart:

	![Gauge visualization](Images/change-visualization-to-gauge.png?raw=true "Gauge visualization")

	_Gauge visualization_

1. Change the **Value** from **Sum** to **Average**

	![Change Value to Average](Images/change-value-to-average.png?raw=true "Change Value to Average")

	_Change Value to Average_

	Now the Report is ready:

	![Gauge Report](Images/gauge-report.png?raw=true "Gauge Report")

	_Gauge Report_

1. Save and then Pin it to the Dashboard.

1. You can create a new _Temperature by Time_  report by repeating the same instructions from Task 1, but selecting **Temperature** from the _measurename_ field, and add it to the dashboard.

1. Lastly, edit the reports name in the dashboard by clicking the pencil icon next to each report.

	![Editing the Report Title](Images/edit-report-title.png?raw=true "Editing the Report Title")

	_Editing the Report Title_

	After renaming both reports, you'll get a dashboard similar to the one in the following screenshot, which will be automatically refreshed as new data arrives.

	![Final Power BI Dashboard](Images/final-power-bi-dashboard.png?raw=true "Final Power BI Dashboard")

	_Final Power BI Dashboard_


<a name="Exercise4"></a>
### Exercise 4: Consuming the IoT Hub data from a Website ###
In this exercise, you'll deploy a website to Azure, and then you'll enable WebSockets to allow communication with the IoT hub, and display live telemetry data using charts.

<a name="Ex4Task1"></a>
#### Task 1 - Create a Consumer Group for the Web site ####
Before you can consume data from the web site, you need to create Consumer Groups to avoid conflicts with the Stream Analytics job.


  > **Note:** In order to use the EventProcessorHost class, you must have an Azure Storage account to enable the EventProcessorHost to record checkpoint information. You can use an existing storage account, or follow the instructions in [About Azure Storage](https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#create-a-storage-account) to create a new one. Make a note of the storage account connection string.

1. Create two different consumer groups (follow steps from [Exercise 2 Task 1](Ex2Task1)):

  - _website_
  - _local_ (used when debugging)

1. Take note of the **Event Hub-compatible name** and **Event Hub-compatible endpoint** values in the _Messaging_ blade

	![Adding website consumer groups](Images/adding-website-consumer-groups.png?raw=true "Adding website consumer groups")

	_Adding website consumer groups_

1. Go to the **Source\Ex4\Begin** folder, and open the Web Site project (_IoTWorkshopWebSite.sln_) in Visual Studio.

1. Edit the _Web.config_ file and add the corresponding values for the following keys:
	- **Microsoft.ServiceBus.EventHubDevices**: Event hub-compatible name you copied in the previous step.
	- **Microsoft.ServiceBus.ConnectionStringDevices**: Event hub-compatible connection string which is composed by the **Event hub-compatible endpoint** and the **_iothubowner_ Shared access policy Primary Key**.
	- **Microsoft.Storage.ConnectionString**: Regional monitoring storage account configured in the Stream Analytics, in this case use the **storage account name** and **storage account primary key** to complete the endpoint.

<a name="Ex4Task2"></a>
#### Task 2 - Deploying to Azure Web Site ####

In this task, you'll deploy the website to an Azure Web Site.

1. In Visual Studio, right-click the project name and select **Publish**.

1. Select **Microsoft Azure Web Apps**.

	![Selecting Publish Target](Images/selecting-publish-target.png?raw=true "Selecting Publish target")

	_Selecting Publish target_

1. Click **New** and use the following configuration.

	- **Web App name**: Pick something unique.
	- **App Service plan**: Select an App Service plan in the same region used for _Stream Analytics_ or create a new one using that region.
	- **Region**: Pick same region as you used for _Stream Analytics_.
	- **Database server**: No database.

1. Click **Create**. After some time the website will be created in Azure.

	![Creating a new Web App on Microsoft Azure](Images/creating-a-new-web-app.png?raw=true "Creating a new Web App on Microsoft Azure")

	_Creating a new Web App on Microsoft Azure_

1. Click **Publish**.

	> **Note:** You might need to install the **WebDeploy** extension if you are having an error stating that the Web deployment task failed. You can find WebDeploy [here](http://www.iis.net/downloads/microsoft/web-deploy).


<a name="Ex4Task3"></a>
#### Task 3 - Enabling WebSockets in the Azure Web Site ####
After you deploy the site, it's required that you enable **Web sockets**. To do this, perform the following steps:

1. Browse to https://portal.azure.com and select your _Azure Web App.

1. Click **All settings**.

1. Click **Applicattion settings**

1. Then set **Web sockets** to **On** and click **Save**.

	![Enabling Web Sockets in your website](Images/enabling-web-sockets.png?raw=true "Enabling Web Sockets in your website")

	_Enabling Web Sockets in your website_


1. Navigate to your recently deployed Web Application. You will see something like in the following screenshot. There will be 2 real-time graphs representing the values read from the temperature and light sensors.

	> **Note:** Take into account that the Universal app must be running and sending information to the IoT Hub in order to see the graphics.

	![Web Site Consuming the IoT Hub Data](Images/web-site-consuming-the-event-hub-data.png?raw=true "Web Site Consuming the IoT Hub Data")

	_Web Site Consuming the IoT Hub data_

	> **Note:** At the bottom of the page you should see "**Connected**". If you see "**ERROR undefined**" you likely didn't enabled **WebSockets** for the Azure Web Site.

---

<a name="Summary"></a>
## Summary ##

By completing this module, you should have:

- Learnt how to create an Azure IoT hub and configure a device app to connect to the hub.
- Created a **Windows Universal Platform** app that sends telemetry messages to Azure IoT Hub.
- Used **Stream Analytics** to process data in near-realtime and spool data to **Blob Storage** and **Power BI**.
- Created a **Power BI** reports and dashboards with graphs to visualize the telemetry data.
- Configured and deployed an **Azure Web site** that consumes and displays the live data from the **Azure IoT hub**.
