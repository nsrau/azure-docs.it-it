---
title: Report sui dati di analisi del traffico di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare analisi del traffico di ricerca per ricerca cognitiva di Azure, raccogliere i dati di telemetria e gli eventi avviati dall'utente usando Application Insights e quindi analizzare i risultati in un report di Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: b9b0ba85aed4d63fe6bb939c9ed3b99d3e789397
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932567"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementare analisi del traffico di ricerca in Azure ricerca cognitiva

Analisi del traffico di ricerca è un modello per l'implementazione di un ciclo di feedback per il servizio di ricerca. L'obiettivo è raccogliere dati di telemetria sugli eventi Click avviati dall'utente e sugli input da tastiera. Utilizzando queste informazioni, è possibile determinare l'efficacia della soluzione di ricerca, inclusi i termini di ricerca più diffusi, la velocità di click-through e gli input della query che producono zero risultati.

Questo modello prende una dipendenza da [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (una funzionalità di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/)) per raccogliere i dati dell'utente. Sarà inoltre necessario aggiungere strumentazione al codice client, come descritto in questo articolo. Infine, sarà necessario un meccanismo di creazione di report per analizzare i dati. È consigliabile Power BI ma è possibile usare qualsiasi strumento che si connetta a Application Insights.

> [!NOTE]
> Il modello descritto in questo articolo è relativo agli scenari avanzati e ai dati clickstream generati dal client. In alternativa, è possibile creare report sulle informazioni di log generate dal servizio di ricerca. Per altre informazioni sui report del log del servizio, vedere [monitorare l'utilizzo delle risorse e le attività di query](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Identificare i dati di ricerca rilevanti

Per ottenere metriche di ricerca utili, è necessario registrare alcuni segnali provenienti dagli utenti dell'applicazione di ricerca. Questi segnali indicano contenuti che gli utenti trovano interessanti e considerano rilevanti per le proprie esigenze.

I segnali necessari per Analisi del traffico di ricerca sono due:

+ Eventi di ricerca generati dall'utente: sono interessanti solo le query di ricerca avviate da un utente. Le richieste di ricerca utilizzate per popolare facet, contenuti aggiuntivi o informazioni interne non sono rilevanti e anzi pregiudicano la correttezza dei risultati.

+ Eventi click generati dall'utente: per clic in questo documento, si fa riferimento a un utente che seleziona un determinato risultato della ricerca restituito da una query di ricerca. Un clic in genere indica che un documento è un risultato rilevante per una specifica query di ricerca.

Collegando la ricerca e facendo clic sugli eventi con un ID di correlazione, è possibile analizzare i comportamenti degli utenti nell'applicazione. I log del traffico di ricerca non sono in grado di offrire informazioni sulla ricerca così dettagliate.

## <a name="add-search-traffic-analytics"></a>Aggiungere Analisi del traffico di ricerca

I segnali menzionati nella sezione precedente devono essere raccolti dall'applicazione di ricerca quando l'utente interagisce con essa. Application Insights è una soluzione di monitoraggio estensibile, disponibile per più piattaforme, con opzioni di strumentazione flessibili. L'uso di Application Insights consente di sfruttare i report di ricerca Power BI creati da ricerca cognitiva di Azure per semplificare l'analisi dei dati.

Nella pagina del [portale](https://portal.azure.com) per il servizio ricerca cognitiva di Azure, la pagina analisi del traffico di ricerca contiene un foglio informativo per seguire questo modello di telemetria. È anche possibile selezionare o creare una risorsa di Application Insights e visualizzare i dati necessari, tutti raccolti in un'unica posizione.

![Istruzioni per Analisi del traffico di ricerca][1]

## <a name="1---select-a-resource"></a>1 - Selezionare una risorsa

È necessario selezionare una risorsa di Application Insights da usare o crearne una se non è già disponibile. È possibile usare una risorsa già in uso per registrare gli eventi personalizzati necessari.

Quando si crea una nuova risorsa di Application Insights, sono validi tutti i tipi di applicazione per questo scenario. Selezionare quella che meglio si adatta alla piattaforma in uso.

Per creare il client di telemetria per l'applicazione, è necessario disporre della chiave di strumentazione. È possibile ottenerla dal dashboard del portale di Application Insights oppure dalla pagina Analisi del traffico di ricerca selezionando l'istanza che si desidera usare.

## <a name="2---add-instrumentation"></a>2 - Aggiungere la strumentazione

Questo passaggio consente di instrumentare l'applicazione di ricerca usando la risorsa Application Insights creata nel passaggio precedente. Questo processo è suddiviso in quattro passaggi:

**Passaggio 1: creare un client di telemetria**

Si tratta dell'oggetto che invia eventi alla risorsa Application Insights.

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

**Passaggio 2: richiedere un ID di ricerca per la correlazione**

Per correlare le richieste di ricerca con clic, è necessario disporre di un ID correlazione che mette in relazione questi due eventi distinti. Azure ricerca cognitiva fornisce un ID di ricerca quando viene richiesto con un'intestazione:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**Passaggio 3: registrare gli eventi di ricerca**

Ogni volta che un utente esegue una richiesta di ricerca, è necessario registrarla come evento di ricerca con lo schema seguente in un evento personalizzato di Application Insights:

**SearchServiceName**: (String) nome del servizio di ricerca **SearchId**: (Guid) identificatore univoco della query di ricerca (disponibile nella risposta di **ricerca) IndexName: (** String) indice del servizio di ricerca su cui eseguire una query **QueryTerms**: (String) termini di ricerca immessi dall'utente **Resultcount**: (int) numero di documenti restituiti (in risposta alla ricerca) **ScoringProfile**: (stringa) nome del profilo di punteggio usato, se disponibile.

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

**Passaggio 4: eventi Click di log**

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

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Eseguire l'analisi in Power BI

Dopo aver instrumentato l'app e aver verificato che l'applicazione sia connessa correttamente alla Application Insights, è possibile usare un modello predefinito creato da Azure ricerca cognitiva per Power BI desktop. 

Ricerca cognitiva di Azure fornisce un [pacchetto di contenuto Power bi](https://app.powerbi.com/getdata/services/azure-search) di monitoraggio per poter analizzare i dati di log. Il pacchetto di contenuti aggiunge tabelle e grafici predefiniti utili per analizzare i dati aggiuntivi acquisiti per Analisi del traffico di ricerca. Per altre informazioni, vedere la [pagina della Guida del pacchetto di contenuti](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Nel riquadro di spostamento a sinistra del dashboard di Azure ricerca cognitiva, in **Impostazioni**, fare clic su **analisi del traffico di ricerca**.

2. Nella pagina **Analisi del traffico di ricerca**, nel passaggio 3, fare clic su **Scarica Power BI Desktop** per installare Power BI.

   ![Ottenere report Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Ottenere report Power BI")

2. Nella stessa pagina fare clic su **scarica Power bi report**.

3. Il report viene aperto in Power BI Desktop e viene richiesto di connettersi ad Application Insights. Queste informazioni sono reperibili nelle pagine portale di Azure per la risorsa Application Insights.

   ![Connetti a Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Connetti a Application Insights")

4. Fare clic su **Carica**.

Il report contiene grafici e tabelle che consentono di prendere decisioni più informate per migliorare le prestazioni di ricerca e la rilevanza.

Sono incluse le metriche seguenti:

* Tasso di clic (CTR): rapporto tra utenti che fanno clic su un documento specifico e numero di ricerche totali.
* Ricerche senza clic: termini delle query principali per i quali non sono registrati clic.
* Documenti con più clic: documenti con più clic suddivisi per ID nelle ultime 24 ore e negli ultimi 7 e 30 giorni.
* Coppie termine-documento più comuni: termini che producono lo stesso documento selezionato, ordinati per clic.
* Tempo dei clic: clic con bucket definiti in base al tempo dopo la query di ricerca.

Lo screenshot seguente mostra i report predefiniti e i grafici per l'analisi di Analisi del traffico di ricerca.

![Dashboard Power BI per Azure ricerca cognitiva](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Dashboard Power BI per Azure ricerca cognitiva")

## <a name="next-steps"></a>Passaggi successivi
Instrumentare l'applicazione di ricerca per ottenere dati estremamente utili e dettagliati relativi al servizio di ricerca.

È possibile trovare altre informazioni su [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/) per conoscere i diversi livelli di servizio.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
