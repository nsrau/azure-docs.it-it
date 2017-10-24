---
title: Metriche del database SQL di Azure e registrazione diagnostica | Microsoft Docs
description: "Informazioni sulla configurazione del database SQL di Azure per archiviare le statistiche sull'utilizzo delle risorse, la connettività e l'esecuzione delle query."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.openlocfilehash: a56d48eaf335d9e78eeba99162cea7c61d96b7cb
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metriche del database SQL di Azure e registrazione diagnostica 
Il database SQL di Azure può generare metriche e log di diagnostica per facilitare il monitoraggio. È possibile configurare il database SQL di Azure per archiviare l'utilizzo delle risorse, i ruoli di lavoro e sessioni e la connettività in una delle risorse di Azure seguenti:
- **Archiviazione di Azure**: per l'archiviazione di enormi quantità di dati di telemetria a un costo conveniente
- **Hub eventi di Azure**: per l'integrazione dei dati di telemetria del database SQL di Azure con soluzioni di monitoraggio personalizzate o pipeline attive
- **Log Analytics di Azure**: per usare una soluzione di monitoraggio già pronta con funzionalità di reporting, avviso e mitigazione 

    ![architettura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Abilitazione della registrazione

Le metriche e la registrazione diagnostica non sono abilitate per impostazione predefinita. È possibile abilitare e gestire le metriche e la gestione diagnostica usando uno dei metodi seguenti:
- Portale di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- API REST 
- Modello di Resource Manager

Quando si abilitano le metriche e la registrazione diagnostica, è necessario specificare la risorsa di Azure in cui vengono raccolti i dati selezionati. Opzioni disponibili:
- Log Analytics
- Hub eventi
- Archiviazione di Azure 

È possibile eseguire il provisioning di una nuova risorsa di Azure o selezionare una risorsa esistente. Dopo aver selezionato la risorsa di archiviazione, è necessario specificare quali dati raccogliere. Le opzioni disponibili includono:

- **[Metriche 1 minuto](sql-database-metrics-diag-logging.md#1-minute-metrics)**: contiene percentuale DTU, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP
- **[QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics)** - contiene le informazioni sulle statistiche di runtime delle query, ad esempio l'utilizzo della CPU, durata delle query e così via.
- **[QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics)** -contiene le informazioni sulle statistiche di attesa delle query che indicano cosa hanno atteso le query, ad esempio la CPU, il LOG, il BLOCCO...
- **[Errors](sql-database-metrics-diag-logging.md#errors-dataset)**  - contiene le informazioni sugli errori SQL che si sono verificati in questo database.
- **[DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset)** - contiene le informazioni su quanto tempo un database ha atteso nei diversi tipi di attesa.
- **[Timeouts](sql-database-metrics-diag-logging.md#timeouts-dataset)** - contiene le informazioni su quanto tempo un database ha atteso nei diversi tipi di attesa.
- **[Blockings](sql-database-metrics-diag-logging.md#blockings-dataset)** - contiene le informazioni su eventi di blocco verificatisi in un database.
- **[SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset)**  - contiene Intelligent Insights. [Altre informazioni su Intelligent Insights](sql-database-intelligent-insights.md)

Se si specifica l'hub eventi o un account di archiviazione di Azure, è possibile indicare un criterio di conservazione per specificare che vengano eliminati i dati che superano un periodo di tempo selezionato. Se si specifica Log Analytics, i criteri di conservazione dipendono dal piano tariffario selezionato. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

È consigliabile leggere fino in fondo gli articoli [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per comprendere non solo come abilitare la registrazione, ma anche le metriche e le categorie di log supportate dai vari servizi di Azure.

### <a name="azure-portal"></a>Portale di Azure

Per abilitare le metriche e la raccolta di log di diagnostica nel portale di Azure, passare alla pagina del pool elastico o del database SQL di Azure e fare clic su **Impostazioni di diagnostica**.

   ![Abilitazione nel portale di Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

Creare delle nuove impostazioni o modificare le impostazioni di diagnostica esistenti selezionando la destinazione e la telemetria.

   ![Configurate le impostazioni di diagnostica](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Per abilitare le metriche e la registrazione diagnostica tramite PowerShell, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

- Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Per consentire l'invio dei log di diagnostica all'area di lavoro di Log Analytics , usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="to-configure-multiple-azure-subscriptions"></a>Per configurare più sottoscrizioni di Azure

Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell). Specificare l'ID risorsa dell'area di lavoro come parametro quando si esegue lo script per inviare i dati di diagnostica dalle risorse in una sottoscrizione di Azure a un'area di lavoro in un'altra sottoscrizione di Azure.

- Per configurare più sottoscrizioni di Azure, usare i comandi seguenti:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare le metriche e la registrazione diagnostica tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

- Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L'ID regola del bus di servizio è una stringa nel formato seguente:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Per consentire l'invio dei log di diagnostica all'area di lavoro di Log Analytics , usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modello di Resource Manager

Informazioni su come [abilitare automaticamente le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Eseguire lo streaming in Log Analytics 
Le metriche del database SQL Azure e i log di diagnostica possono essere trasmessi in Log Analytics usando l'opzione "Invia a Log Analytics" presente nel portale oppure abilitando Log Analytics in un'impostazione di diagnostica tramite cmdlet di PowerShell di Azure, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

### <a name="installation-overview"></a>Panoramica dell'installazione

Monitorare la flotta del database SQL di Azure è semplice con Log Analytics. Sono necessari tre passaggi:

1. Creare la risorsa Log Analytics
2. Configurare i database per registrare le metriche e i log di diagnostica nella risorsa Log Analytics creata
3. Installare la soluzione **Analisi SQL di Azure** dalla raccolta in Log Analytics

### <a name="create-log-analytics-resource"></a>Creare la risorsa Log Analytics

1. Fare clic su **Nuovo** nel menu a sinistra.
2. Fare clic su **Monitoraggio e gestione**
3. Fare clic su **Log Analytics**
4. Compilare il modulo Log Analytics con le informazioni aggiuntive necessarie: nome dell'area di lavoro, sottoscrizione, gruppo di risorse, posizione e livello di prezzo.

   ![log analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Configurare i database per registrare le metriche e i log di diagnostica

Il modo più semplice per configurare la posizione in cui i database registrano le metriche è tramite il portale di Azure. Nel portale di Azure passare alla risorsa database SQL di Azure e fare clic su **Impostazioni di diagnostica**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Installare la soluzione Analisi SQL di Azure dalla raccolta  

1. Quando la risorsa Log Analytics è stata creata e i dati vengono trasmessi a essa, installare una soluzione Analisi SQL di Azure. Questa operazione può essere eseguita tramite la **raccolta soluzioni** disponibile nella home page di OMS e nel menu laterale. Nella raccolta, trovare e fare clic sulla soluzione **Analisi SQL di Azure** e fare clic su **Aggiungi**.

   ![soluzione di monitoraggio](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Nella home page di OMS viene visualizzato un nuovo riquadro chiamato **Analisi SQL di Azure**. Se si seleziona questo riquadro viene aperto il dashboard di Analisi SQL di Azure.

### <a name="using-azure-sql-analytics-solution"></a>Uso della soluzione Analisi SQL di Azure

Analisi SQL di Azure è un dashboard gerarchico che consente di spostarsi nella gerarchia di risorse del database SQL di Azure. [Fare clic qui per informazioni su come usare la soluzione Analisi SQL di Azure](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-azure-event-hub"></a>Eseguire lo streaming nell'hub eventi di Azure

Le metriche del database SQL Azure e i log di diagnostica possono essere trasmessi nell'hub eventi usando l'opzione "Stream to an event hub" (Esegui streaming in un Hub eventi) presente nel portale oppure abilitando l'ID regola del bus di servizio in un'impostazione di diagnostica tramite cmdlet di PowerShell di Azure, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>Cosa fare con le metriche e i log di diagnostica nell'hub eventi?
Una volta eseguito lo streaming dei dati selezionati nell'hub eventi, si è un passo più vicini all'abilitazione di scenari di monitoraggio avanzati. Hub eventi funge da "porta principale" per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, è possibile trasformarli e archiviarli tramite qualsiasi provider di analisi in tempo reale o adattatore di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione. Per altre informazioni sull'hub eventi, vedere:

- [Cosa sono gli hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
- [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Ecco alcuni esempi di come è possibile usare la funzionalità di trasmissione:

-             Visualizzare lo stato di integrità del servizio mediante la trasmissione di dati sul "percorso critico" a Power BI: hub eventi, analisi di flusso e Power BI permettono di trasformare facilmente i dati di metriche e diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per una panoramica della configurazione dell'hub eventi, dell'elaborazione dei dati con analisi di flusso e dell'uso di Power BI come output, vedere [Analisi di flusso e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-             Eseguire lo streaming dei log in stream di registrazione e telemetria di terze parti: usando lo streaming degli hub eventi è possibile trasmettere le metriche e i log di diagnostica a diverse soluzioni di monitoraggio e analisi di log di terze parti. 
-             Compilare una piattaforma di registrazione e telemetria personalizzata: se è disponibile una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili dell'hub eventi offrono grande flessibilità per l'inserimento dei log di diagnostica. [Vedere la guida all'uso dell'hub eventi in una piattaforma di telemetria su scala globale di Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Eseguire lo streaming in Archiviazione di Azure

Le metriche del database SQL Azure e i log di diagnostica possono essere archiviati in Archiviazione di Azure usando l'opzione "Archive to a storage account" (Archivia in un account di archiviazione) presente nel portale di Azure oppure abilitando Archiviazione di Azure in un'impostazione di diagnostica tramite cmdlet di PowerShell di Azure, l'interfaccia della riga di comando di Azure o l'API REST di Monitoraggio di Azure.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Schema delle metriche e dei log di diagnostica nell'account di archiviazione

Dopo aver configurato la raccolta delle metriche e dei log di diagnostica, viene creato un contenitore di archiviazione nell'account di archiviazione selezionato quando sono disponibili le prime righe di dati. La struttura di questi BLOB è:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
O, più semplicemente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, un nome del BLOB per la metrica 1 minuto potrebbe essere:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Se si vuole registrare i dati dal pool elastico, il nome del BLOB è leggermente diverso:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Scaricare le metriche e i log da Archiviazione di Azure

Vedere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

## <a name="metrics-and-logs-available"></a>Le metriche e i log disponibili

### <a name="1-minute-metrics"></a>metriche 1 minuto

|**Risorsa**|**Metriche**|
|---|---|
|Database|Percentuale DTU, DTU utilizzata, limite DTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, riuscito/non riuscito/bloccato dalle connessioni firewall, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, percentuale di archiviazione XTP, deadlock |
|Pool elastico|Percentuale eDTU, eDTU utilizzata, limite eDTU, percentuale CPU, percentuale lettura dati fisici, percentuale scrittura log, percentuale sessioni, percentuale ruoli di lavoro, risorsa di archiviazione, percentuale di archiviazione, limite di archiviazione, percentuale di archiviazione XTP |
|||

### <a name="query-store-runtime-statistics"></a>Statistiche di runtime di Query Store

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|query_hash_s|Hash di query|
|query_plan_hash_s|Hash del piano di query|
|statement_sql_handle_s|Punto di controllo dell'istruzione SQL|
|interval_start_time_d|Datetimeoffset iniziale dell'intervallo in numero di tick da 1900-1-1.|
|interval_end_time_d|Datetimeoffset finale dell'intervallo in numero di tick da 1900-1-1.|
|logical_io_writes_d|Numero totale di scritture di I/O logiche.|
|max_logical_io_writes_d|Numero massimo di scritture di I/O logiche per esecuzione.|
|physical_io_reads_d|Numero totale di letture di I/O fisiche.|
|max_physical_io_reads_d|Numero massimo letture di I/O logiche per esecuzione.|
|logical_io_reads_d|Numero totale di letture di I/O logiche.|
|max_logical_io_reads_d|Numero massimo letture di I/O logiche per esecuzione.|
|execution_type_d|Tipo di esecuzione|
|count_executions_d|Numero di esecuzioni della query.|
|cpu_time_d|Tempo totale della CPU usato dalla query in microsecondi.|
|max_cpu_time_d|Consumer massimo del tempo di CPU di una singola esecuzione in microsecondi.|
|dop_d|Somma dei gradi di parallelismo.|
|max_dop_d|Massimo grado parallelismo usato per una singola esecuzione.|
|rowcount_d|Numero di righe totali restituite.|
|max_rowcount_d|Numero massimo di righe restituite in una singola esecuzione.|
|query_max_used_memory_d|Quantità totale di memoria usata in KB.|
|max_query_max_used_memory_d|Quantità massima di memoria usata da una singola esecuzione in KB.|
|duration_d|Tempo di esecuzione totale in microsecondi.|
|max_duration_d|Tempo massimo di esecuzione di una singola esecuzione.|
|num_physical_io_reads_d|Numero totale di letture fisiche.|
|max_num_physical_io_reads_d|Numero massimo di scritture fisiche per esecuzione.|
|log_bytes_used_d|Quantità totale di byte di log usati.|
|max_log_bytes_used_d|Quantità massima di byte di log usati per esecuzione.|
|query_id_d|ID della query in Query Store|
|plan_id_d|ID del piano in Query Store|

[Ulteriori informazioni sui dati delle statistiche di runtime di Query Store.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>Statistiche relative alle attese di Query Store

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|wait_category_s|Categoria dell'attesa.|
|is_parameterizable_s|La query è parametrizzabile.|
|statement_type_s|Tipo di istruzione.|
|statement_key_hash_s|Hash della chiave di istruzione.|
|exec_type_d|Tipo di esecuzione|
|total_query_wait_time_ms_d|Tempo di attesa totale della query nella categoria di attesa specifica.|
|max_query_wait_time_ms_d|Tempo di attesa massimo della query nella singola esecuzione nella categoria di attesa specifica|
|query_param_type_d|0|
|query_hash_s|Hash di query in Query Store.|
|query_plan_hash_s|Hash del piano di query in Query Store|
|statement_sql_handle_s|Handle di istruzione in Query Store|
|interval_start_time_d|Datetimeoffset iniziale dell'intervallo in numero di tick da 1900-1-1.|
|interval_end_time_d|Datetimeoffset finale dell'intervallo in numero di tick da 1900-1-1.|
|count_executions_d|Numero di esecuzioni della query|
|query_id_d|ID della query in Query Store|
|plan_id_d|ID del piano in Query Store|

[Fare clic qui per ulteriori informazioni sui dati delle statistiche di attesa di Query Store.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>Set di dati di errori

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|Message|Messaggio di errore in testo normale|
|user_defined_b|È il bit di errore definito dall'utente|
|error_number_d|Codice di errore|
|Severity|Gravità dell'errore|
|state_d|Stato dell'errore|
|query_hash_s|Hash di query della query non riuscita, se disponibile|
|query_plan_hash_s|Hash del piano di query della query non riuscita, se disponibile|

[Messaggi di errore di SQL Server](https://msdn.microsoft.com/en-us/library/cc645603.aspx)

### <a name="database-waits-dataset"></a>Set di dati delle attese del database

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|wait_type_s|Nome del tipo di attesa|
|start_utc_date_t [UTC]|Ora di inizio del periodo misurato.|
|end_utc_date_t [UTC]|Ora di fine del periodo misurato.|
|delta_max_wait_time_ms_d|Tempo massimo tempo di attesa per esecuzione|
|delta_signal_wait_time_ms_d|Tempo di attesa totale del segnale|
|delta_wait_time_ms_d|Tempo di attesa totale nel periodo|
|delta_waiting_tasks_count_d|Numero di attività in attesa|

[Fare clic qui per ulteriori informazioni sui dati delle statistiche di attesa del database.](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="timeouts-dataset"></a>Set di dati dei timeout

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|error_state_d|Codice di stato dell'errore|
|query_hash_s|Hash di query, se disponibile|
|query_plan_hash_s|Hash del piano di query, se disponibile|

### <a name="blockings-dataset"></a>Set di dati dei blocchi

|Proprietà|Descrizione|
|---|---|
|TenantId|L'ID del tenant.|
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Timestamp di quando è stato registrato il log.|
|Tipo|Always: AzureDiagnostics|
|ResourceProvider|Nome del provider di risorse. Always: MICROSOFT.SQL|
|Categoria|Nome della categoria. Always: QueryStoreRuntimeStatistics|
|OperationName|Nome dell'operazione. Always: QueryStoreRuntimeStatisticsEvent|
|Risorsa|Nome della risorsa|
|ResourceType|Nome del tipo di risorsa. Always: SERVERS/DATABASES|
|SubscriptionId|GUID della sottoscrizione a cui appartiene il database.|
|ResourceGroup|Nome del gruppo di risorse al quale appartiene il database.|
|LogicalServerName_s|Nome del server al quale appartiene il database.|
|ElasticPoolName_s|Nome del pool di database elastico al quale appartiene il database, se esistente.|
|DatabaseName_s|Nome del database|
|ResourceId|URI della risorsa|
|lock_mode_s|Modalità di blocco usata dalla query|
|resource_owner_type_s|Proprietario del blocco.|
|blocked_process_filtered_s|Report XML del processo bloccato.|
|duration_d|Durata del blocco in millisecondi.|

### <a name="intelligent-insights-dataset"></a>Set di dati di Intelligent Insights
[Fare clic qui per ulteriori informazioni sul formato di log di Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>Passaggi successivi

- Leggere fino in fondo gli articoli [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per comprendere non solo come abilitare la registrazione, ma anche le metriche e le categorie di log supportate dai vari servizi di Azure.
- Per informazioni sugli hub eventi, leggere gli articoli seguenti:
   - [Cosa sono gli hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
   - [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Vedere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
