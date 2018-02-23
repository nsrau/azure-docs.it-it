---
title: Live Metrics Stream con diagnostica e metriche personalizzate in Azure Application Insights | Microsoft Docs
description: Monitorare l'app Web in tempo reale, con metriche personalizzate e diagnosticare problemi con un feed live di errori, tracce ed eventi.
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin
ms.openlocfilehash: 866fc729b3167863c2d423d0e6ac0d7640e3425e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorare e diagnosticare con una latenza di 1 secondo 

Usando Live Metrics Stream da [Application Insights](app-insights-overview.md) è possibile testare il funzionamento dell'applicazione Web live nell'ambiente di produzione. Selezionare e filtrare le metriche e i contatori delle prestazioni in tempo reale, senza distorsioni del servizio. Esaminare le analisi dello stack da richieste ed eccezioni di esempio non riuscite. Insieme al [Profiler](app-insights-profiler.md), al [debugger di snapshot](app-insights-snapshot-debugger.md) e [al test delle prestazioni](app-insights-monitor-web-app-availability.md#performance-tests), Live Metrics Stream offre uno strumento di diagnostica non invasivo e potente per il sito Web live.

Con Live Metrics Stream, è possibile:

* Convalidare una correzione durante il rilasciato, osservando i contatori delle prestazioni e degli errori.
* Osservare l'effetto dei carichi di test e diagnosticare i problemi live. 
* Concentrarsi sulle sessioni di test specifiche o filtrare i problemi noti, selezionando e filtrando le metriche che si desidera osservare.
* Ottenere le eccezioni delle tracce in tempo reale.
* Provare a usare i filtri per individuare gli indicatori di prestazioni chiave più rilevanti.
* Monitorare ogni contatore delle prestazioni di Windows live.
* Identificare facilmente un server che presenta problemi e filtrare tutti gli indicatori KPI o i feed live solo per tale server.

[![Video di Live Metrics Stream](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Live Metrics Stream è attualmente disponibile nelle app ASP.NET in esecuzione in locale o nel cloud. 

## <a name="get-started"></a>Attività iniziali

1. Se [Application Insights](app-insights-asp-net.md) non è stato ancora installato nell'app Web ASP.NET o nell'[app del server Windows](app-insights-windows-services.md), è possibile farlo ora. 
2. **Eseguire l'aggiornamento alla versione più recente** del pacchetto Application Insights. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Aprire la scheda **Aggiornamenti**, spuntare **Includi versione preliminare** e selezionare tutti i pacchetti Microsoft.ApplicationInsights.*.

    Ridistribuire l'app.

3. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights per l'app e quindi Live Stream.

4. [Proteggere il canale di controllo](#secure-the-control-channel) se è possibile usare i dati sensibili, ad esempio i nomi dei clienti, nei filtri.


![Nel pannello Panoramica fare clic su Flusso attivo](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Dati non visualizzati Controllare il firewall del server

Controllare che [le porte in uscita di Live Metrics Stream](app-insights-ip-addresses.md#outgoing-ports) siano aperte nel firewall del server. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Differenze tra Live Metrics Stream ed Esplora metriche e Analisi

| |Live Stream | Esplora metriche e Analisi |
|---|---|---|
|Latency|Dati visualizzati in un secondo|Aggregati in minuti|
|Nessuna conservazione|I dati vengono mantenuti finché si trovano nel grafico, poi vengono eliminati|[Dati mantenuti per 90 giorni](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|On demand|I dati vengono trasmessi durante l'apertura di Live Metrics|I dati vengono inviati ogni volta che l'SDK viene installato e attivato|
|Gratuito|Non sono previste spese per i dati di Live Stream|Soggetto al [piano tariffario](app-insights-pricing.md)
|campionamento|Tutte le metriche selezionate e i contatori vengono trasmessi. Gli errori e le analisi dello stack vengono usati come esempi. TelemetryProcessors non viene applicato.|Eventi potrebbero essere usati come [esempi](app-insights-api-filtering-sampling.md)|
|Canale di controllo|I segnali di controllo del filtro vengono inviati all'SDK. È consigliabile [proteggere questo canale](#secure-channel).|La comunicazione è unidirezionale al portale|


## <a name="select-and-filter-your-metrics"></a>Selezionare e filtrare le metriche

(Disponibile in app ASP.NET classiche con l'SDK più recente.)

È possibile monitorare gli indicatori KPI personalizzati live applicando filtri arbitrari su eventuali dati di Application Insights Telemetry dal portale. Fare clic sul controllo del filtro che viene mostrato quando il puntatore del mouse passa su un grafico. Il grafico seguente traccia un indicatore KPI del conteggio di richieste personalizzato con filtri sugli attributi dell'URL e della durata. Convalidare i filtri nella sezione Anteprima flusso che mostra un feed live di telemetria che corrisponde ai criteri specificati in qualsiasi punto nel tempo. 

![Indicatore KPI di richieste personalizzato](./media/app-insights-live-stream/live-stream-filteredMetric.png)

È possibile monitorare un valore diverso dal conteggio. Le opzioni dipendono dal tipo di flusso, che può essere una qualsiasi Application Insights Telemetry: richieste, dipendenze, eccezioni, tracce, eventi o metriche. Può essere la propria [misura personalizzata](app-insights-api-custom-events-metrics.md#properties):

![Opzioni del valore](./media/app-insights-live-stream/live-stream-valueoptions.png)

Oltre ai dati di Application Insights Telemetry, è anche possibile monitorare un contatore delle prestazioni di Windows selezionandolo dalle opzioni di flusso e inserendo il nome del contatore delle prestazioni.

Le metriche attive vengono aggregate in due punti: in locale su ciascun server e quindi su tutti i server. È possibile modificare l'impostazione predefinita per entrambi selezionando altre opzioni nei rispettivi elenchi a discesa.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria di esempio: eventi di diagnostica live personalizzati
Per impostazione predefinita, il feed live degli eventi mostra esempi di richieste non riuscite e chiamate di dipendenza, eccezioni, eventi e tracce. Fare clic sull'icona del filtro per visualizzare i criteri applicati in un punto qualsiasi nel tempo. 

![Feed live predefinito](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Così come con le metriche, è possibile specificare i criteri arbitrari per i tipi di dati di Application Insights Telemetry. In questo esempio, vengono selezionati eventi, tracce e richieste non riuscite specifiche. Vengono selezionate anche tutte le eccezioni e gli errori di dipendenza.

![Feed live personalizzati](./media/app-insights-live-stream/live-stream-events.png)

Nota: attualmente, per i criteri di eccezione basati sul messaggio, usare il messaggio di eccezione più esterno. Nell'esempio precedente, per filtrare l'eccezione di tipo benigno con messaggio di eccezione interna, segue il delimitatore "<--", "Il client si è disconnesso." usare un criterio che non contiene il messaggio "Errore durante la lettura del contenuto della richiesta".

Visualizzare i dettagli di un elemento nel feed live facendovi clic sopra. È possibile sospendere il feed facendo clic su **Sospendi** o semplicemente scorrendo verso il basso o facendo clic su un elemento. Il feed live verrà ripreso se, scorrendo, si torna all'inizio o facendo clic sul contatore degli elementi raccolti che era stato sospeso.

![Errori live campionati](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtro per istanza di server

Se si vuole monitorare un'istanza specifica del ruolo server, è possibile applicare un filtro per server.

![Errori live campionati](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisiti SDK
Live Metrics Stream personalizzato è disponibile con la versione 2.4.0-beta2 o più recente di [Application Insights SDK per il Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Ricordarsi di selezionare l'opzione "Includi versione preliminare" da Gestione pacchetti di NuGet.

## <a name="secure-the-control-channel"></a>Proteggere il canale di controllo
I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave privata API e con la chiave di strumentazione.
### <a name="create-an-api-key"></a>Creare una chiave API

![Creare una chiave API](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Aggiungere una chiave API alla configurazione
Nel file applicationinsights.config aggiungere AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
Oppure nel codice impostarla in QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

Tuttavia, se l'utente riconosce tutti i server collegati e li ritiene affidabili, è possibile provare i filtri personalizzati senza il canale autenticato. Questa opzione è disponibile per sei mesi. Questa sostituzione è necessaria una volta per ogni nuova sessione oppure quando un nuovo server è online.

![Opzioni di autenticazione delle metriche attive](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>È consigliabile configurare il canale autenticato prima di immettere informazioni potenzialmente riservate, ad esempio CustomerID nei criteri di filtro.
>

## <a name="generating-a-performance-test-load"></a>Generazione di un carico di test delle prestazioni

Se si desidera osservare l'effetto di un aumento del carico, usare il pannello Test delle prestazioni. Simula le richieste da un certo numero di utenti simultanei. Può eseguire "test manuale", ovvero test ping, di un singolo URL o un [test delle prestazioni Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests) caricato, proprio come un test di disponibilità.

> [!TIP]
> Dopo aver creato il test delle prestazioni, aprire il test e il pannello Live Stream in finestre distinte. È possibile visualizzare l'avvio del test delle prestazioni in coda e il flusso live nello stesso momento.
>


## <a name="troubleshooting"></a>risoluzione dei problemi

Dati non visualizzati Se l'applicazione si trova in una rete protetta: Live Metrics Stream usa un indirizzo IP diverso dagli altri dati di Application Insights Telemetry. Assicurarsi che [tali indirizzi IP](app-insights-ip-addresses.md) siano aperti nel firewall.



## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](app-insights-web-track-usage.md)
* [Uso di Ricerca diagnostica](app-insights-diagnostic-search.md)
* [Profiler](app-insights-profiler.md)
* [Debugger di snapshot](app-insights-snapshot-debugger.md)
