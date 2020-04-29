---
title: Telemetria per analisi del traffico di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare analisi del traffico di ricerca per ricerca cognitiva di Azure, raccogliere i dati di telemetria e gli eventi avviati dall'utente usando Application Insights e quindi analizzare i risultati in un report di Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128098"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Raccogliere i dati di telemetria per analisi del traffico di ricerca

Analisi del traffico di ricerca è un modello per la raccolta di dati di telemetria sulle interazioni degli utenti con l'applicazione ricerca cognitiva di Azure, ad esempio gli eventi Click avviati dall'utente e gli input da tastiera. Utilizzando queste informazioni, è possibile determinare l'efficacia della soluzione di ricerca, inclusi i termini di ricerca più diffusi, la velocità di click-through e gli input della query che producono zero risultati.

Questo modello prende una dipendenza da [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (una funzionalità di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)) per raccogliere i dati dell'utente. È necessario aggiungere la strumentazione al codice client, come descritto in questo articolo. Infine, sarà necessario un meccanismo di creazione di report per analizzare i dati. È consigliabile Power BI ma è possibile usare il dashboard dell'applicazione o qualsiasi strumento che si connette a Application Insights.

> [!NOTE]
> Il modello descritto in questo articolo è relativo agli scenari avanzati e ai dati clickstream generati dal codice aggiunto al client. Al contrario, i log del servizio sono facili da configurare, offrono una gamma di metriche e possono essere eseguiti nel portale senza che sia necessario alcun codice. L'abilitazione della registrazione è consigliata per tutti gli scenari. Per altre informazioni, vedere [raccogliere e analizzare i dati di log](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificare i dati di ricerca rilevanti

Per avere metriche utili per l'analisi del traffico di ricerca, è necessario registrare alcuni segnali dagli utenti dell'applicazione di ricerca. Questi segnali indicano il contenuto a cui gli utenti sono interessati e che considerano rilevanti. Per analisi del traffico di ricerca, sono incluse le seguenti:

+ Eventi di ricerca generati dall'utente: sono interessanti solo le query di ricerca avviate da un utente. Le richieste di ricerca utilizzate per popolare facet, contenuti aggiuntivi o informazioni interne non sono rilevanti e anzi pregiudicano la correttezza dei risultati.

+ Eventi click generati dall'utente: in una pagina dei risultati della ricerca, un evento Click indica in genere che un documento è un risultato pertinente per una query di ricerca specifica.

Collegando la ricerca e facendo clic sugli eventi con un ID di correlazione, si otterrà una conoscenza più approfondita delle prestazioni della funzionalità di ricerca dell'applicazione.

## <a name="add-search-traffic-analytics"></a>Aggiungere Analisi del traffico di ricerca

Nella pagina del [portale](https://portal.azure.com) per il servizio ricerca cognitiva di Azure, la pagina analisi del traffico di ricerca contiene un foglio informativo per seguire questo modello di telemetria. Da questa pagina è possibile selezionare o creare una risorsa Application Insights, ottenere la chiave di strumentazione, copiare frammenti che è possibile adattare per la soluzione e scaricare un report Power BI compilato sullo schema riflesso nel modello.

![Cerca Analisi del traffico pagina nel portale](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Cerca Analisi del traffico pagina nel portale")

## <a name="1---set-up-application-insights"></a>1-impostare Application Insights

Selezionare una risorsa Application Insights esistente o [crearne una](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) se non ne è già presente una. Se si usa la pagina Cerca Analisi del traffico, è possibile copiare la chiave di strumentazione necessaria per la connessione dell'applicazione a Application Insights.

Dopo aver creato una risorsa di Application Insights, è possibile seguire [le istruzioni per le lingue e le piattaforme supportate](https://docs.microsoft.com/azure/azure-monitor/app/platforms) per registrare l'app. La registrazione consiste semplicemente nell'aggiungere la chiave di strumentazione da Application Insights al codice, che configura l'associazione. È possibile trovare la chiave nel portale o nella pagina Cerca Analisi del traffico quando si seleziona una risorsa esistente.

Un collegamento adatto per alcuni tipi di progetto di Visual Studio viene riflesso nei passaggi seguenti. Crea una risorsa e registra l'app in pochi clic.

1. Per lo sviluppo di Visual Studio e ASP.NET, aprire la soluzione e selezionare **progetto** > **Aggiungi Application Insights Telemetry**.

1. Fare clic su **Per iniziare**.

1. Registrare l'app fornendo una account Microsoft, una sottoscrizione di Azure e una risorsa Application Insights (una nuova risorsa è l'impostazione predefinita). Fare clic su **Register**.

A questo punto, l'applicazione è configurata per il monitoraggio delle applicazioni, il che significa che tutti i caricamenti di pagina vengono monitorati con le metriche predefinite. Per ulteriori informazioni sui passaggi precedenti, vedere [Enable Application Insights telemetria sul lato server](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Aggiungere la strumentazione

Questo passaggio consente di instrumentare l'applicazione di ricerca usando la risorsa Application Insights creata nel passaggio precedente. Sono disponibili quattro passaggi per questo processo, a partire dalla creazione di un client di telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passaggio 1: creare un client di telemetria

Creare un oggetto che invii gli eventi a Application Insights. È possibile aggiungere strumentazione al codice dell'applicazione lato server o al codice lato client in esecuzione in un browser, espresso qui come varianti C# e JavaScript (per altre lingue, vedere l'elenco completo delle [piattaforme e dei Framework supportati](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Scegliere l'approccio che fornisce la profondità desiderata delle informazioni.

La telemetria sul lato server acquisisce le metriche a livello di applicazione, ad esempio in applicazioni in esecuzione come servizio Web nel cloud o come app locale in una rete aziendale. La telemetria sul lato server acquisisce la ricerca e fa clic sugli eventi, sulla posizione di un documento nei risultati e sulle informazioni sulle query, ma l'ambito della raccolta dei dati sarà limitato a tutte le informazioni disponibili in tale livello.

Sul client, è possibile che si disponga di codice aggiuntivo che manipola gli input della query, aggiunge lo spostamento o include il contesto (ad esempio, le query avviate da un home page rispetto a una pagina del prodotto). Se si descrive la soluzione, è possibile optare per la strumentazione lato client in modo che i dati di telemetria riflettano i dettagli aggiuntivi. Il modo in cui vengono raccolti i dettagli aggiuntivi esula dall'ambito di questo modello, ma è possibile esaminare [Application Insights per le pagine Web](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) per altre direzioni. 

**Usare C#**

Per C#, **InstrumentationKey** si trova nella configurazione dell'applicazione, ad esempio appSettings. JSON se il progetto è ASP.NET. Se non si è certi della posizione della chiave, consultare le istruzioni di registrazione.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>Passaggio 2: richiedere un ID di ricerca per la correlazione

Per correlare le richieste di ricerca con clic, è necessario disporre di un ID correlazione che mette in relazione questi due eventi distinti. Azure ricerca cognitiva fornisce un ID di ricerca quando viene richiesto con un'intestazione HTTP.

L'ID di ricerca consente la correlazione delle metriche emesse da Azure ricerca cognitiva per la richiesta stessa, con le metriche personalizzate che si stanno registrando Application Insights.  

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

**Usare JavaScript (chiamata alle API REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passaggio 3: registrare gli eventi di ricerca

Ogni volta che una richiesta di ricerca viene rilasciata da un utente, è necessario registrarla come un evento di ricerca con lo schema seguente in un Application Insights evento personalizzato. Ricordarsi di registrare solo le query di ricerca generate dall'utente.

+ **SearchServiceName**: (String) nome del servizio di ricerca
+ **SearchId**: (Guid) identificatore univoco della query di ricerca (viene visualizzato nella risposta di ricerca)
+ **IndexName: (** String) indice del servizio di ricerca su cui eseguire una query
+ **QueryTerms**: (String) termini di ricerca immessi dall'utente
+ **Resultcount**: (int) numero di documenti restituiti (viene visualizzato nella risposta di ricerca)
+ **ScoringProfile**: (String) il nome del profilo di punteggio usato, se presente

> [!NOTE]
> Richiedere il numero di query generate dall'utente aggiungendo $count = true alla query di ricerca. Per altre informazioni, vedere [Search Documents (REST)](/rest/api/searchservice/search-documents#counttrue--false).
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

### <a name="step-4-log-click-events"></a>Passaggio 4: eventi Click di log

Ogni clic di un utente su un documento è un segnale che deve essere registrato a scopo di analisi delle ricerche. Utilizzare gli eventi personalizzati di Application Insights per registrare questi eventi con lo schema seguente:

+ Nome del servizio di ricerca **ServiceName**: (String)
+ **SearchId**: (Guid) identificatore univoco della query di ricerca correlata
+ **Docid**: (String) identificatore documento
+ **Position**: (int) rango del documento nella pagina dei risultati della ricerca

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

Dopo aver instrumentato l'app e aver verificato che l'applicazione sia connessa correttamente alla Application Insights, è possibile scaricare un modello di report predefinito per analizzare i dati in Power BI desktop. Il report contiene grafici e tabelle predefiniti utili per analizzare i dati aggiuntivi acquisiti per analisi del traffico di ricerca.

1. Nel riquadro di spostamento a sinistra del dashboard di Azure ricerca cognitiva, in **Impostazioni**, fare clic su **analisi del traffico di ricerca**.

1. Nella pagina **Analisi del traffico di ricerca**, nel passaggio 3, fare clic su **Scarica Power BI Desktop** per installare Power BI.

   ![Ottenere report Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Ottenere report Power BI")

1. Nella stessa pagina fare clic su **scarica Power bi report**.

1. Il report verrà aperto in Power BI Desktop e verrà richiesto di connettersi Application Insights e fornire le credenziali. È possibile trovare le informazioni di connessione nelle pagine portale di Azure per la risorsa Application Insights. Per le credenziali, fornire lo stesso nome utente e la stessa password usati per l'accesso al portale.

   ![Connetti a Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Connetti a Application Insights")

1. Fare clic su **Carica**.

Il report contiene grafici e tabelle che consentono di prendere decisioni più informate per migliorare le prestazioni di ricerca e la rilevanza.

Sono incluse le metriche seguenti:

+ Volume di ricerca e coppie di documenti di termine più diffuse: termini che comportano lo stesso documento selezionato, ordinato per clic.
+ Ricerche senza clic: termini delle query principali per i quali non sono registrati clic.

Lo screenshot seguente mostra l'aspetto di un report incorporato se sono stati usati tutti gli elementi dello schema.

![Dashboard Power BI per Azure ricerca cognitiva](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Dashboard Power BI per Azure ricerca cognitiva")

## <a name="next-steps"></a>Passaggi successivi

Instrumentare l'applicazione di ricerca per ottenere dati estremamente utili e dettagliati relativi al servizio di ricerca.

È possibile trovare altre informazioni su [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/) per conoscere i diversi livelli di servizio.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .