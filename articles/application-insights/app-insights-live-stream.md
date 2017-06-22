---
title: Diagnostica e metriche attive personalizzate in Azure Application Insights | Microsoft Docs
description: Monitorare l&quot;app Web in tempo reale, con metriche personalizzate e diagnosticare problemi con un feed live di errori, tracce ed eventi.
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
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>Eventi e metriche attive personalizzate: monitorare e diagnosticare con una latenza di 1 secondo 
Live Metrics Stream mostra le metriche e gli venti di [Application Insights](app-insights-overview.md) con una latenza quasi in tempo reale di un secondo. Questo monitoraggio immediato consente di ridurre il tempo medio per rilevare e diagnosticare e consente di soddisfare i contratti di servizio. È possibile:
* Monitorare gli indicatori KPI personalizzati live: sperimentare i filtri, eseguire un'analisi approfondita dei dati di telemetria esistenti di Application Insights per ottenere immediatamente gli indicatori KPI più importanti dal portale. Non sono necessari codifiche, modifiche di configurazione o distribuzioni. Qualsiasi metrica personalizzata o misurazioni che si desidera inviare è disponibile.
* Rilevamento e diagnostica live: vedere gli errori di richiesta e delle dipendenze in tempo reale insieme alle informazioni dettagliate delle tracce d'eccezione. Filtrare i problemi noti di concentrarsi su quelli reali e nuovi.
* Debug live: riprodurre un problema e visualizzare tutti i dati di telemetria correlati in tempo reale, insieme al filtro personalizzato applicato a un ID di sessione specifico, o con gli attributi personalizzati, che identifica l'interazione di riproduzione. Raccogliere informazioni da tutti i server per avere un punto di partenza nella risoluzione del problema.
* Vedere immediatamente le reazioni del consumo di risorse al carico: monitorare *qualsiasi* contatore live di prestazioni di Windows, durante l'esecuzione di test di carico o monitorare la produzione per agire prima di qualsiasi elemento venga compromesso. Non sono necessari modifiche di configurazione o distribuzioni.
* Convalidare una correzione rilasciata. Verificare che l'aggiornamento del servizio avvenga correttamente. Verificare che l'errore risolto non di verifichi più.
* Identificare facilmente un server che presenta problemi e filtrare tutti gli indicatori KPI o i feed live solo per tale server.

I dati di Flusso metriche attive ed eventi sono gratuiti e non vengono addebitati in fattura. I dati vengono trasmessi dai server su richiesta quando si apre l'esperienza del portale. I dati vengono mantenuti finché  si trovano nel grafico, poi vengono eliminati. La funzionalità completa è disponibile per le applicazioni ASP.NET classiche, mentre le applicazioni di base .NET attualmente hanno solo un set fisso di metriche attive e gli errori di esempio. Vengono usati tutti gli SDK supportati fino a PAR per le funzionalità di streaming live più recenti. 

Il flusso live viene raccolta dalle istanze dell'applicazione prima che un campionamento o un TelemetryProcessors personalizzato venga applicato. 

![Video del flusso di metriche in diretta](./media/app-insights-live-stream/youtube.png) [Video del flusso di metriche in diretta](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Accedere alle metriche attive e agli eventi facendo clic sull'opzione a sinistra o sul pulsante nel pannello Panoramica:

![Nel pannello Panoramica fare clic su Flusso attivo](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>Indicatori KPI live personalizzati
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

## <a name="authenticated-channel"></a>Canale autenticati
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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Dati non visualizzati Se l'applicazione si trova in una rete protetta: Live Metrics Stream usa un indirizzo IP diverso dagli altri dati di Application Insights Telemetry. Assicurarsi che [tali indirizzi IP](app-insights-ip-addresses.md) siano aperti nel firewall.



## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](app-insights-web-track-usage.md)
* [Uso di Ricerca diagnostica](app-insights-diagnostic-search.md)


