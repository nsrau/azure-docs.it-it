---
title: Analizzare i log e le metriche nel cloud Spring di Azure | Microsoft Docs
description: Informazioni su come analizzare i dati di diagnostica nel cloud Spring di Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 57850b45820ec259337a8ad5b67bfebfd6762c24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84790586"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizzare i log e le metriche con le impostazioni di diagnostica

Usando la funzionalità di diagnostica di Azure Spring cloud, è possibile analizzare i log e le metriche con uno dei servizi seguenti:

* Usare Log Analytics di Azure, in cui i dati vengono scritti in archiviazione di Azure. Si è verificato un ritardo durante l'esportazione dei log in Log Analytics.
* Salvare i log in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni).
* Eseguire lo streaming dei log nell'hub eventi per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata.

Scegliere la categoria di log e la categoria metrica che si vuole monitorare.

> [!TIP]
> Vuoi semplicemente trasmettere i log? Vedere questo [comando dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs).

## <a name="logs"></a>Log

|File di log | Descrizione |
|----|----|
| **ApplicationConsole** | Log della console di tutte le applicazioni dei clienti. |
| **SystemLogs** | Attualmente, solo i log di [Spring cloud config server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) in questa categoria. |

## <a name="metrics"></a>Metriche

Per un elenco completo delle metriche, vedere la pagina relativa alle [metriche di Spring cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

Per iniziare, abilitare uno di questi servizi per la ricezione dei dati. Per informazioni sulla configurazione di Log Analytics, vedere [Introduzione ai log Analytics in monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md).

## <a name="configure-diagnostics-settings"></a>Configurare le impostazioni di diagnostica

1. Nel portale di Azure passare all'istanza di Azure Spring cloud.
1. Selezionare l'opzione **impostazioni di diagnostica** e quindi selezionare **Aggiungi impostazione di diagnostica**.
1. Immettere un nome per l'impostazione e quindi scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni seguenti:
    * **Archivia in un account di archiviazione**
    * **Streaming in un hub eventi**
    * **Invia a Log Analytics**

1. Scegliere la categoria di log e la categoria metrica che si desidera monitorare, quindi specificare il periodo di conservazione (in giorni). Il periodo di memorizzazione si applica solo all'account di archiviazione.
1. Selezionare **Salva**.

> [!NOTE]
> 1. È possibile che si verifichino gap fino a 15 minuti tra il momento in cui vengono emessi i log o le metriche e quando vengono visualizzati nell'account di archiviazione, nell'hub eventi o in Log Analytics.
> 1. Se l'istanza del cloud Spring di Azure viene eliminata o spostata, l'operazione non produrrà a catena le risorse delle **impostazioni di diagnostica** . Le risorse delle **impostazioni di diagnostica** devono essere eliminate manualmente prima dell'operazione rispetto al relativo padre, ad esempio l'istanza di Azure Spring cloud. In caso contrario, se viene eseguito il provisioning di una nuova istanza di Azure Spring cloud con lo stesso ID risorsa di quello eliminato o se l'istanza di Azure Spring cloud viene spostata di nuovo, le risorse delle **impostazioni di diagnostica** precedenti continuano a estenderlo.

## <a name="view-the-logs-and-metrics"></a>Visualizzare i log e le metriche
Sono disponibili diversi metodi per visualizzare i log e le metriche, come descritto nelle intestazioni seguenti.

### <a name="use-the-logs-blade"></a>Usare il pannello logs

1. Nel portale di Azure passare all'istanza di Azure Spring cloud.
1. Per aprire il riquadro **Ricerca log** , selezionare **log**.
1. Nella casella di ricerca **tabelle**
   * Per visualizzare i log, immettere una semplice query, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Per visualizzare le metriche, immettere una semplice query, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Per visualizzare i risultati della ricerca, selezionare **Esegui**.

### <a name="use-log-analytics"></a>Usare Log Analytics

1. Nel riquadro sinistro della portale di Azure selezionare **log Analytics**.
1. Selezionare l'area di lavoro Log Analytics scelta quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **Ricerca log** , selezionare **log**.
1. Nella casella di ricerca **tabelle** ,
   * per visualizzare i log, immettere una semplice query, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * per visualizzare le metriche, immettere una semplice query, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Per visualizzare i risultati della ricerca, selezionare **Esegui**.
1. È possibile cercare nei log dell'applicazione o dell'istanza specifica impostando una condizione di filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==`fa distinzione tra maiuscole e minuscole, ma `=~` non lo è.

Per altre informazioni sul linguaggio di query usato in Log Analytics, vedere query di [log di monitoraggio di Azure](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Usare l'account di archiviazione

1. Nella portale di Azure trovare gli **account di archiviazione** nel pannello di navigazione sinistro o nella casella di ricerca.
1. Selezionare l'account di archiviazione scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **contenitore BLOB** , selezionare **BLOB**.
1. Per esaminare i log dell'applicazione, cercare un contenitore denominato **Insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un contenitore denominato **Insights-Metrics-PT1M**.

Per ulteriori informazioni sull'invio di informazioni di diagnostica a un account di archiviazione, vedere [archiviare e visualizzare i dati di diagnostica in archiviazione di Azure](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Usare l'hub eventi

1. Nel portale di Azure individuare **Hub eventi** nel riquadro di spostamento sinistro o nella casella di ricerca.

1. Cercare e selezionare l'hub eventi scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il riquadro **elenco hub eventi** , selezionare **Hub eventi**.
1. Per esaminare i log dell'applicazione, cercare un hub eventi denominato **Insights-logs-applicationconsole**.
1. Per esaminare le metriche dell'applicazione, cercare un hub eventi denominato **Insights-Metrics-PT1M**.

Per altre informazioni sull'invio di informazioni di diagnostica a un hub eventi, vedere [trasmettere dati diagnostica di Azure nel percorso critico tramite hub eventi](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analizzare i log

Azure Log Analytics è in esecuzione con un motore kusto ed è quindi possibile eseguire query sui log per l'analisi. Per una rapida introduzione all'esecuzione di query sui log tramite kusto, vedere l' [esercitazione log Analytics](../azure-monitor/log-query/get-started-portal.md).

I log applicazioni forniscono informazioni critiche e log dettagliati sull'integrità, sulle prestazioni e molto altro dell'applicazione. Nelle sezioni successive sono disponibili alcune semplici query che consentono di comprendere gli stati correnti e precedenti dell'applicazione.

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

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Come convertire le tracce dello stack Java a più righe in una singola riga?

Esiste una soluzione alternativa per convertire le tracce dello stack su più righe in una singola riga. È possibile modificare l'output del log Java per riformattare i messaggi di traccia dello stack, sostituendo i caratteri di nuova riga con un token. Se si usa la libreria Java Logback, è possibile riformattare i messaggi di traccia dello stack aggiungendo `%replace(%ex){'[\r\n]+', '\\n'}%nopex` come segue:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Quindi, è possibile sostituire il token con i caratteri di nuova riga in Log Analytics come indicato di seguito:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Potrebbe essere possibile usare la stessa strategia per altre librerie di log Java.