---
title: Esempio introduttivo
description: Power BI Embedded - Uso di SDK per aggiungere report di Power BI interattivi nell&quot;applicazione di Business Intelligence
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 11409a498c14abbc95e52ef793564c254df8ec89
ms.openlocfilehash: 2bd7da95901e7216a84aaa8e1761b491c7217ea3


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Introduzione all'esempio di Power BI Embedded

**Microsoft Power BI Embedded**consente di integrare i report di Power BI direttamente nelle applicazioni Web o per dispositivi mobili. In questo articolo verrà presentato l'esempio introduttivo per **Power BI Embedded** .

Prima di continuare, è consigliabile salvare le risorse seguenti, che risulteranno utili durante l'integrazione dei report di Power BI nell'app di esempio e nelle app personalizzate.

* [App Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Riferimento all'API di Power BI Embedded](https://msdn.microsoft.com/en-US/library/azure/mt711507.aspx)
* [Power BI Embedded .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (disponibile tramite NuGet)
* [Esempio per incorporare il report di JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE] 
> Prima di poter configurare ed eseguire l'esempio d'introduzione a Power BI Embedded, è necessario creare almeno una **raccolta di aree di lavoro** nella sottoscrizione di Azure. Per informazioni su come creare una **Raccolta di aree di lavoro** nel portale di Azure, vedere [Introduzione a Microsoft Power BI Embedded - Anteprima](power-bi-embedded-get-started.md).

## <a name="configure-the-sample-app"></a>Configurare l'app di esempio

La procedura dettagliata seguente illustra la configurazione dell'ambiente di sviluppo di Visual Studio per l'accesso ai componenti necessari per eseguire l'app di esempio.

1. Scaricare e decomprimere l'esempio [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) su GitHub.
2. Aprire il file **PowerBI embedded.sln** in Visual Studio. Potrebbe essere necessario eseguire il comando **Update-Package** nella Console di gestione pacchetti NuGET per aggiornare i pacchetti utilizzati in questa soluzione.
3. Compilare la soluzione.
4. Eseguire l'applicazione console **ProvisionSample** . Nell'applicazione console di esempio, effettuare il provisioning di un'area di lavoro e importare un file PBIX.
5. Per effettuare il provisioning di una nuova **Area di lavoro**, selezionare l'opzione 5, **Provision a new workspace in an existing workspace collection** (Effettua il provisioning di una nuova area di lavoro in una raccolta di aree di lavoro esistenti).

    ![](media/powerbi-embedded-get-started-sample/console-option-5.png)
6. Immettere il **nome della raccolta di aree di lavoro** e la **chiave di accesso**. È possibile ottenere questi dati nel **portale di Azure**. Per altre informazioni su come ottenere la **Chiave di accesso**, vedere [Visualizzare le chiavi di accesso all'API Power BI](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) nell'introduzione a Microsoft Power BI Embedded.

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. Copiare e salvare l' **ID area di lavoro** appena creato per poterlo usare successivamente. Dopo aver creato l'**ID area di lavoro**, è possibile trovarlo nel **portale di Azure**.

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. Per importare un file PBIX nell'**area di lavoro**, selezionare l'opzione **6. Import PBIX Desktop file into an existing workspace**. In mancanza di un file PBIX adatto, è possibile scaricare l'esempio [Retail Analysis PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).
9. Se richiesto, immettere un nome descrittivo per il **set di dati**.

Verrà visualizzata una risposta simile alla seguente:

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## Explore the sample code

The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.

### Model

The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string

The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View

The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app

Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application

You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)

More questions? [Try the Power BI Community](http://community.powerbi.com/)


<!--HONumber=Feb17_HO1-->


