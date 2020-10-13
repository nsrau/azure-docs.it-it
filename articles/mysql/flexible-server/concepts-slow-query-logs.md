---
title: Log di query lente-database di Azure per MySQL-server flessibile
description: Descrive i log di query lente disponibili nel server flessibile database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 512a103f5ac116f1f4eb18631cdc8e4a8b9380c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399211"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Log di query lente nel server flessibile database di Azure per MySQL (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Nel database di Azure per MySQL server flessibile, il log di query lente è disponibile per gli utenti per la configurazione e l'accesso. I log di query lente sono disabilitati per impostazione predefinita e possono essere abilitati per facilitare l'identificazione di colli di bottiglia delle prestazioni durante la risoluzione dei problemi.

Per ulteriori informazioni sul log di query lente di MySQL, vedere la [sezione Slow query log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) nella documentazione del motore MySQL.

## <a name="configure-slow-query-logging"></a>Configurare la registrazione lenta delle query 
Per impostazione predefinita, il log di query lente è disabilitato. Per abilitare i log, impostare il `slow_query_log` parametro Server *su on*. Questa configurazione può essere eseguita usando l'interfaccia della riga di comando portale di Azure o Azure <!-- add link to server parameter-->. 

Altri parametri che è possibile modificare per controllare il comportamento della registrazione lenta delle query includono:

- **long_query_time**: registra una query se richiede più tempo di `long_query_time` (in secondi) per il completamento. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: determina se le istruzioni amministrative (ad esempio `ALTER_TABLE`, `ANALYZE_TABLE` ) vengono registrati.
- **log_queries_not_using_indexes**: determina se le query che non utilizzano indici vengono registrate.
- **log_throttle_queries_not_using_indexes**: limita il numero di query non indicizzate che possono essere scritte nel log di query lente. Questo parametro viene applicato quando `log_queries_not_using_indexes` è impostato *su on* .

> [!IMPORTANT]
> Se le tabelle non sono indicizzate, l'impostazione `log_queries_not_using_indexes` dei `log_throttle_queries_not_using_indexes` parametri e **su on** può influire sulle prestazioni di MySQL perché tutte le query in esecuzione in queste tabelle non indicizzate verranno scritte nel log di query lente.

Per una descrizione completa dei parametri per il log di query lente, vedere la [documentazione sul log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) per MySQL.

## <a name="access-slow-query-logs"></a>Accedere ai log di query lente

I log di query lente sono integrati con le impostazioni di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di query lente sul server MySQL flessibile, è possibile crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per ulteriori informazioni sulle impostazioni di diagnostica, vedere la [documentazione relativa ai log di diagnostica](../../azure-monitor/platform/platform-logs-overview.md). Per altre informazioni su come abilitare le impostazioni di diagnostica nella portale di Azure, vedere l' [articolo relativo al portale di log di query lente](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

Nella tabella seguente viene descritto l'output del log di query lente. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Timestamp in cui il log è stato registrato in formato UTC. |
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
| `start_time_t` UTC | Ora di inizio della query |
| `query_time_s` | Tempo totale in secondi impiegato per l'esecuzione della query |
| `lock_time_s` | Tempo totale in secondi per cui la query è stata bloccata |
| `user_host_s` | Username |
| `rows_sent_s` | Numero di righe inviate |
| `rows_examined_s` | Numero di righe esaminate |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserisci ID |
| `sql_text_s` | Query completa |
| `server_id_s` | ID del server |
| `thread_id_s` | ID del thread |
| `\_ResourceId` | URI della risorsa |

> [!Note]
> Per `sql_text_s` , log verrà troncato se supera i 2048 caratteri.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizzare i log nei log di monitoraggio di Azure

Quando i log di query lente vengono inviati tramite pipe ai log di monitoraggio di Azure tramite i log di diagnostica, è possibile eseguire un'ulteriore analisi delle query lente. Di seguito sono riportate alcune query di esempio utili per iniziare. Assicurarsi di aggiornare quanto segue con il nome del server.

- Query con più di 10 secondi in un determinato server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Elenca le prime cinque query più lunghe su un server specifico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Riepilogare query lente per tempo di query minimo, massimo, medio e di deviazione standard in un determinato server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafico della distribuzione di query lente in un determinato server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visualizza query per più di 10 secondi in tutti i server MySQL con i log di diagnostica abilitati

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [log di controllo](concepts-audit-logs.md)
- Configurare i log di query lente dal [portale di Azure](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->