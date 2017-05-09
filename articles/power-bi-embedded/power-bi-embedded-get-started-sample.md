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
ms.date: 03/02/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c3cb1763f807220a4a829f410d7eb77974b25776
ms.contentlocale: it-it
ms.lasthandoff: 04/20/2017


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Introduzione all'esempio di Power BI Embedded

**Microsoft Power BI Embedded**consente di integrare i report di Power BI direttamente nelle applicazioni Web o per dispositivi mobili. In questo articolo verrà presentato l'esempio introduttivo per **Power BI Embedded** .

Prima di continuare, è consigliabile salvare le risorse seguenti, che risulteranno utili durante l'integrazione dei report di Power BI nell'app di esempio e nelle app personalizzate.

* [App Web di esempio per l'area di lavoro](http://go.microsoft.com/fwlink/?LinkId=761493)
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
5. Per effettuare il provisioning di una nuova **area di lavoro**, selezionare l'opzione 1 **Collection management** (Gestione raccolte) e quindi selezionare l'opzione 6 **Provision a new Workspace** (Effettua il provisioning di una nuova area di lavoro).
6. Per importare un nuovo **report**, selezionare l'opzione 2 **Report management** (Gestione report) e quindi selezionare l'opzione 3 **Import PBIX Desktop file into a workspace** (Importa file di PBIX Desktop in un'area di lavoro).

7. Immettere il **nome della raccolta di aree di lavoro** e la **chiave di accesso**. È possibile ottenere questi dati nel **portale di Azure**. Per altre informazioni su come ottenere la **Chiave di accesso**, vedere [Visualizzare le chiavi di accesso all'API Power BI](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) nell'introduzione a Microsoft Power BI Embedded.

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
8. Copiare e salvare l' **ID area di lavoro** appena creato per poterlo usare successivamente. Dopo aver creato l'**ID area di lavoro**, è possibile trovarlo nel **portale di Azure**.

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
9. Per importare un file PBIX nell'**area di lavoro**, selezionare l'opzione **6. Import PBIX Desktop file into an existing workspace**. In mancanza di un file PBIX adatto, è possibile scaricare l'esempio [Retail Analysis PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Se richiesto, immettere un nome descrittivo per il **set di dati**.

Verrà visualizzata una risposta simile alla seguente:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Se il file PBIX contiene connessioni tramite query diretta, eseguire l'opzione 7 per aggiornare le stringhe di connessione.

A questo punto il report di Power BI in formato PBIX è stato importato nell'**area di lavoro**. Viene ora illustrato come eseguire l'applicazione Web dell'esempio introduttivo di **Power BI Embedded**.

## <a name="run-the-sample-web-app"></a>Eseguire l'app Web di esempio
L'app Web di esempio è un'applicazione di esempio che esegue il rendering dei report importati nell'**area di lavoro**. Di seguito viene spiegato come configurare l'app Web di esempio.

1. Nella soluzione **PowerBI Embedded** in Visual Studio fare clic con il pulsante destro del mouse sull'applicazione Web **EmbedSample** e scegliere **Imposta come progetto di avvio**.
2. In **web.config**, nell'applicazione Web **EmbedSample** modificare il nome **appSettings**: **AccessKey**, **WorkspaceCollection** e **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Eseguire l'applicazione Web **EmbedSample**.

Dopo aver eseguito l'applicazione Web **EmbedSample**, il pannello di spostamento a sinistra conterrà un menu **Reports** (Report). Per visualizzare il report che è stato importato, espandere il menu **Reports** (Report) e fare clic su un report. Se è stato importato l'[esempio Analyzing Sales Data PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), l'applicazione Web sarà simile alla seguente:

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

Dopo aver selezionato un report, l'applicazione Web **EmbedSample** sarà simile alla seguente:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Esplorare il codice di esempio

L'esempio di **Microsoft Power BI Embedded** è un'app Web che illustra come integrare report di **Power BI** nella propria app. Usa un modello di progettazione MVC (Model-View-Controller) per illustrare le procedure consigliate. Questa sezione evidenzia parti del codice di esempio che è possibile esplorare nella soluzione di applicazione Web di **PowerBI Embedded**. Il modello MVC separa la modellazione del dominio, la presentazione e le azioni in base all'input dell'utente in tre classi distinte, ovvero modellazione, visualizzazione e controller. Per altre informazioni sul modello MVC, vedere [Learn About ASP.NET](http://www.asp.net/mvc) (Informazioni su ASP.NET).

Il codice di esempio di **Microsoft Power BI Embedded** è suddiviso come indicato di seguito. Ogni sezione contiene il nome del file nella soluzione PowerBI-embedded.sln in modo che sia possibile trovare facilmente il codice nell'esempio.

> [!NOTE]
> Questa sezione è un riepilogo del codice di esempio e illustra in che modo è stato scritto. Per visualizzare l'esempio completo, caricare la soluzione PowerBI-embedded.sln in Visual Studio.

### <a name="model"></a>Modello

L'esempio è composto da **ReportsViewModel** e **ReportViewModel**.

**ReportsViewModel.cs**: rappresenta i report di Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: rappresenta un report di Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Stringa di connessione

La stringa di connessione deve essere nel formato seguente:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

L'uso di attributi comuni di server e database avrà esito negativo. Ad esempio: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Visualizza

**Visualizza** gestisce la visualizzazione di più **report** di Power BI e di un **report** di Power BI.

**Reports.cshtml**: esegue l'iterazione di **Model.Reports** per creare un elemento **ActionLink**. L'elemento **ActionLink** è costituito da:

| Parte: | Descrizione |
| --- | --- |
| Titolo |Nome del report. |
| QueryString |Collegamento al l'ID report. |

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

Report.cshtml: imposta **Model.AccessToken** e l'espressione Lambda per **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controller

**DashboardController.cs**: crea un'istanza di PowerBIClient per il passaggio di un **token dell'applicazione**. Dalla **Chiave di firma** viene generato un token JSON Web (JWT) per ottenere le **Credenziali**. Le **Credenziali** servono per creare un'istanza di **PowerBIClient**. Dopo aver creato un'istanza di **PowerBIClient**, è possibile chiamare i metodi GetReports() e GetReportsAsync().

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

### <a name="integrate-a-report-into-your-app"></a>Integrare un report nell'app

Dopo aver creato un **report**, usare un **iFrame** per incorporare il **report** di Power BI. Ecco un frammento di codice da powerbi.js nell'esempio di **Microsoft Power BI Embedded**.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## <a name="filter-reports-embedded-in-your-application"></a>Filtrare i report incorporati nell'applicazione

È possibile filtrare un report incorporato tramite una sintassi dell'URL. A questo scopo aggiungere un parametro della stringa di query **$filter** con un operatore **eq** all'URL iFrame src specificando il filtro. Di seguito è riportata la sintassi della query del filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} non può includere spazi o caratteri speciali. {fieldValue} accetta un singolo valore categorico.  

## <a name="see-also"></a>Vedere anche

[Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)  
[Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Incorporare un report](power-bi-embedded-embed-report.md)  
[Creare un nuovo report da un set di dati](power-bi-embedded-create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)

