---
title: Analizzare i log e le metriche nel cloud Spring di Azure | Microsoft Docs
description: Informazioni su come analizzare i dati di diagnostica nel cloud Spring di Azure
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038886"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analizzare i log e le metriche con le impostazioni di diagnostica

La funzionalità di diagnostica di Azure Spring cloud consente di analizzare i log e le metriche usando uno dei servizi seguenti:

* Analizzarli con Log Analytics di Azure, in cui i dati vengono scritti immediatamente in Azure Log Analytics senza dover prima scrivere i dati nella risorsa di archiviazione.
* Salvarli in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni).
* Inviarli a hub eventi per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata.

Per iniziare, è necessario abilitare uno di questi servizi per la ricezione dei dati.  Per informazioni sulla configurazione di Log Analytics, vedere [questa esercitazione](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

1. Passare all'istanza di Azure Spring cloud nella portale di Azure.
1. Selezionare l'opzione di menu **impostazioni di diagnostica** .
1. Selezionare il pulsante **Aggiungi impostazione di diagnostica** .
1. Immettere un nome per l'impostazione e scegliere dove si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni seguenti:
    * Archivia in un account di archiviazione
    * Streaming in un hub eventi
    * Invia a Log Analytics

1. Scegliere la categoria di log e la categoria metrica che si vuole monitorare e specificare il periodo di conservazione (in giorni). Il tempo di conservazione si applica solo all'account di archiviazione.
1. Selezionare **Salva** per applicare l'impostazione.

> [!NOTE]
> Potrebbero essere necessari fino a 15 minuti tra il momento in cui vengono emessi i log o le metriche e quando vengono visualizzati in account di archiviazione/Hub eventi/Log Analytics.

## <a name="viewing-logs"></a>Visualizzazione dei log

### <a name="using-log-analytics"></a>Uso di Log Analytics

1. Dal portale di Azure selezionare Log Analytics dal menu di spostamento a sinistra.
1. Selezionare l'area di lavoro Log Analytics scelta durante l'aggiunta delle impostazioni di diagnostica.
1. Selezionare `Logs` per aprire il pannello Ricerca log.
1. Immettere una query semplice nella casella di ricerca log.  Esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Selezionare `Run` per visualizzare i risultati della ricerca.
1. È possibile cercare nei log dell'applicazione o dell'istanza specifica impostando una condizione di filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Altre informazioni sul linguaggio di query usato in Log Analytics [in questo articolo](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Uso di log e metriche nell'account di archiviazione

1. Dal portale di Azure selezionare account di archiviazione dal menu di spostamento a sinistra.
1. Selezionare l'account di archiviazione scelto durante l'aggiunta delle impostazioni di diagnostica.
1. Selezionare `Blobs` voce per aprire il pannello contenitore BLOB.
1. Trovare un contenitore denominato `insights-logs-applicationconsole` per esaminare i registri applicazioni.
1. Trovare un contenitore denominato `insights-metrics-pt1m` per esaminare le metriche dell'applicazione.

[Scopri di più sull'invio di informazioni di diagnostica a un account di archiviazione.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Uso di hub eventi

1. Dal portale di Azure selezionare Hub eventi dal menu di spostamento a sinistra.
1. Individuare e selezionare gli hub eventi scelti durante l'aggiunta delle impostazioni di diagnostica.
1. Selezionare `Event Hubs` per aprire il pannello elenco hub eventi.
1. Trovare un hub eventi denominato `insights-logs-applicationconsole` per esaminare i registri applicazioni.
1. Trovare un hub eventi denominato `insights-metrics-pt1m` per esaminare le metriche dell'applicazione.

[Scopri di più sull'invio di informazioni di diagnostica a un hub eventi.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analisi dei log

Azure Log Analytics fornisce kusto, in modo che sia possibile eseguire query sui log per l'analisi.  Vedere l' [esercitazione log Analytics](../azure-monitor/log-query/get-started-portal.md) per una rapida introduzione all'esecuzione di query sui log con kusto.

I log applicazioni forniscono informazioni critiche sull'integrità, sulle prestazioni e sulle prestazioni dell'applicazione.  Di seguito sono riportate alcune semplici query che consentono di comprendere gli stati correnti e precedenti dell'applicazione.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostra i log applicazioni dal cloud Spring di Azure

Per esaminare un elenco di log applicazioni da Azure Spring cloud, ordinati in base all'ora con i log più recenti visualizzati per primi:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostra voci di log contenenti errori o eccezioni

Questa query consente di esaminare le voci di log che citano un errore o un'eccezione.  I risultati non sono ordinati.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilizzare questa query per individuare gli errori o modificare i termini della query per individuare specifici codici di errore o eccezioni.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostra il numero di errori ed eccezioni segnalati dall'applicazione nell'ultima ora

Questa query crea un grafico a torta che Visualizza il numero di errori e le eccezioni registrate dall'applicazione nell'ultima ora:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Altre informazioni sull'esecuzione di query sui log applicazioni

Monitoraggio di Azure offre un supporto completo per l'esecuzione di query sui log applicazioni usando Log Analytics.  Per altre informazioni su questo servizio, vedere l'esercitazione sulle [query di log](../azure-monitor/log-query/get-started-queries.md) con monitoraggio di Azure. La [Panoramica delle query di log in monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) fornisce altre informazioni sulla compilazione di query per analizzare i log dell'applicazione.
