---
title: Analisi del traffico di ricerca per Ricerca di Azure | Documentazione Microsoft
description: Abilitare Analisi del traffico di ricerca per Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure, per sbloccare informazioni dettagliate su utenti e dati.
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/05/2017
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 303ca5c820f573dc0b58f1910f258403c3baad2a
ms.lasthandoff: 04/07/2017

---

# <a name="what-is-search-traffic-analytics"></a>Analisi del traffico di ricerca
Analisi del traffico di ricerca è un modello per l'implementazione di un ciclo di feedback per il servizio di ricerca. Questo modello descrive i dati necessari e come raccoglierli utilizzando Application Insights, uno strumento leader di settore per il monitoraggio dei servizi in più piattaforme.

Analisi del traffico di ricerca consente di ottenere visibilità nel servizio di ricerca e informazioni dettagliate su utenti e relativo comportamento. La possibilità di accedere ai dati correlati alle scelte effettuate dagli utenti è utile per prendere decisioni in grado di migliorare ulteriormente l'esperienza di ricerca e per fare un passo indietro se i risultati non sono quelli previsti.

Ricerca di Azure offre una soluzione di telemetria che integra Azure Application Insights e Power BI per offrire funzionalità di rilevamento e monitoraggio avanzate. Poiché l'interazione con Ricerca di Azure avviene solo tramite le API, la telemetria deve essere implementata dagli sviluppatori che utilizzano la funzionalità di ricerca, seguendo le istruzioni riportate in questa pagina.

## <a name="identify-the-relevant-search-data"></a>Identificare i dati di ricerca rilevanti

Per ottenere metriche di ricerca utili, è necessario registrare alcuni segnali provenienti dagli utenti dell'applicazione di ricerca. Questi segnali indicano contenuti che gli utenti trovano interessanti e considerano rilevanti per le proprie esigenze.

I segnali necessari per Analisi del traffico di ricerca sono due:

1. Eventi di ricerca generati dagli utenti. Sono interessanti solo le query di ricerca avviate da un utente. Le richieste di ricerca utilizzate per popolare facet, contenuti aggiuntivi o informazioni interne non sono rilevanti e anzi pregiudicano la correttezza dei risultati.

2. Eventi generati dai clic degli utenti. Il termine "clic" in questo documento si riferisce alla selezione, da parte di un utente, di un determinato risultato di ricerca restituito da una query di ricerca. Un clic in genere indica che un documento è un risultato rilevante per una specifica query di ricerca.

Il collegamento degli eventi di ricerca e degli eventi clic mediante un ID di correlazione consente di analizzare i comportamenti degli utenti nell'applicazione. I registri del traffico di ricerca non sono in grado di offrire informazioni sulla ricerca così dettagliate.

## <a name="how-to-implement-search-traffic-analytics"></a>Come implementare Analisi del traffico di ricerca

I segnali menzionati nella sezione precedente devono essere raccolti dall'applicazione di ricerca quando l'utente interagisce con essa. Application Insights è una soluzione di monitoraggio estensibile, disponibile per più piattaforme, con opzioni di strumentazione flessibili. L'uso di Application Insights consente di usufruire dei report di ricerca Power BI creati da Ricerca di Azure per semplificare l'analisi dei dati.

Nella pagina del [portale](https://portal.azure.com) del servizio Ricerca di Azure il pannello Analisi del traffico di ricerca contiene un foglio informativo utile per seguire questo modello di telemetria. È anche possibile selezionare o creare una risorsa di Application Insights e visualizzare i dati necessari, tutti raccolti in un'unica posizione.

![Istruzioni per Analisi del traffico di ricerca][1]

### <a name="1-select-an-application-insights-resource"></a>1. Selezionare una risorsa di Application Insights

È necessario selezionare una risorsa di Application Insights da usare o crearne una se non è già disponibile. È possibile usare una risorsa già in uso per registrare gli eventi personalizzati necessari.

Quando si crea una nuova risorsa di Application Insights, sono validi tutti i tipi di applicazione per questo scenario. Selezionare quella che meglio si adatta alla piattaforma in uso.

Per creare il client di telemetria per l'applicazione, è necessario disporre della chiave di strumentazione. È possibile ottenerla dal dashboard del portale di Application Insights oppure dalla pagina Analisi del traffico di ricerca selezionando l'istanza che si desidera usare.

### <a name="2-instrument-your-application"></a>2. Instrumentare l'applicazione

Questa è la fase in cui si esegue la strumentazione della propria applicazione di ricerca, utilizzando la risorsa di Application Insights creata al passaggio precedente. Questo processo è suddiviso in quattro passaggi:

**I. Creazione di un client di telemetria** Si tratta dell'oggetto che invia eventi alla risorsa di Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Per altre piattaforme e altri linguaggi, vedere l'[elenco](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) completo.

**II. Richiesta di un ID di ricerca per la correlazione** Per correlare le richieste di ricerca con i clic, è necessario disporre di un ID di correlazione che metta in correlazione questi due eventi distinti. Ricerca di Azure offre un ID di ricerca quando viene richiesto con l'intestazione seguente:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**III. Registrazione degli eventi di ricerca**

Ogni volta che un utente esegue una richiesta di ricerca, è necessario registrarla come evento di ricerca con lo schema seguente in un evento personalizzato di Application Insights:

**ServiceName**: (string) nome del servizio di ricerca **SearchId**: (guid) identificatore univoco della query di ricerca (incluso nella risposta alla ricerca) **IndexName**: (string) indice del servizio di ricerca da sottoporre a query **QueryTerms**: (string) termini di ricerca immessi dall'utente **ResultCount**: (int) numero di documenti restituiti (incluso nella risposta alla ricerca) **ScoringProfile**: (string) nome del profilo di punteggio usato, se disponibile

> [!NOTE]
> Richiedere il conteggio nelle query generate dall'utente mediante l'aggiunta di $count=true alla query di ricerca. Ulteriori informazioni sono disponibili [qui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).
>

> [!NOTE]
> Ricordarsi di registrare solo le query di ricerca generate dagli utenti.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**IV. Registrazione degli eventi clic**

Ogni clic di un utente su un documento è un segnale che deve essere registrato a scopo di analisi delle ricerche. Utilizzare gli eventi personalizzati di Application Insights per registrare questi eventi con lo schema seguente:

**ServiceName**: (string) nome del servizio di ricerca**SearchId**: (guid) identificatore univoco della query di ricerca correlata **DocId**: (string) identificatore del documento **Position**: (int) posizione del documento nella pagina dei risultati della ricerca

> [!NOTE]
> Position fa riferimento all'ordine cardinale nell'applicazione. È possibile impostare questo numero, purché si tratti sempre dello stesso, per consentire il confronto.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.TrackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Eseguire l'analisi con Power BI Desktop

Dopo avere instrumentato l'app e averne verificata la corretta connessione ad Application Insights, è possibile utilizzare un modello predefinito creato da Ricerca di Azure per Power BI Desktop.
Questo modello contiene grafici e tabelle che consentono di prendere decisioni più informate per migliorare le prestazioni di ricerca e la rilevanza.

Per creare un'istanza del modello di Power BI Desktop, sono necessarie tre informazioni su Application Insights. Questi dati sono reperibili nella pagina Analisi del traffico di ricerca, quando si seleziona la risorsa da usare.

![Dati di Application Insights nel pannello Analisi del traffico di ricerca][2]

Metriche incluse nel modello di Power BI Desktop:

*    Tasso di clic (CTR): rapporto tra utenti che fanno clic su un documento specifico e numero di ricerche totali.
*    Ricerche senza clic: termini delle query principali per i quali non sono registrati clic.
*    Documenti con più clic: documenti con più clic suddivisi per ID nelle ultime 24 ore e negli ultimi 7 e 30 giorni.
*    Coppie termine-documento più comuni: termini che producono lo stesso documento selezionato, ordinati per clic.
*    Tempo dei clic: clic con bucket definiti in base al tempo dopo la query di ricerca.

![Modello di Power BI per la lettura da Application Insights][3]


## <a name="next-steps"></a>Passaggi successivi
Instrumentare l'applicazione di ricerca per ottenere dati estremamente utili e dettagliati relativi al servizio di ricerca.

Ulteriori informazioni su Application Insights sono disponibili [qui](https://go.microsoft.com/fwlink/?linkid=842905). Per ulteriori informazioni sui diversi livelli di servizio, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/) di Application Insights.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png

