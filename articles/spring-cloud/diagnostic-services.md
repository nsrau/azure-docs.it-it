---
title: Analizzare i log e le metriche nel cloud Spring di Azure | Microsoft Docs
description: Informazioni su come analizzare i dati di diagnostica nel cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607867"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizzare i log e le metriche con le impostazioni di diagnostica

Usando la funzionalità di diagnostica di Azure Spring cloud, è possibile analizzare i log e le metriche con uno dei servizi seguenti:

* Usare Log Analytics di Azure, in cui i dati vengono scritti immediatamente senza dover prima essere scritti nell'archiviazione.
* Salvarli in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni).
* Eseguirne lo streaming all'hub eventi per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata.

Per iniziare, abilitare uno di questi servizi per la ricezione dei dati. Per informazioni sulla configurazione di Log Analytics, vedere [Introduzione ai log Analytics in monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Configurare le impostazioni di diagnostica

1. Nel portale di Azure passare all'istanza di Azure Spring cloud.
1. Selezionare l'opzione **impostazioni di diagnostica** e quindi selezionare **Aggiungi impostazione di diagnostica**.
1. Immettere un nome per l'impostazione e quindi scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni seguenti:
    * **Archivia in un account di archiviazione**
    * **Trasmettere a un hub eventi**
    * **Invia a Log Analytics**

1. Scegliere la categoria di log e la categoria metrica che si desidera monitorare, quindi specificare il periodo di conservazione (in giorni). Il periodo di memorizzazione si applica solo all'account di archiviazione.
1. Selezionare **Salva**.

> [!NOTE]
> È possibile che si verifichino gap fino a 15 minuti tra il momento in cui vengono emessi i log o le metriche e quando vengono visualizzati nell'account di archiviazione, nell'hub eventi o in Log Analytics.

## <a name="view-the-logs"></a>Visualizzare i log

### <a name="use-log-analytics"></a>Usare Log Analytics

1. Nel riquadro sinistro della portale di Azure selezionare **log Analytics**.
1. Selezionare l'area di lavoro Log Analytics scelta quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **Ricerca log** , selezionare **log**.
1. Nella casella di ricerca **log** immettere una semplice query, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Per visualizzare i risultati della ricerca, selezionare **Esegui**.
1. È possibile cercare nei log dell'applicazione o dell'istanza specifica impostando una condizione di filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Per altre informazioni sul linguaggio di query usato in Log Analytics, vedere query di [log di monitoraggio di Azure](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Usare l'account di archiviazione 

1. Nel portale di Azure, nel riquadro sinistro, selezionare account di **archiviazione**.

1. Selezionare l'account di archiviazione scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **contenitore BLOB** , selezionare **BLOB**.
1. Per esaminare i log dell'applicazione, cercare un contenitore denominato **Insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un contenitore denominato **Insights-Metrics-PT1M**.

Per ulteriori informazioni sull'invio di informazioni di diagnostica a un account di archiviazione, vedere [archiviare e visualizzare i dati di diagnostica in archiviazione di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Usare l'hub eventi

1. Nel riquadro sinistro della portale di Azure selezionare **Hub eventi**.

1. Cercare e selezionare l'hub eventi scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **elenco hub eventi** , selezionare **Hub eventi**.
1. Per esaminare i log dell'applicazione, cercare un hub eventi denominato **Insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un hub eventi denominato **Insights-Metrics-PT1M**.

Per altre informazioni sull'invio di informazioni di diagnostica a un hub eventi, vedere [trasmettere dati diagnostica di Azure nel percorso critico tramite hub eventi](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analizzare i log

Azure Log Analytics fornisce kusto in modo che sia possibile eseguire query sui log per l'analisi. Per una rapida introduzione all'esecuzione di query sui log tramite kusto, vedere l' [esercitazione log Analytics](../azure-monitor/log-query/get-started-portal.md).

I log applicazioni forniscono informazioni critiche sull'integrità, sulle prestazioni e sulle prestazioni dell'applicazione. Nelle sezioni successive sono disponibili alcune semplici query che consentono di comprendere gli stati correnti e precedenti dell'applicazione.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostra i log applicazioni dal cloud Spring di Azure

Per esaminare un elenco di log applicazioni da Azure Spring cloud, ordinati in base all'ora con i log più recenti visualizzati per primi, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostra voci di log contenenti errori o eccezioni

Per esaminare le voci di log non ordinate che citano un errore o un'eccezione, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilizzare questa query per individuare gli errori o modificare i termini della query per individuare specifici codici di errore o eccezioni. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostra il numero di errori ed eccezioni segnalati dall'applicazione nell'ultima ora

Per creare un grafico a torta che Visualizza il numero di errori e le eccezioni registrate dall'applicazione nell'ultima ora, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Altre informazioni sull'esecuzione di query sui log applicazioni

Monitoraggio di Azure offre un supporto completo per l'esecuzione di query sui log applicazioni usando Log Analytics. Per altre informazioni su questo servizio, vedere [Introduzione alle query di log in monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md). Per altre informazioni sulla compilazione di query per analizzare i log applicazioni, vedere [Panoramica delle query di log in monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).
