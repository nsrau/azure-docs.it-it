---
title: Cosa fare in Application Insights | Microsoft Docs
description: Domande frequenti in Application Insights
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9a3df0ad2483471023ebb954d613bc5cad8fb7bf
ms.openlocfilehash: 9e54ee2d67a8dfb5b480db01219e128607e26f51
ms.lasthandoff: 02/02/2017


---
# <a name="how-do-i--in-application-insights"></a>Cosa fare in Application Insights?
## <a name="get-an-email-when-"></a>Per ricevere un messaggio di posta elettronica quando...
### <a name="email-if-my-site-goes-down"></a>Inviare un messaggio di posta elettronica se il sito non è disponibile
Impostare un [test web di disponibilità](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Inviare un messaggio di posta elettronica se il sito è sovraccarico
Impostare un [avviso](app-insights-alerts.md) per il **tempo di risposta del server**. Una soglia compresa tra 1 e 2 secondi dovrebbe risolvere il problema.

![](./media/app-insights-how-do-i/030-server.png)

L'applicazione potrebbe inoltre dare segni di difficoltà tramite la restituzione di codici di errore. Impostare un avviso per le **richieste non riuscite**.

Se si desidera impostare un avviso per le **eccezioni del server**, è necessario effettuare [alcune impostazioni aggiuntive](app-insights-asp-net-exceptions.md) per visualizzare i dati.

### <a name="email-on-exceptions"></a>Inviare un messaggio di posta elettronica in caso di eccezioni
1. [Configurare il monitoraggio delle eccezioni](app-insights-asp-net-exceptions.md)
2. [Impostare un avviso](app-insights-alerts.md) sulla metrica relativa al conteggio del numero di eccezioni

### <a name="email-on-an-event-in-my-app"></a>Inviare un messaggio di posta elettronica per un evento generato dall'app
Si supponga che si desidera ricevere un messaggio di posta elettronica quando si verifica un evento specifico. Application Insights non fornisce direttamente questa funzionalità, ma è possibile [inviare un avviso quando una metrica supera una soglia](app-insights-alerts.md).

Gli avvisi possono essere impostati per [metriche personalizzate](app-insights-api-custom-events-metrics.md#trackmetric), anche se non per gli eventi personalizzati. Scrivere codice per potenziare una metrica quando si verifica l'evento:

    telemetry.TrackMetric("Alarm", 10);

oppure:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Poiché gli avvisi possono avere due stati, è necessario inviare un valore basso quando si presume che l'avviso sia terminato:

    telemetry.TrackMetric("Alarm", 0.5);

Creare un grafico in [Esplora metriche](app-insights-metrics-explorer.md) per visualizzare l'allarme:

![](./media/app-insights-how-do-i/010-alarm.png)

Impostare un avviso da attivare quando la metrica supera un valore medio per un breve periodo:

![](./media/app-insights-how-do-i/020-threshold.png)

Impostare il periodo medio sul valore minimo.

Verranno ricevuti messaggi di posta elettronica quando la metrica è maggiore o minore della soglia.

Alcune informazioni da considerare:

* Un avviso dispone di due stati: "Avviso" e "Integro". Lo stato viene valutato solo quando viene ricevuta una metrica.
* Un messaggio di posta elettronica viene inviato solo quando lo stato cambia. Questo è il motivo per cui è necessario inviare sia metriche con valori alti che metriche con valori bassi.
* Per valutare l'avviso, viene eseguita la media dei valori ricevuti nel periodo precedente. Ciò si verifica ogni volta che viene ricevuta una metrica. Per questo motivo è possibile che i messaggi di posta elettronica vengano inviati più frequentemente rispetto al periodo impostato.
* Poiché vengono inviati messaggi di posta elettronica sia per avvisi con stato "Avviso" e che per avvisi con stato "Integro", è possibile riconsiderare l'evento unico come una condizione con due stati. Ad esempio, anziché un evento "processo completato", creare una condizione "processo in corso", dove è possibile ricevere messaggi di posta elettronica all'inizio e alla fine di un processo.

### <a name="set-up-alerts-automatically"></a>Impostare automaticamente gli avvisi
[Usare PowerShell per creare nuovi avvisi](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Usare PowerShell per gestire Application Insights
* [Creare nuove risorse](app-insights-powershell-script-create-resource.md)
* [Creare nuovi avvisi](app-insights-alerts.md#automation)

## <a name="application-versions-and-stamps"></a>Versioni e indicatori delle applicazioni
### <a name="separate-the-results-from-dev-test-and-prod"></a>Separare i risultati da sviluppo, test e produzione
* Per ambienti diversi impostare valori ikey diversi
* Per diversi indicatori (sviluppo, test, produzione) contrassegnare i dati di telemetria con diversi valori di proprietà

[Altre informazioni](app-insights-separate-resources.md)

### <a name="filter-on-build-number"></a>Filtrare in base al numero di build
Quando si pubblica una nuova versione dell'app, potrebbe essere opportuno separare i dati telemetrici delle diverse build.

È possibile impostare la proprietà della versione dell'applicazione in modo che sia possibile filtrare i risultati della [ricerca](app-insights-diagnostic-search.md) e di [Esplora metriche](app-insights-metrics-explorer.md).

![](./media/app-insights-how-do-i/050-filter.png)

Esistono diversi metodi di impostazione della proprietà della versione dell'applicazione.

* Impostare direttamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Eseguire il wrapping di tale riga in un [inizializzatore di telemetria](app-insights-api-custom-events-metrics.md#defaults) per assicurarsi che tutte le istanze di TelemetryClient siano impostate in modo coerente.
* [ASP.NET] Impostare la versione `BuildInfo.config`. Il modulo Web selezionerà la versione dal nodo BuildLabel. Includere questo file nel progetto e ricordarsi di impostare la proprietà Copia sempre in Esplora soluzioni.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generare automaticamente BuildInfo.config in MSBuild. A tale scopo, aggiungere alcune righe al file con estensione CSPROJ:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Viene generato un file denominato *NomeProgetto*.BuildInfo.config. Il processo di pubblicazione rinomina il file in BuildInfo.config.

    L'etichetta di compilazione contiene un segnaposto (AutoGen_) se la compilazione viene eseguita in Visual Studio. Se la compilazione viene eseguita con MSBuild, viene inserito il numero di versione corretto.

    Per consentire a MSBuild di generare i numeri di versione, in AssemblyReference.cs impostare la versione come, ad esempio, `1.0.*` .

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorare i server back-end e le app desktop
[Usare il modulo di Windows Server SDK](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualizzare i dati
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard con metriche da più app
* In [Esplora metriche](app-insights-metrics-explorer.md)personalizzare il grafico e salvarlo come preferito. Aggiungerlo al dashboard di Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard con dati provenienti da altre fonti e Application Insights
* [Esportare dati di telemetria in Power BI](app-insights-export-power-bi.md).

Or

* Usare SharePoint come dashboard, dove i dati vengono visualizzati in Web part di SharePoint. [Usare l'esportazione continua e l'analisi dei flussi per eseguire l'esportazione in SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Usare PowerView per esaminare il database e creare una Web part di SharePoint per PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrare gli utenti anonimi o autenticati
Se gli utenti effettuano l'accesso, è possibile impostare l' [ID dell'utente autenticato](app-insights-api-custom-events-metrics.md#authenticated-users). Questa operazione non viene eseguita automaticamente.

È quindi possibile:

* Eseguire ricerche in base a ID utente specifici

![](./media/app-insights-how-do-i/110-search.png)

* Filtrare le metriche in base a utenti anonimi o autenticati

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificare i nomi della proprietà o i valori
Creare un [filtro](app-insights-api-filtering-sampling.md#filtering). Consente di modificare o filtrare la telemetria prima che venga inviata dall'app ad Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Elencare utenti specifici e il relativo uso
Se si desidera [cercare utenti specifici](#search-specific-users), è possibile impostare l'[ID dell'utente autenticato](app-insights-api-custom-events-metrics.md#authenticated-users).

Se si desidera un elenco di utenti con i dati quali, ad esempio, le pagine visualizzate o la frequenza di accesso, sono disponibili due opzioni:

* [Impostare l'ID dell'utente autenticato](app-insights-api-custom-events-metrics.md#authenticated-users), [eseguire l'esportazione in un database](app-insights-code-sample-export-sql-stream-analytics.md) e usare gli strumenti appropriati per analizzare i dati utente.
* Se si dispone solo di un numero limitato di utenti, inviare gli eventi o le metriche personalizzati usando i dati di interesse quali, ad esempio, il valore della metrica o il nome dell'evento, quindi impostando l'ID utente come proprietà. Per analizzare le visualizzazioni di pagina, sostituire la chiamata standard JavaScript trackPageView. Per analizzare i dati di telemetria sul lato server, usare un inizializzatore di telemetria per aggiungere l'ID utente a tutti i dati di telemetria del server. È quindi possibile filtrare e segmentare le metriche e le ricerche in base all'ID utente.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Ridurre il traffico dall'app ad Application Insights
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)disabilitare tutti i moduli non necessari, come gli agenti di raccolta del contatore delle prestazioni.
* Usare [Campionamento e filtri](app-insights-api-filtering-sampling.md) nell’SDK.
* Nelle pagine Web limitare il numero di chiamate Ajax segnalato per ogni visualizzazione della pagina. Nel frammento di script dopo `instrumentationKey:...` inserire: `,maxAjaxCallsPerView:3` (o un numero adatto).
* Se si usa [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), calcolare l'aggregazione di batch di valori delle metriche prima di inviare il risultato. Un overload di TrackMetric() esegue questa operazione.

Altre informazioni su [prezzi e quote](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Disabilitare telemetria
Per **avviare e arrestare in modo dinamico** la raccolta e la trasmissione di dati di telemetria dal server:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Per **disabilitare gli agenti di raccolta standard selezionati** - ad esempio, i contatori delle prestazioni, delle richieste HTTP o delle dipendenze - eliminare o impostare come commento le righe pertinenti in [Applicationinsights.config](app-insights-api-custom-events-metrics.md). È possibile eseguire questa operazione, ad esempio, se si vogliono inviare i propri dati TrackRequest.

## <a name="view-system-performance-counters"></a>Visualizzare i contatori delle prestazioni di sistema
Tra le metriche che è possibile visualizzare in Esplora metriche è disponibile un set di contatori delle prestazioni di sistema. Esiste un pannello predefinito denominato **Server** in cui sono visualizzati alcuni set.

![Aprire la risorsa Application Insights e fare clic su Server.](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se non vengono visualizzati dati dei contatori delle prestazioni
* **Server IIS** sul proprio computer o in una macchina virtuale. [Installare Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Sito Web di Azure** - i contatori delle prestazioni non sono ancora supportati. Esistono diverse metriche che è possibile ottenere come una parte standard del Pannello di controllo del sito Web di Azure.
* **Server Unix** - [Installare collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Per visualizzare altri contatori delle prestazioni
* Innanzitutto [aggiungere un nuovo grafico](app-insights-metrics-explorer.md) e verificare che il contatore sia incluso nel set di base offerto.
* In caso contrario, [aggiungere il contatore al set raccolto dal modulo del contatore delle prestazioni](app-insights-performance-counters.md).

