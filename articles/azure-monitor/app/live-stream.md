---
title: Diagnostica con informazioni dettagliate Live Metrics Stream applicazione Azure
description: Monitorare l'app Web in tempo reale, con metriche personalizzate e diagnosticare problemi con un feed live di errori, tracce ed eventi.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: c12126c23ce1f1e2bd72f88eead5b8f34e4fd83d
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142214"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorare e diagnosticare con una latenza di 1 secondo

Monitora l'applicazione Web Live in produzione usando Live Metrics Stream (noto anche come QuickPulse) da [Application Insights](./app-insights-overview.md). Selezionare e filtrare le metriche e i contatori delle prestazioni in tempo reale, senza distorsioni del servizio. Esaminare le analisi dello stack da richieste ed eccezioni di esempio non riuscite. Insieme a [Profiler](./profiler.md) e [Snapshot debugger](./snapshot-debugger.md), Live Metrics Stream offre uno strumento di diagnostica potente e non invasivo per il sito Web Live.

Con Live Metrics Stream, è possibile:

* Convalidare una correzione durante il rilasciato, osservando i contatori delle prestazioni e degli errori.
* Osservare l'effetto dei carichi di test e diagnosticare i problemi live.
* Concentrarsi sulle sessioni di test specifiche o filtrare i problemi noti, selezionando e filtrando le metriche che si desidera osservare.
* Ottenere le eccezioni delle tracce in tempo reale.
* Provare a usare i filtri per individuare gli indicatori di prestazioni chiave più rilevanti.
* Monitorare ogni contatore delle prestazioni di Windows live.
* Identificare facilmente un server che presenta problemi e filtrare tutti gli indicatori KPI o i feed live solo per tale server.

![Scheda metriche attive](./media/live-stream/live-metric.png)

Le metriche attive sono attualmente supportate per le app ASP.NET, ASP.NET Core, funzioni di Azure, Java e Node.js.

## <a name="get-started"></a>Introduzione

1. Seguire le linee guida specifiche del linguaggio per abilitare le metriche in tempo reale.
   * [ASP.NET](./asp-net.md) -la metrica dinamica è abilitata per impostazione predefinita.
   * La metrica [ASP.NET Core](./asp-net-core.md)-Live è abilitata per impostazione predefinita.
   * Per impostazione predefinita, le metriche di [.NET/.NET Core Console/Worker](./worker-service.md)-Live sono abilitate.
   * [Applicazioni .NET: consente di abilitare l'uso del codice](#enable-livemetrics-using-code-for-any-net-application).
   * [Node.js](./nodejs.md#live-metrics)

2. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights per l'app e quindi Live Stream.

3. [Proteggere il canale di controllo](#secure-the-control-channel) se è possibile usare i dati sensibili, ad esempio i nomi dei clienti, nei filtri.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Abilitare LiveMetrics usando il codice per qualsiasi applicazione .NET

Anche se LiveMetrics è abilitato per impostazione predefinita quando si esegue l'onboarding con le istruzioni consigliate per le applicazioni .NET, di seguito viene illustrato come configurare manualmente le metriche in tempo reale.

1. Installare il pacchetto NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. Il codice dell'app console di esempio seguente mostra la configurazione di metriche attive.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Mentre l'esempio precedente è per un'app console, lo stesso codice può essere usato in qualsiasi applicazione .NET. Se sono abilitati altri TelemetryModules che raccolgono automaticamente i dati di telemetria, è importante assicurarsi che la stessa configurazione usata per inizializzare tali moduli venga usata anche per il modulo Live Metrics.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Differenze tra Live Metrics Stream ed Esplora metriche e Analisi

| |Live Stream | Esplora metriche e Analisi |
|---|---|---|
|**Latency**|Dati visualizzati in un secondo|Aggregati in minuti|
|**Nessuna conservazione**|I dati vengono mantenuti finché si trovano nel grafico, poi vengono eliminati|[Dati mantenuti per 90 giorni](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Su richiesta**|I dati vengono trasmessi solo quando il riquadro metriche attive è aperto |I dati vengono inviati ogni volta che l'SDK viene installato e attivato|
|**Gratuito**|Non sono previste spese per i dati di Live Stream|Soggetto al [piano tariffario](./pricing.md)
|**Campionamento**|Tutte le metriche selezionate e i contatori vengono trasmessi. Gli errori e le analisi dello stack vengono usati come esempi. |Eventi potrebbero essere usati come [esempi](./api-filtering-sampling.md)|
|**Canale di controllo**|I segnali di controllo del filtro vengono inviati all'SDK. È consigliabile proteggere questo canale.|La comunicazione è unidirezionale, al portale|

## <a name="select-and-filter-your-metrics"></a>Selezionare e filtrare le metriche

Disponibile con ASP.NET, ASP.NET Core e Funzioni di Azure (v2).

È possibile monitorare gli indicatori KPI personalizzati live applicando filtri arbitrari su eventuali dati di Application Insights Telemetry dal portale. Fare clic sul controllo del filtro che viene mostrato quando il puntatore del mouse passa su un grafico. Il grafico seguente traccia un indicatore KPI del conteggio di richieste personalizzato con filtri sugli attributi dell'URL e della durata. Convalidare i filtri nella sezione Anteprima flusso che mostra un feed live di telemetria che corrisponde ai criteri specificati in qualsiasi punto nel tempo.

![Frequenza di richieste di filtro](./media/live-stream/filter-request.png)

È possibile monitorare un valore diverso dal conteggio. Le opzioni dipendono dal tipo di flusso, che può essere una qualsiasi Application Insights Telemetry: richieste, dipendenze, eccezioni, tracce, eventi o metriche. Può essere la propria [misura personalizzata](./api-custom-events-metrics.md#properties):

![Generatore di query sulla frequenza delle richieste con metrica personalizzata](./media/live-stream/query-builder-request.png)

Oltre ai dati di Application Insights Telemetry, è anche possibile monitorare un contatore delle prestazioni di Windows selezionandolo dalle opzioni di flusso e inserendo il nome del contatore delle prestazioni.

Le metriche attive vengono aggregate in due punti: in locale su ciascun server e quindi su tutti i server. È possibile modificare l'impostazione predefinita per entrambi selezionando altre opzioni nei rispettivi elenchi a discesa.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria di esempio: eventi di diagnostica live personalizzati
Per impostazione predefinita, il feed live degli eventi mostra esempi di richieste non riuscite e chiamate di dipendenza, eccezioni, eventi e tracce. Fare clic sull'icona del filtro per visualizzare i criteri applicati in un punto qualsiasi nel tempo.

![Pulsante Filter (Filtro)](./media/live-stream/filter.png)

Così come con le metriche, è possibile specificare i criteri arbitrari per i tipi di dati di Application Insights Telemetry. In questo esempio vengono selezionati errori di richiesta specifici ed eventi.

![Generatore di query](./media/live-stream/query-builder.png)

> [!NOTE]
> attualmente, per i criteri di eccezione basati sul messaggio, usare il messaggio dell'eccezione più esterna. Nell'esempio precedente, per filtrare l'eccezione di tipo benigno con messaggio di eccezione interna, segue il delimitatore "<--", "Il client si è disconnesso." usare un criterio che non contiene il messaggio "Errore durante la lettura del contenuto della richiesta".

Visualizzare i dettagli di un elemento nel feed live facendovi clic sopra. È possibile sospendere il feed facendo clic su **Sospendi** o semplicemente scorrendo verso il basso o facendo clic su un elemento. Il feed live verrà ripreso se, scorrendo, si torna all'inizio o facendo clic sul contatore degli elementi raccolti che era stato sospeso.

![Errori live campionati](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtro per istanza di server

Se si vuole monitorare un'istanza specifica del ruolo server, è possibile applicare un filtro per server. Per filtrare, selezionare il nome del server in *Server*.

![Errori live campionati](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Proteggere il canale di controllo

> [!NOTE]
> Attualmente, è possibile configurare solo un canale autenticato usando il monitoraggio basato sul codice e non è in grado di autenticare i server con il codice di connessione.

I criteri dei filtri personalizzati specificati nel portale di metriche attive vengono restituiti al componente metriche attive nell'SDK Application Insights. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave privata API e con la chiave di strumentazione.

### <a name="create-an-api-key"></a>Creare una chiave API

![Chiave API > creare la chiave API ](./media/live-stream/api-key.png)
 ![ creare la scheda chiave API. Selezionare "autenticare il canale di controllo SDK" quindi "Genera chiave"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Aggiungere una chiave API alla configurazione

### <a name="aspnet"></a>ASP.NET

Nel file applicationinsights.config aggiungere AuthenticationApiKey a QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Per [ASP.NET Core](./asp-net-core.md) applicazioni, seguire le istruzioni riportate di seguito.

Modificare `ConfigureServices` il file startup.cs nel modo seguente:

Aggiungere lo spazio dei nomi seguente.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Modificare quindi `ConfigureServices` il metodo come indicato di seguito.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Altre informazioni sulla configurazione delle applicazioni ASP.NET Core sono disponibili nella Guida alla [configurazione dei moduli di telemetria in ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

Per le applicazioni [WorkerService](./worker-service.md) , seguire le istruzioni riportate di seguito.

Aggiungere lo spazio dei nomi seguente.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Aggiungere quindi la riga seguente prima della chiamata a `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Altre informazioni sulla configurazione delle applicazioni WorkerService sono disponibili nella Guida alla [configurazione dei moduli di telemetria in WorkerServices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>App per le funzioni di Azure

Per le app per le funzioni di Azure (v2), la protezione del canale con una chiave API può essere eseguita con una variabile di ambiente.

Creare una chiave API dall'interno della risorsa Application Insights e passare a **impostazioni > configurazione** per il app per le funzioni. Selezionare **nuova impostazione applicazione** e immettere un nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e un valore corrispondente alla chiave API.

Tuttavia, se l'utente riconosce tutti i server collegati e li ritiene affidabili, è possibile provare i filtri personalizzati senza il canale autenticato. Questa opzione è disponibile per sei mesi. Questa sostituzione è necessaria una volta per ogni nuova sessione oppure quando un nuovo server è online.

![Opzioni di autenticazione delle metriche attive](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>È consigliabile configurare il canale autenticato prima di immettere informazioni potenzialmente riservate, ad esempio CustomerID nei criteri di filtro.
>

## <a name="supported-features-table"></a>Tabella delle funzionalità supportate

| Linguaggio                         | Metriche di base       | Metriche delle prestazioni | Filtro personalizzato    | Telemetria di esempio    | Suddivisione CPU per processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +)  |
| .NET Core (target =. NET Framework)| Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +)  |
| .NET Core (target =. NET Core)     | Supportato (versione 2.4.1 +) | Supportato*          | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | **Non Supportato**    |
| Funzioni di Azure v2               | Funzionalità supportata           | Funzionalità supportata           | Funzionalità supportata           | Funzionalità supportata           | **Non supportato**    |
| Java                             | Supportato (versione 2.0.0 +) | Supportato (versione 2.0.0 +) | **Non Supportato**   | **Non Supportato**   | **Non Supportato**    |
| Node.js                          | Supportato (V 1.3.0 +) | Supportato (V 1.3.0 +) | **Non Supportato**   | Supportato (V 1.3.0 +) | **Non Supportato**    |

Le metriche di base includono la richiesta, la dipendenza e la frequenza delle eccezioni. Le metriche delle prestazioni (contatori delle prestazioni) includono memoria e CPU. La telemetria di esempio mostra un flusso di informazioni dettagliate per le richieste e le dipendenze non riuscite, le eccezioni, gli eventi e le tracce.

 \*Il supporto di PerfCounters varia leggermente tra le versioni di .NET Core che non sono destinate al .NET Framework:

- Le metriche PerfCounters sono supportate durante l'esecuzione nel servizio app Azure per Windows. (AspNetCore SDK versione 2.4.1 o successiva)
- PerfCounters sono supportati quando l'app viene eseguita in qualsiasi computer Windows (VM o servizio cloud o in locale). (AspNetCore SDK versione 2.7.1 o successiva), ma per le app destinate a .NET Core 2,0 o versioni successive.
- PerfCounters sono supportati quando l'app è in esecuzione ovunque (Linux, Windows, servizio app per Linux, contenitori e così via) nelle versioni più recenti (ad esempio, AspNetCore SDK versione 2.8.0 o successiva), ma solo per le app destinate a .NET Core 2,0 o versioni successive.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Live Metrics Stream USA indirizzi IP diversi rispetto ad altri dati di telemetria Application Insights. Assicurarsi che [tali indirizzi IP](./ip-addresses.md) siano aperti nel firewall. Verificare inoltre che le [porte in uscita per Live Metrics Stream](./ip-addresses.md#outgoing-ports) siano aperte nel firewall dei server.

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio dell'utilizzo con Application Insights](./usage-overview.md)
* [Uso di Ricerca diagnostica](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Debugger di snapshot](./snapshot-debugger.md)
