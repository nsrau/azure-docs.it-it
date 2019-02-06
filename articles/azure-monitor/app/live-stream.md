---
title: Live Metrics Stream con diagnostica e metriche personalizzate in Azure Application Insights | Microsoft Docs
description: Monitorare l'app Web in tempo reale, con metriche personalizzate e diagnosticare problemi con un feed live di errori, tracce ed eventi.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/28/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f369eb6241a8eb3d44a0a38e243c533da47103e1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104614"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorare e diagnosticare con una latenza di 1 secondo

Usando Live Metrics Stream da [Application Insights](../../azure-monitor/app/app-insights-overview.md) è possibile testare il funzionamento dell'applicazione Web live nell'ambiente di produzione. Selezionare e filtrare le metriche e i contatori delle prestazioni in tempo reale, senza distorsioni del servizio. Esaminare le analisi dello stack da richieste ed eccezioni di esempio non riuscite. Insieme al [Profiler](../../azure-monitor/app/profiler.md), al [debugger di snapshot](../../azure-monitor/app/snapshot-debugger.md) e [al test delle prestazioni](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), Live Metrics Stream offre uno strumento di diagnostica non invasivo e potente per il sito Web live.

Con Live Metrics Stream, è possibile:

* Convalidare una correzione durante il rilasciato, osservando i contatori delle prestazioni e degli errori.
* Osservare l'effetto dei carichi di test e diagnosticare i problemi live. 
* Concentrarsi sulle sessioni di test specifiche o filtrare i problemi noti, selezionando e filtrando le metriche che si desidera osservare.
* Ottenere le eccezioni delle tracce in tempo reale.
* Provare a usare i filtri per individuare gli indicatori di prestazioni chiave più rilevanti.
* Monitorare ogni contatore delle prestazioni di Windows live.
* Identificare facilmente un server che presenta problemi e filtrare tutti gli indicatori KPI o i feed live solo per tale server.

[![Video di Live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Le metriche attive sono attualmente supportate per ASP.NET, ASP.NET Core, Funzioni di Azure e app Java.

## <a name="get-started"></a>Attività iniziali

1. Se non si è ancora [installato Application Insights](../../azure-monitor/azure-monitor-app-hub.md) nell'app Web, è possibile farlo ora.
2. Oltre ai pacchetti standard di Application Insights, è necessario [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) per abilitare Live Metrics Stream.
3. **Eseguire l'aggiornamento alla versione più recente** del pacchetto Application Insights. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Aprire la scheda **Aggiornamenti** e selezionare tutti i pacchetti Microsoft.ApplicationInsights.*.

    Ridistribuire l'app.

3. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights per l'app e quindi Live Stream.

4. [Proteggere il canale di controllo](#secure-the-control-channel) se è possibile usare i dati sensibili, ad esempio i nomi dei clienti, nei filtri.

### <a name="no-data-check-your-server-firewall"></a>Dati non visualizzati Controllare il firewall del server

Controllare che [le porte in uscita di Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) siano aperte nel firewall del server. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Differenze tra Live Metrics Stream ed Esplora metriche e Analisi

| |Live Stream | Esplora metriche e Analisi |
|---|---|---|
|Latency|Dati visualizzati in un secondo|Aggregati in minuti|
|Nessuna conservazione|I dati vengono mantenuti finché si trovano nel grafico, poi vengono eliminati|[Dati mantenuti per 90 giorni](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|On demand|I dati vengono trasmessi durante l'apertura di Live Metrics|I dati vengono inviati ogni volta che l'SDK viene installato e attivato|
|Gratuito|Non sono previste spese per i dati di Live Stream|Soggetto al [piano tariffario](../../azure-monitor/app/pricing.md)
|campionamento|Tutte le metriche selezionate e i contatori vengono trasmessi. Gli errori e le analisi dello stack vengono usati come esempi. TelemetryProcessors non viene applicato.|Eventi potrebbero essere usati come [esempi](../../azure-monitor/app/api-filtering-sampling.md)|
|Canale di controllo|I segnali di controllo del filtro vengono inviati all'SDK. È consigliabile [proteggere questo canale](#secure-channel).|La comunicazione è unidirezionale al portale|


## <a name="select-and-filter-your-metrics"></a>Selezionare e filtrare le metriche

Disponibile con ASP.NET, ASP.NET Core e Funzioni di Azure (v2).

È possibile monitorare gli indicatori KPI personalizzati live applicando filtri arbitrari su eventuali dati di Application Insights Telemetry dal portale. Fare clic sul controllo del filtro che viene mostrato quando il puntatore del mouse passa su un grafico. Il grafico seguente traccia un indicatore KPI del conteggio di richieste personalizzato con filtri sugli attributi dell'URL e della durata. Convalidare i filtri nella sezione Anteprima flusso che mostra un feed live di telemetria che corrisponde ai criteri specificati in qualsiasi punto nel tempo. 

![Indicatore KPI di richieste personalizzato](./media/live-stream/live-stream-filteredMetric.png)

È possibile monitorare un valore diverso dal conteggio. Le opzioni dipendono dal tipo di flusso, che può essere una qualsiasi Application Insights Telemetry: richieste, dipendenze, eccezioni, tracce, eventi o metriche. Può essere la propria [misura personalizzata](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opzioni del valore](./media/live-stream/live-stream-valueoptions.png)

Oltre ai dati di Application Insights Telemetry, è anche possibile monitorare un contatore delle prestazioni di Windows selezionandolo dalle opzioni di flusso e inserendo il nome del contatore delle prestazioni.

Le metriche attive vengono aggregate in due punti: in locale su ciascun server e quindi su tutti i server. È possibile modificare l'impostazione predefinita per entrambi selezionando altre opzioni nei rispettivi elenchi a discesa.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Dati di telemetria di esempio: eventi di diagnostica live personalizzati
Per impostazione predefinita, il feed live degli eventi mostra esempi di richieste non riuscite e chiamate di dipendenza, eccezioni, eventi e tracce. Fare clic sull'icona del filtro per visualizzare i criteri applicati in un punto qualsiasi nel tempo. 

![Feed live predefinito](./media/live-stream/live-stream-eventsdefault.png)

Così come con le metriche, è possibile specificare i criteri arbitrari per i tipi di dati di Application Insights Telemetry. In questo esempio, vengono selezionati eventi, tracce e richieste non riuscite specifiche. Vengono selezionate anche tutte le eccezioni e gli errori di dipendenza.

![Feed live personalizzati](./media/live-stream/live-stream-events.png)

Note: attualmente, per i criteri di eccezione basati sul messaggio, usare il messaggio dell'eccezione più esterna. Nell'esempio precedente, per filtrare l'eccezione di tipo benigno con messaggio di eccezione interna, segue il delimitatore "<--", "Il client si è disconnesso." usare un criterio che non contiene il messaggio "Errore durante la lettura del contenuto della richiesta".

Visualizzare i dettagli di un elemento nel feed live facendovi clic sopra. È possibile sospendere il feed facendo clic su **Sospendi** o semplicemente scorrendo verso il basso o facendo clic su un elemento. Il feed live verrà ripreso se, scorrendo, si torna all'inizio o facendo clic sul contatore degli elementi raccolti che era stato sospeso.

![Errori live campionati](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtro per istanza di server

Se si vuole monitorare un'istanza specifica del ruolo server, è possibile applicare un filtro per server.

![Errori live campionati](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisiti SDK
Live Metrics Stream personalizzato è disponibile con la versione 2.4.0-beta2 o più recente di [Application Insights SDK per il Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Ricordarsi di selezionare l'opzione "Includi versione preliminare" da Gestione pacchetti di NuGet.

## <a name="secure-the-control-channel"></a>Proteggere il canale di controllo
I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave privata API e con la chiave di strumentazione.
### <a name="create-an-api-key"></a>Creare una chiave API

![Creare una chiave API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Aggiungere una chiave API alla configurazione

### <a name="classic-aspnet"></a>ASP.NET classico

Nel file applicationinsights.config aggiungere AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Oppure nel codice impostarla in QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>App per le funzioni di Azure

Per le app per le funzioni di Azure (v2) è possibile proteggere il canale con una chiave API usando una variabile di ambiente. 

Creare una chiave API dalla risorsa di Application Insights e passare a **Impostazioni applicazione** per l'app per le funzioni. Selezionare **Aggiungi nuova impostazione** e immettere il nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e un valore corrispondente alla chiave API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (richiede Application Insights ASP.NET Core SDK 2.3.0-beta o versioni successive)

Modificare il file startup.cs come indicato di seguito:

Prima aggiungere

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Poi, all'interno del metodo ConfigureServices, aggiungere:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Tuttavia, se l'utente riconosce tutti i server collegati e li ritiene affidabili, è possibile provare i filtri personalizzati senza il canale autenticato. Questa opzione è disponibile per sei mesi. Questa sostituzione è necessaria una volta per ogni nuova sessione oppure quando un nuovo server è online.

![Opzioni di autenticazione delle metriche attive](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>È consigliabile configurare il canale autenticato prima di immettere informazioni potenzialmente riservate, ad esempio CustomerID nei criteri di filtro.
>

## <a name="generating-a-performance-test-load"></a>Generazione di un carico di test delle prestazioni

Se si desidera osservare l'effetto di un aumento del carico, usare il pannello Test delle prestazioni. Simula le richieste da un certo numero di utenti simultanei. Può eseguire "test manuale", ovvero test ping, di un singolo URL o un [test delle prestazioni Web in più passaggi](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) caricato, proprio come un test di disponibilità.

> [!TIP]
> Dopo aver creato il test delle prestazioni, aprire il test e il pannello Live Stream in finestre distinte. È possibile visualizzare l'avvio del test delle prestazioni in coda e il flusso live nello stesso momento.
>


## <a name="troubleshooting"></a>risoluzione dei problemi

Dati non visualizzati Se l'applicazione è in una rete protetta: Live Metrics Stream usa indirizzi IP diversi da quelli di altri dati di telemetria di Application Insights. Assicurarsi che [tali indirizzi IP](../../azure-monitor/app/ip-addresses.md) siano aperti nel firewall.



## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](../../azure-monitor/app/usage-overview.md)
* [Uso di Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Debugger di snapshot](../../azure-monitor/app/snapshot-debugger.md)
