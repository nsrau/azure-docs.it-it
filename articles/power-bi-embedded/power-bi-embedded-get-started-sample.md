<properties
   pageTitle="Esempio introduttivo"
   description="Power BI Embedded - Uso di SDK per aggiungere report di Power BI interattivi nell'applicazione di Business Intelligence"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/14/2016"
   ms.author="mblythe"/>

# Introduzione all'esempio di Power BI Embedded

**Microsoft Power BI Embedded** consente di integrare i report di Power BI direttamente nelle applicazioni Web o per dispositivi mobili. In questo articolo verrà presentato l'esempio introduttivo per **Power BI Embedded**.

Prima di continuare, è consigliabile salvare le risorse seguenti, che risulteranno utili durante l'integrazione dei report di Power BI nell'app di esempio e nelle app personalizzate.

 -	[App Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Riferimento all'API di Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Power BI Embedded .NET SDK](http://go.microsoft.com/fwlink/?LinkId=746472) (disponibile tramite NuGet)



> [AZURE.NOTE] Prima di poter configurare ed eseguire l'esempio d'introduzione a Power BI Embedded, è necessario creare almeno una **raccolta di aree di lavoro** nella sottoscrizione di Azure. Per informazioni su come creare una **Raccolta di aree di lavoro** nel portale di Azure, vedere [Introduzione a Microsoft Power BI Embedded - Anteprima](power-bi-embedded-get-started.md).

## Configurare l'app di esempio

La procedura dettagliata seguente illustra la configurazione dell'ambiente di sviluppo di Visual Studio per l'accesso ai componenti necessari per eseguire l'app di esempio.

1. Scaricare e decomprimere l'esempio [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) su GitHub.

2. Aprire il file **PowerBI embedded.sln** in Visual Studio. Potrebbe essere necessario eseguire il comando **Update-Package** nella Console di gestione pacchetti NuGET per aggiornare i pacchetti utilizzati in questa soluzione.

3. Compilare la soluzione.

4. Eseguire l'applicazione console **ProvisionSample**. Nell'applicazione console di esempio, effettuare il provisioning di un'area di lavoro e importare un file PBIX.

5. Per effettuare il provisioning di una nuova **Area di lavoro**, selezionare l'opzione 5, **Provision a new workspace in an existing workspace collection**.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)  

6. Immettere il nome della **raccolta di aree di lavoro ** e la **chiave di accesso**. È possibile ottenere questi dati nel **portale di Azure**. Per altre informazioni su come ottenere la **Chiave di accesso**, vedere [Visualizzare le chiavi di accesso all'API Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) nell'introduzione a Microsoft Power BI Embedded.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)  

7. Copiare e salvare l'**ID area di lavoro** appena creato per poterlo usare successivamente. Dopo aver creato l'**ID area di lavoro**, è possibile trovarlo nel **portale di Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)  

8. Per importare un file PBIX nell'**area di lavoro**, selezionare l'opzione **6. Import PBIX Desktop file into an existing workspace**. In mancanza di un file PBIX adatto, è possibile scaricare l'esempio [Retail Analysis PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).

9. Se richiesto, immettere un nome descrittivo per il **set di dati**.

Verrà visualizzata una risposta simile alla seguente:

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] Se il file PBIX contiene connessioni tramite query diretta, eseguire l'opzione 7 per aggiornare le stringhe di connessione.

A questo punto il report di Power BI in formato PBIX è stato importato nell'**area di lavoro**. Viene ora illustrato come eseguire l'app Web dell'esempio introduttivo di **Power BI Embedded**.

## Eseguire l'app Web di esempio

L'app Web di esempio è un dashboard che esegue il rendering dei report importati nell'**area di lavoro**. Di seguito viene spiegato come configurare l'app Web di esempio.

1. Nella soluzione **PowerBI Embedded** in Visual Studio fare clic con il pulsante destro del mouse sull'applicazione Web **EmbedSample** e scegliere **Imposta come progetto di avvio**.
2. In **web.config**, nell'applicazione Web **EmbedSample** modificare il nome **appSettings**: **AccessKey**, **WorkspaceCollection** e il valore **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Eseguire l'applicazione Web **EmbedSample**.

Dopo aver eseguito l'applicazione Web **EmbedSample**, il pannello di spostamento a sinistra conterrà un menu **Reports**. Per visualizzare il report che è stato importato, espandere il menu **Reports** e fare clic su un report. Se è stato ad esempio importato l'[esempio Analyzing Sales Data PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), l'app Web sarà simile alla seguente:

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)  

Dopo aver selezionato un report, l'applicazione Web **EmbedSample** sarà simile alla seguente:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)  


## Esplorare il codice di esempio
L'esempio di **Microsoft Power BI Embedded** è un'app Web dashboard di esempio che illustra come integrare report di **Power BI** nella propria app. Usa un modello di progettazione MVC (Model-View-Controller) per illustrare le procedure consigliate. Questa sezione evidenzia parti del codice di esempio che è possibile esplorare nella soluzione di app Web di **PowerBI Embedded**. Il modello MVC separa la modellazione del dominio, la presentazione e le azioni in base all'input dell'utente in tre classi distinte, ovvero modellazione, visualizzazione e controller. Per altre informazioni sul modello MVC, vedere [Learn About ASP.NET](http://www.asp.net/mvc) (Informazioni su ASP.NET).

Il codice di esempio di **Microsoft Power BI Embedded** è suddiviso come indicato di seguito. Ogni sezione contiene il nome del file nella soluzione PowerBI-embedded.sln in modo che sia possibile trovare facilmente il codice nell'esempio.

> [AZURE.NOTE] Questa sezione è un riepilogo del codice di esempio e illustra in che modo è stato scritto. Per visualizzare l'esempio completo, caricare la soluzione PowerBI-embedded.sln in Visual Studio.

### Modello
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

### Stringa di connessione
La stringa di connessione deve essere nel formato seguente:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

L'uso di attributi comuni di server e database avrà esito negativo. Ad esempio: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### Visualizza
La **visualizzazione** gestisce la visualizzazione di più **report** di Power BI e di un **report** di Power BI.

**Reports.cshtml**: esegue l'iterazione di **Model.Reports** per creare un elemento **ActionLink**. L'elemento **ActionLink** è costituito da:

|Parte:|Descrizione
|---|---
|Titolo| Nome del report.
|QueryString| Collegamento al l'ID report.

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

### Controller

**DashboardController.cs**: crea un'istanza di PowerBIClient per il passaggio di un **token dell'app**. Dalla **Chiave di firma** viene generato un token JSON Web (JWT) per ottenere le **Credenziali**. Le **Credenziali** servono per creare un'istanza di **PowerBIClient**. Dopo aver creato un'istanza di **PowerBIClient**, è possibile chiamare i metodi GetReports() e GetReportsAsync().

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

### Integrare un report nell'app

Dopo aver creato un **report**, usare un **IFrame** per incorporare il **report** di Power BI. Ecco un frammento di codice da powerbi.js nell'esempio di **Microsoft Power BI Embedded**.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)  


## Filtrare i report incorporati nell'applicazione

È possibile filtrare un report incorporato tramite una sintassi dell'URL. A questo scopo aggiungere un parametro della stringa di query **$filter** con un operatore **eq** all'URL iFrame src specificando il filtro. Di seguito è riportata la sintassi della query del filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} non può includere spazi o caratteri speciali. {fieldValue} accetta un singolo valore categorico.


## Vedere anche

- [Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0907_2016-->