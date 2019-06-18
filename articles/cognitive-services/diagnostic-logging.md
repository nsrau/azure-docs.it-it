---
title: Registrazione diagnostica
titleSuffix: Azure Cognitive Services
description: Questa guida vengono fornite istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivi di Azure. Questi log forniscono dati completi e frequenti sul funzionamento di una risorsa che vengono utilizzati per l'identificazione dei problemi e debug.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155731"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Abilitare la registrazione diagnostica per i servizi cognitivi di Azure

Questa guida vengono fornite istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivi di Azure. Questi log forniscono dati completi e frequenti sul funzionamento di una risorsa che vengono utilizzati per l'identificazione dei problemi e debug. Prima di continuare, è necessario un account Azure con una sottoscrizione di almeno un servizio cognitivi, ad esempio [ricerca Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), o [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la registrazione diagnostica, è necessario una posizione in cui archiviare i dati di log. Questa esercitazione Usa archiviazione di Azure e Log Analitica.

* [Archiviazione di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -conserva i log di diagnostica per il controllo dei criteri, analisi statica o backup. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.
* [Log Analitica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -uno strumento di analitica e ricerca log flessibile che consente di analizzare i log non elaborati generati da una risorsa di Azure.

> [!NOTE]
> Sono disponibili opzioni di configurazione aggiuntive. Per altre informazioni, vedere [raccogliere e usare i dati di log dalle risorse di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-diagnostic-log-collection"></a>Abilitare la raccolta di log di diagnostica  

Per iniziare, abilitare la registrazione nel portale di Azure diagnostica.

> [!NOTE]
> Per abilitare questa funzionalità usando PowerShell o l'interfaccia CLI di Azure, usare le istruzioni disponibili nel [raccogliere e usare i dati di log dalle risorse di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

1. Passare al portale di Azure. Quindi, individuare e selezionare una risorsa di servizi cognitivi. Ad esempio, la sottoscrizione di ricerca Web Bing.   
2. Successivamente, dal menu di navigazione a sinistra, individuare **Monitoring** e selezionare **le impostazioni di diagnostica**. Questa schermata contiene tutte le impostazioni di diagnostica create in precedenza per questa risorsa.
3. Se è presente una risorsa creata in precedenza che si desidera utilizzare, è possibile selezionarli ora. In caso contrario, selezionare **+ Aggiungi impostazione di diagnostica**.
4. Immettere un nome per l'impostazione. Quindi selezionare **archivia in un account di archiviazione** e **Invia a log Analitica**.
5. Quando viene richiesto di configurare, selezionare l'account di archiviazione e l'area di lavoro OMS che si desidera usare per archiviare il log di diagnostica. **Nota**: Se non hai un account di archiviazione o l'area di lavoro OMS, seguire le istruzioni per crearne uno.
6. Selezionare **Audit**, **RequestResponse**, e **AllMetrics**. Quindi impostare il periodo di conservazione per i dati di log di diagnostica. Se un criterio di conservazione è impostato su zero, gli eventi per tale categoria di log vengono archiviati per un periodo illimitato.
7. Fare clic su **Save**.

Può richiedere fino a due ore prima che siano disponibili per query e analizzare i dati di registrazione. In modo non occorre preoccuparsi se non viene visualizzato nulla sin da subito.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visualizzare ed esportare i dati di diagnostica da archiviazione di Azure

Archiviazione di Azure è una soluzione di archiviazione di oggetti affidabile che è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati. In questa sezione imparerai a eseguire una query su un intervallo di tempo di 30 giorni l'account di archiviazione per le transazioni totali ed esportare i dati in excel.

1. Dal portale di Azure, individuare la risorsa di archiviazione di Azure che è stato creato nell'ultima sezione.
2. Dal menu di navigazione a sinistra, individuare **Monitoring** e selezionare **metriche**.
3. Utilizzare elenchi a discesa disponibile per configurare la query. Per questo esempio, è possibile impostare l'intervallo di tempo **ultimi 30 giorni** e per la metrica **transazione**.
4. Quando la query è stata completata, si noterà una visualizzazione di transazione negli ultimi 30 giorni. Per esportare questi dati, usare il **Esporta in Excel** pulsante nella parte superiore della pagina.

Altre informazioni sulle operazioni eseguibili con dati di diagnostica nei [archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Seguire queste istruzioni per esplorare i dati di log analitica per la risorsa.

1. Dal portale di Azure, individuare e selezionare **Log Analitica** dal menu di spostamento a sinistra.
2. Individuare e selezionare la risorsa creata quando si abilita la diagnostica.
3. Sotto **generali**, individuare e selezionare **registri**. In questa pagina è possibile eseguire query su log.

### <a name="sample-queries"></a>Query di esempio

Ecco alcune query Kusto basic che è possibile usare per esplorare i dati di log.

Servizi cognitivi di Azure per un periodo di tempo specificato, eseguire questa query per tutti i log di diagnostica:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Eseguire la query per visualizzare i 10 log più recenti:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Eseguire questa query per raggruppare le operazioni dal **risorsa**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Eseguire questa query per trovare il tempo medio impiegato per eseguire un'operazione:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Eseguire la query per visualizzare il volume di operazioni nel corso del tempo, suddivise in base OperationName con conteggi eseguito il binning per ogni 10 secondi.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come abilitare la registrazione, nonché le categorie di metriche e di log supportate dai vari servizi di Azure, leggere il [panoramica delle metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) in Microsoft Azure e [Panoramica di Azure i log di diagnostica ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) articoli.
* Per informazioni sugli hub eventi, leggere gli articoli seguenti:
  * [Che cos'è l'hub di eventi di Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introduzione all'Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Leggere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Lettura [informazioni sulle ricerche log in Monitoraggio di Azure log](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
