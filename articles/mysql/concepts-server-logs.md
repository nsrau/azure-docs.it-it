---
title: Log di query lenti - Database di Azure per MySQLSlow query logs - Azure Database for MySQL
description: Descrive i log di query lenti disponibili nel database di Azure per MySQL e i parametri disponibili per abilitare diversi livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269432"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Log di query lenti nel database di Azure per MySQLSlow query logs in Azure Database for MySQL
Nel Database di Azure per MySQL, il log delle query lente è disponibile per gli utenti. L'accesso al log delle transazioni non è supportato. Il log delle query lente può essere usato per identificare eventuali colli di bottiglia delle prestazioni e procedere alla risoluzione dei problemi.

Per altre informazioni sul log delle query lente MySQL, vedere la [sezione relativa ai log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) del manuale di riferimento per MySQL.

## <a name="access-slow-query-logs"></a>Accedere ai registri delle query lente
È possibile elencare e scaricare il database di Azure per i log delle query lente MySQL usando il portale di Azure e l'interfaccia della riga di comando di Azure.You can list and download Azure Database for MySQL slow query logs using the Azure portal, and the Azure CLI.

Nel portale di Azure selezionare il server del Database di Azure per MySQL. Nell'intestazione **Monitoraggio** selezionare la pagina **Log del server**.

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Configurare e accedere alogi di query lenti usando l'interfaccia della riga di comando](howto-configure-server-logs-in-cli.md)di Azure.For more information on Azure CLI, see Configure and access slow query logs using Azure CLI.

Analogamente, è possibile reindirizzare i log ad Monitoraggio di Azure usando i log di diagnostica. Per ulteriori informazioni, vedere [di seguito.](concepts-server-logs.md#diagnostic-logs)

## <a name="log-retention"></a>Conservazione dei log
I log sono disponibili per un massimo di sette giorni dalla data di creazione. Se le dimensioni totali dei log disponibili superano 7 GB, i file meno recenti vengono eliminati fino a quando non è disponibile dello spazio. 

I log vengono ruotati ogni 24 ore o 7 GB, a seconda del valore raggiunto per primo.

## <a name="configure-slow-query-logging"></a>Configurare la registrazione delle query lente 
Per impostazione predefinita il log delle query lente è disabilitato. Per abilitarlo, impostare slow_query_log su ON.

Altri parametri che è possibile modificare includono:

- **long_query_time**: se una query richiede più tempo del valore di long_query_time (in secondi), la query viene registrata. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: se è ON include le istruzioni a livello amministrativo come ALTER_TABLE e ANALYZE_TABLE nelle istruzioni scritte in slow_query_log.
- **log_queries_not_using_indexes**: determina se le query che non usano gli indici vengono registrate in slow_query_log
- **log_throttle_queries_not_using_indexes**: questo parametro limita il numero di query non di indice che possono essere scritte nel log di query lente. Questo parametro ha effetto quando log_queries_not_using_indexes è impostato su ON.
- **log_output**: se "File", consente di scrivere il log di query lento sia nell'archivio del server locale che nei log di diagnostica di Monitoraggio di Azure.A: if "File", allows the slow query log to be written to both the local server storage and to Azure Monitor Diagnostic Logs. Se "Nessuno", il log di query lento verrà scritto solo nei log di diagnostica di Monitoraggio di Azure.If "None", the slow query log will only be written to Azure Monitor Diagnostics Logs. 

> [!IMPORTANT]
> Se le tabelle non sono `log_queries_not_using_indexes` indicizzate, l'impostazione dei parametri e su `log_throttle_queries_not_using_indexes` ON può influire sulle prestazioni di MySQL poiché tutte le query eseguite su queste tabelle non indicizzate verranno scritte nel log di query lento.<br><br>
> Se si prevede di registrare query lente per un `log_output` periodo di tempo prolungato, si consiglia di impostare su "Nessuno". Se impostato su "File", questi log vengono scritti nell'archivio del server locale e possono influire sulle prestazioni di MySQL. 

Per una descrizione completa dei parametri per il log di query lente, vedere la [documentazione sul log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) per MySQL.

## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per MySQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo aver abilitato i log delle query lente nel server MySQL, è possibile scegliere di generarli nei log di Monitoraggio di Azure, negli hub eventi o in Archiviazione di Azure.Once you have enabled slow query logs on your MySQL server, you can choose to have mitted them to Azure Monitor logs, Event Hubs, or Azure Storage. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../azure-monitor/platform/platform-logs-overview.md).

La tabella seguente descrive il contenuto di ogni log. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome del server |
| `start_time_t`[UTC] | Ora di inizio della query |
| `query_time_s` | Tempo totale in secondi impiegato per l'esecuzione della query |
| `lock_time_s` | Tempo totale in secondi in cui la query è stata bloccata |
| `user_host_s` | Username |
| `rows_sent_s` | Numero di righe inviate |
| `rows_examined_s` | Numero di righe esaminate |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserisci ID |
| `sql_text_s` | Query completa |
| `server_id_s` | ID del server |
| `thread_id_s` | ID del thread |
| `\_ResourceId` | URI della risorsa |

> [!Note]
> Per `sql_text`, il log verrà troncato se supera i 2048 caratteri.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizzare i log nei log di Monitoraggio di AzureAnalyze logs in Azure Monitor Logs

Dopo aver reindirizzato i log di query lenti ai log di Monitoraggio di Azure tramite i log di diagnostica, è possibile eseguire un'ulteriore analisi delle query lente. Di seguito sono riportate alcune query di esempio che consentono di iniziare. Assicurarsi di aggiornare quanto segue con il nome del server.

- Query più lunghe di 10 secondi su un server specifico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Elencare le 5 query più lunghe in un server specifico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Riepilogare le query lente in base al tempo di query di deviazione minima, massima, media e standard in un determinato server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Rappresentare graficamente la distribuzione lenta delle query in un server specifico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visualizzare le query più lunghe di 10 secondi in tutti i server MySQL con i registri di diagnostica abilitati

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Passaggi successivi
- [Come configurare log di query lenti dal portale di AzureHow to configure slow query logs from the Azure portal](howto-configure-server-logs-in-portal.md)
- [Come configurare log di query lenti dall'interfaccia della riga di comando](howto-configure-server-logs-in-cli.md)di Azure.
