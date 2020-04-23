---
title: Analizzare i log e le metriche in Azure Spring Cloud Documenti Microsoft
description: Informazioni su come analizzare i dati di diagnostica in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870398"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizzare i log e le metriche con le impostazioni di diagnosticaAnalyze logs and metrics with diagnostics settings

Usando la funzionalità di diagnostica di Azure Spring Cloud, è possibile analizzare i log e le metriche con uno dei servizi seguenti:Using the diagnostics functionality of Azure Spring Cloud, you can analyze logs and metrics with any of the following services:

* Usare Analisi dei log di Azure, in cui i dati vengono scritti in Archiviazione di Azure.Use Azure Log Analytics, where the data is written to Azure Storage. Si verifica un ritardo durante l'esportazione dei log in Log Analytics.There is a delay when exporting logs to Log Analytics.
* Salvare i log in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni).
* Eseguire lo streaming dei log all'hub eventi per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata.

Scegliere la categoria di log e la categoria metrica da monitorare.

> [!TIP]
> Vuoi solo trasmettere i tuoi log? Dai un'occhiata a questo [comando dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)di Azure!

## <a name="logs"></a>Log

|File di log | Descrizione |
|----|----|
| **Console Applicazione** | Registro della console di tutte le applicazioni dei clienti. | 
| **Registri di sistema** | Attualmente, solo i registri di [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) in questa categoria. |

## <a name="metrics"></a>Metriche

Per un elenco completo delle metriche, vedere [Metriche di Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

Per iniziare, abilitare uno di questi servizi per ricevere i dati. Per altre informazioni sulla configurazione di Log Analytics, vedere Introduzione a Log Analytics in Monitoraggio di Azure.To learn about configuring Log Analytics, see [Get started with Log Analytics in Azure Monitor.](../azure-monitor/log-query/get-started-portal.md) 

## <a name="configure-diagnostics-settings"></a>Configurare le impostazioni di diagnostica

1. Nel portale di Azure passare all'istanza di Azure Spring Cloud.In the Azure portal, go to your Azure Spring Cloud instance.
1. Selezionare l'opzione **Impostazioni di diagnostica** e quindi selezionare Aggiungi **impostazione diagnostica**.
1. Immettere un nome per l'impostazione e quindi scegliere dove si desidera inviare i registri. È possibile selezionare qualsiasi combinazione delle tre opzioni seguenti:
    * **Archivia in un account di archiviazione**
    * **Streaming in un hub eventi**
    * **Invia a Log Analytics**

1. Scegliere la categoria di log e la categoria di metrica da monitorare, quindi specificare il tempo di conservazione (in giorni). Il tempo di conservazione si applica solo all'account di archiviazione.
1. Selezionare **Salva**.

> [!NOTE]
> Potrebbe esserci un intervallo di tempo fino a 15 minuti tra il momento in cui vengono generati i log o le metriche e quando vengono visualizzati nell'account di archiviazione, nell'hub eventi o in Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Visualizzare i log e le metriche
Esistono vari metodi per visualizzare i log e le metriche come descritto nelle intestazioni seguenti.

### <a name="use-logs-blade"></a>Usa pannello Registri

1. Nel portale di Azure passare all'istanza di Azure Spring Cloud.In the Azure portal, go to your Azure Spring Cloud instance.
1. Per aprire il riquadro **Ricerca log,** selezionare **Registri**.
1. Nella casella di ricerca **Log**
   * Per visualizzare i log, immettere una query semplice, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Per visualizzare le metriche, immettere una query semplice, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Per visualizzare i risultati della ricerca, selezionare **Esegui**.

### <a name="use-log-analytics"></a>Usare Log Analytics

1. Nel riquadro sinistro del portale di Azure selezionare **Log Analytics.**
1. Selezionare l'area di lavoro Log Analytics scelta al momento dell'aggiunta delle impostazioni di diagnostica.
1. Per aprire il riquadro **Ricerca log,** selezionare **Registri**.
1. Nella casella di ricerca **Log,**
   * per visualizzare i log, immettere una query semplice, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * per visualizzare le metriche, immettere una query semplice, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Per visualizzare i risultati della ricerca, selezionare **Esegui**.
1. È possibile cercare i log dell'applicazione o dell'istanza specifica impostando una condizione di filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`fa distinzione `=~` tra maiuscole e minuscole, ma non lo è.

Per altre informazioni sul linguaggio di query usato in Log Analytics, vedere Query di log di Monitoraggio di [Azure.](../azure-monitor/log-query/query-language.md)

### <a name="use-your-storage-account"></a>Usare l'account di archiviazioneUse your storage account 

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Account di archiviazione.**

1. Selezionare l'account di archiviazione scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **Contenitore BLOB,** selezionare **BLOB**.
1. Per esaminare i log delle applicazioni, cercare un contenitore denominato **insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un contenitore denominato **insights-metrics-pt1m**.

Per altre informazioni sull'invio di informazioni di diagnostica a un account di archiviazione, vedere Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure.To learn more about sending diagnostics information to a storage account, see [Store and view diagnostics data in Azure Storage.](../storage/common/storage-introduction.md)

### <a name="use-your-event-hub"></a>Usare l'hub eventi

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Hub eventi**.

1. Cercare e selezionare l'hub eventi scelto al momento dell'aggiunta delle impostazioni di diagnostica.
1. Per aprire il riquadro **Elenco hub eventi,** selezionare **Hub eventi**.
1. Per esaminare i registri applicazioni, cercare un hub eventi denominato **insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un hub eventi denominato **insights-metrics-pt1m**.

Per altre informazioni sull'invio di informazioni di diagnostica a un hub eventi, vedere Streaming dei dati di [Diagnostica di Azure nel percorso critico tramite Hub eventi.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyze-the-logs"></a>Analizzare i registri

Azure Log Analytics è in esecuzione con un motore Kusto per poter eseguire query sui log per l'analisi. Per una rapida introduzione all'esecuzione di query sui log tramite Kusto, vedere [l'esercitazione](../azure-monitor/log-query/get-started-portal.md)di Log Analytics .

I log dell'applicazione forniscono informazioni critiche e log dettagliati sull'integrità, le prestazioni e altro ancora dell'applicazione. Nelle sezioni successive sono riportate alcune query semplici che consentono di comprendere gli stati correnti e passati dell'applicazione.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrare i log delle applicazioni da Azure Spring CloudShow application logs from Azure Spring Cloud

Per esaminare un elenco di log dell'applicazione da Azure Spring Cloud, ordinati in base al tempo con i log più recenti visualizzati per primi, eseguire la query seguente:To review a list of application logs from Azure Spring Cloud, sorted by time with the most recent logs shown first, run the following query:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostra voci di registro contenenti errori o eccezioni

Per esaminare le voci del log non ordinate che menzionano un errore o un'eccezione, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilizzare questa query per individuare gli errori o modificare i termini della query per trovare codici di errore o eccezioni specifici. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visualizzare il numero di errori ed eccezioni segnalati dall'applicazione nell'ultima ora

Per creare un grafico a torta che visualizza il numero di errori ed eccezioni registrati dall'applicazione nell'ultima ora, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Altre informazioni sull'esecuzione di query sui log delle applicazioniLearn more about querying application logs

Azure Monitor provides extensive support for querying application logs by using Log Analytics. Per altre informazioni su questo servizio, vedere Introduzione alle query di log in Monitoraggio di Azure.To learn more about this service, see [Get started with log queries in Azure Monitor.](../azure-monitor/log-query/get-started-queries.md) Per altre informazioni sulla creazione di query per analizzare i log delle applicazioni, vedere [Panoramica delle query di log in Monitoraggio di Azure.For](../azure-monitor/log-query/log-query-overview.md)more information about building queries to analyze your application logs, see Overview of log queries in Azure Monitor.
