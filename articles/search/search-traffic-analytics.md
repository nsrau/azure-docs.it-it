---
title: Telemetria per l'analisi del traffico di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare l'analisi del traffico di ricerca per Ricerca cognitiva di Azure, raccogliere dati di telemetria ed eventi avviati dall'utente tramite Application Insights e quindi analizzare i risultati in un report di Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258210"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Raccogliere dati di telemetria per l'analisi del traffico di ricercaCollect telemetry data for search traffic analytics

L'analisi del traffico di ricerca è un modello per la raccolta di dati di telemetria sulle interazioni degli utenti con l'applicazione Ricerca cognitiva di Azure, ad esempio gli eventi clic avviati dall'utente e gli input da tastiera. Utilizzando queste informazioni, è possibile determinare l'efficacia della soluzione di ricerca, inclusi i termini di ricerca più diffusi, il tasso di clickthrough e gli input delle query che restituiscono zero risultati.

Questo modello accetta una dipendenza [da Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (una funzionalità di Monitoraggio di [Azure](https://docs.microsoft.com/azure/azure-monitor/)) per raccogliere i dati utente. È necessario aggiungere la strumentazione al codice client, come descritto in questo articolo. Infine, è necessario un meccanismo di reporting per analizzare i dati. È consigliabile Power BI, ma è possibile usare il dashboard dell'applicazione o qualsiasi strumento che si connette ad Application Insights.We recommend Power BI but you can use the Application Dashboard or any tool that connects to Application Insights.

> [!NOTE]
> Il modello descritto in questo articolo è per scenari avanzati e dati clickstream generati dal codice aggiunto al client. Al contrario, i log del servizio sono facili da configurare, forniscono una gamma di metriche e possono essere eseguiti nel portale senza codice richiesto. L'abilitazione della registrazione diagnostica è consigliata per tutti gli scenari. Per ulteriori informazioni, consultate [Raccogliere e analizzare i dati di log.](search-monitor-logs.md)

## <a name="identify-relevant-search-data"></a>Identificare i dati di ricerca rilevanti

Per avere metriche utili per l'analisi del traffico di ricerca, è necessario registrare alcuni segnali dagli utenti dell'applicazione di ricerca. Questi segnali indicano contenuti che gli utenti sono interessati e che considerano rilevanti. Per l'analisi del traffico di ricerca, sono inclusi:

+ Eventi di ricerca generati dall'utente: sono interessanti solo le query di ricerca avviate da un utente. Le richieste di ricerca utilizzate per popolare facet, contenuti aggiuntivi o informazioni interne non sono rilevanti e anzi pregiudicano la correttezza dei risultati.

+ Eventi clic generati dall'utente: in una pagina dei risultati di ricerca, un evento clic indica in genere che un documento è un risultato rilevante per una query di ricerca specifica.

Collegando gli eventi di ricerca e clic con un ID di correlazione, si otterrà una comprensione più approfondita delle prestazioni della funzionalità di ricerca dell'applicazione.

## <a name="add-search-traffic-analytics"></a>Aggiungere Analisi del traffico di ricerca

Nella pagina del [portale](https://portal.azure.com) per il servizio Ricerca cognitiva di Azure, la pagina Analisi del traffico di ricerca contiene un foglio di controllo per seguire questo modello di telemetria. Da questa pagina è possibile selezionare o creare una risorsa di Application Insights, ottenere la chiave di strumentazione, copiare frammenti che è possibile adattare alla soluzione e scaricare un report di Power BI basato sullo schema riflesso nel modello.

![Pagina Analisi traffico di ricerca nel portale](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pagina Analisi traffico di ricerca nel portale")

## <a name="1---set-up-application-insights"></a>1 - Configurare Application Insights

Selezionare una risorsa di Application Insights esistente o [crearne una,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) se non ne è già presente. Se si usa la pagina Analisi del traffico di ricerca, è possibile copiare la chiave di strumentazione necessaria per connettersi ad Application Insights.If you use the Search Traffic Analytics page, you can copy the instrumentation key your application needs to connect to Application Insights.

Dopo aver a che fare con una risorsa di Application Insights, puoi seguire [le istruzioni per le lingue e le piattaforme supportate](https://docs.microsoft.com/azure/azure-monitor/app/platforms) per registrare la tua app. La registrazione è semplicemente l'aggiunta della chiave di strumentazione da Application Insights al codice, che imposta l'associazione. È possibile trovare la chiave nel portale o nella pagina Analisi del traffico di ricerca quando si seleziona una risorsa esistente.

Un collegamento che funziona per alcuni tipi di progetto di Visual Studio si riflette nei passaggi seguenti. Crea una risorsa e registra l'app in pochi clic.

1. Per lo sviluppo di Visual Studio e ASP.NET, aprire la soluzione e selezionare **Project** > **Add Application Insights Telemetry**.

1. Fare clic su **Per iniziare**.

1. Registrare l'app fornendo un account Microsoft, una sottoscrizione di Azure e una risorsa Application Insights (una nuova risorsa è quella predefinita). Fare clic su **Registra**.

A questo punto, l'applicazione è configurata per il monitoraggio dell'applicazione, il che significa che tutti i carichi di pagina vengono registrati con le metriche predefinite. Per altre informazioni sui passaggi precedenti, vedere Abilitare la telemetria lato server di Application Insights.For more information about the previous steps, see [Enable Application Insights server-side telemetry](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Aggiungere la strumentazione

Questo passaggio consente di instrumentare un'applicazione di ricerca personalizzata usando la risorsa Application Insights creata nel passaggio precedente. Esistono quattro passaggi per questo processo, a partire dalla creazione di un client di telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passaggio 1: Creare un client di telemetriaStep 1: Create a telemetry client

Creare un oggetto che invia eventi ad Application Insights.Create an object that sends events to Application Insights. È possibile aggiungere la strumentazione al codice dell'applicazione lato server o al codice lato client in esecuzione in un browser, espressa qui come varianti di C e JavaScript (per altri linguaggi, vedere l'elenco completo delle [piattaforme e framework supportati.](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) Scegliere l'approccio che fornisce la profondità desiderata di informazioni.

La telemetria lato server acquisisce le metriche a livello di applicazione, ad esempio nelle applicazioni in esecuzione come servizio Web nel cloud o come app locale in una rete aziendale. I dati di telemetria sul lato server acquisiscono gli eventi di ricerca e clic, la posizione di un documento nei risultati e le informazioni sulle query, ma l'ambito della raccolta dati verrà definito in base alle informazioni disponibili a tale livello.

Nel client potrebbe essere presente codice aggiuntivo che modifica gli input delle query, aggiunge la navigazione o include il contesto (ad esempio, le query avviate da una home page e una pagina del prodotto). Se questo descrive la soluzione, è possibile optare per la strumentazione lato client in modo che i dati di telemetria riflettano i dettagli aggiuntivi. La modalità di raccolta di questo dettaglio aggiuntivo va oltre l'ambito di questo modello, ma è possibile esaminare [Application Insights per](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) le pagine Web per ottenere maggiore direzione. 

**Usare C#**

Per c'è, il **InstrumentationKey** si trova nella configurazione dell'applicazione, ad esempio appsettings.json se il progetto è ASP.NET. Fare riferimento alle istruzioni di registrazione se non si è sicuri della posizione chiave.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Usare JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Passaggio 2: Richiedere un ID di ricerca per la correlazioneStep 2: Request a Search ID for correlation

Per correlare le richieste di ricerca con i clic, è necessario disporre di un ID di correlazione che correla questi due eventi distinti. Ricerca cognitiva di Azure offre un ID di ricerca quando viene richiesto con un'intestazione HTTP.

L'ID di ricerca consente la correlazione delle metriche generate da Ricerca cognitiva di Azure per la richiesta stessa, con le metriche personalizzate che si stanno registrando in Application Insights.Having the search ID allows correlation of the metrics mitted by Azure Cognitive Search for the request itself, with the custom metrics you are logging in Application Insights.  

**Usare C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Usare JavaScript (chiamando le API REST)Use JavaScript (calling REST APIs)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passaggio 3: Registrare gli eventi di ricercaStep 3: Log Search events

Ogni volta che un utente invia una richiesta di ricerca, è necessario registrarlo come evento di ricerca con lo schema seguente in un evento personalizzato di Application Insights.Every time that a search request is issued by a user, you should log that as a search event with the following schema on an Application Insights custom event. Ricordarsi di registrare solo le query di ricerca generate dall'utente.

+ **SearchServiceName**: (stringa) nome del servizio di ricerca
+ **SearchId**: (guid) identificatore univoco della query di ricerca (viene fornito nella risposta di ricerca)
+ **IndexName**: indice del servizio di ricerca (stringa) su cui eseguire la query
+ **QueryTerms**: (stringa) termini di ricerca immessi dall'utente
+ **ResultCount**: numero (int) di documenti restituiti (viene fornito nella risposta di ricerca)
+ **ScoringProfile**: (stringa) nome del profilo di punteggio utilizzato, se presente

> [!NOTE]
> Richiedere il conteggio delle query generate dall'utente aggiungendo $count , true, alla query di ricerca. Per altre informazioni, vedere [Documenti di ricerca (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Usare C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Usare JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Passo 4: Registrare gli eventi Click

Ogni clic di un utente su un documento è un segnale che deve essere registrato a scopo di analisi delle ricerche. Utilizzare gli eventi personalizzati di Application Insights per registrare questi eventi con lo schema seguente:

+ **NomeServizio**: (stringa) nome del servizio di ricercaServiceName : (string) search service name
+ **SearchId**: (guid) identificatore univoco della query di ricerca correlata
+ **DocId**: (stringa) identificatore del documento
+ **Posizione**: (int) classificazione del documento nella pagina dei risultati di ricerca

> [!NOTE]
> Position fa riferimento all'ordine cardinale nell'applicazione. È possibile impostare questo numero, purché si tratti sempre dello stesso, per consentire il confronto.
>

**Usare C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Usare JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Eseguire l'analisi in Power BI

Dopo aver instrumentato l'app e verificato che l'applicazione sia connessa correttamente a Application Insights, si scarica un modello di report predefinito per analizzare i dati nel desktop di Power BI. Il report contiene grafici e tabelle predefiniti utili per analizzare i dati aggiuntivi acquisiti per l'analisi del traffico di ricerca.

1. Nel riquadro di spostamento a sinistra del dashboard di Ricerca cognitiva di Azure, in **Impostazioni**, fare clic su **Analisi del traffico**di ricerca .

1. Nella pagina **Analisi del traffico di ricerca**, nel passaggio 3, fare clic su **Scarica Power BI Desktop** per installare Power BI.

   ![Ottenere report di Power BIGet Power BI reports](./media/search-traffic-analytics/get-use-power-bi.png "Ottenere report di Power BIGet Power BI reports")

1. Nella stessa pagina fare clic su **Scarica report di Power BI.**

1. Il report viene aperto in Power BI Desktop e viene richiesto di connettersi a Application Insights e di fornire le credenziali. È possibile trovare informazioni sulla connessione nelle pagine del portale di Azure per la risorsa Application Insights.You can find connection information in the Azure portal pages for your Application Insights resource. Per le credenziali, specificare lo stesso nome utente e la stessa password utilizzati per l'accesso al portale.

   ![Connetti a Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Connetti a Application Insights")

1. Fare clic su **Carica**.

Il report contiene grafici e tabelle che consentono di prendere decisioni più informate per migliorare le prestazioni di ricerca e la rilevanza.

Sono incluse le metriche seguenti:

+ Volume di ricerca e coppie termine-documento più popolari: termini che generano lo stesso documento su cui è stato fatto clic, ordinati in base ai clic.
+ Ricerche senza clic: termini delle query principali per i quali non sono registrati clic.

Nella schermata seguente viene illustrato l'aspetto di un report predefinito se sono stati utilizzati tutti gli elementi dello schema.

![Power BI dashboard for Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI dashboard for Azure Cognitive Search")

## <a name="next-steps"></a>Passaggi successivi

Instrumentare l'applicazione di ricerca per ottenere dati estremamente utili e dettagliati relativi al servizio di ricerca.

È possibile trovare altre informazioni su [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/) per conoscere i diversi livelli di servizio.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power BI Desktop.See Getting started with Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) for details.