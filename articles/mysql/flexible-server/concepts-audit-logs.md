---
title: Log di controllo-database di Azure per MySQL-server flessibile
description: Descrive i log di controllo disponibili nel server flessibile database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: e630ccec744c4edf0dc80d374ecc93526f960d44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295990"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Tenere traccia dell'attività del database con i log di controllo nel database di Azure per MySQL server flessibile

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Il server flessibile database di Azure per MySQL offre agli utenti la possibilità di configurare i log di controllo. I log di controllo possono essere utilizzati per tenere traccia delle attività a livello di database, inclusi eventi di connessione, amministrazione, DDL e DML. Questi tipi di log vengono comunemente usati per finalità di conformità.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

>[!IMPORTANT]
> Si consiglia di registrare solo i tipi di evento e gli utenti necessari a scopo di controllo, in modo da garantire che le prestazioni del server non siano fortemente interessate.

Per impostazione predefinita, i log di controllo sono disabilitati. Per abilitarle, impostare il `audit_log_enabled` parametro Server *su on*. Questa configurazione può essere eseguita usando l'interfaccia della riga di comando portale di Azure o Azure <!-- add link to server parameter-->. 

Altri parametri che è possibile modificare per controllare il comportamento della registrazione di controllo includono:

- `audit_log_events`: controlla gli eventi da registrare. Vedere la tabella seguente per gli eventi di controllo specifici.
- `audit_log_include_users`: Utenti MySQL da includere per la registrazione. Il valore predefinito per questo parametro è vuoto, che include tutti gli utenti per la registrazione. Questa operazione ha una priorità più elevata rispetto a `audit_log_exclude_users` . La lunghezza massima del parametro è di 512 caratteri.
- `audit_log_exclude_users`: Utenti di MySQL da escludere dalla registrazione. La lunghezza massima del parametro è di 512 caratteri.

> [!NOTE]
> `audit_log_include_users` ha una priorità maggiore rispetto a `audit_log_exclude_users` . Se, ad esempio, `audit_log_include_users`  =  `demouser` e `audit_log_exclude_users`  =  `demouser` , l'utente verrà incluso nei log di controllo perché `audit_log_include_users` ha una priorità più elevata.

| **Event** | **Descrizione** |
|---|---|
| `CONNECTION` | -Avvio della connessione (esito positivo o negativo) <br> -Riautenticazione utente con diversi utenti/password durante la sessione <br> -Terminazione connessione |
| `DML_SELECT`| Query SELECT |
| `DML_NONSELECT` | Query di inserimento, eliminazione e aggiornamento |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query come "DROP DATABASE" |
| `DCL` | Query come "Concedi autorizzazione" |
| `ADMIN` | Query come "Mostra stato" |
| `GENERAL` | Tutto in DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | -Disponibile solo per MySQL 5,7 <br> -Istruzioni Read della tabella, ad esempio SELECT o INSERT INTO... Selezionare <br> -Istruzioni DELETE della tabella, ad esempio DELETE o TRUNCATE TABLE <br> -Istruzioni Table Insert, ad esempio INSERT o REPLACE <br> -Istruzioni Update della tabella, ad esempio UPDATE |

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con le impostazioni di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di controllo nel server MySQL flessibile, è possibile crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per ulteriori informazioni sulle impostazioni di diagnostica, vedere la [documentazione relativa ai log di diagnostica](../../azure-monitor/platform/platform-logs-overview.md). Per ulteriori informazioni su come abilitare le impostazioni di diagnostica nella portale di Azure, vedere l' [articolo](how-to-configure-audit-logs-portal.md#set-up-diagnostics)relativo al portale di log di controllo.

Le sezioni seguenti descrivono l'output dei log di controllo di MySQL in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

### <a name="connection"></a>Connessioni

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome del server |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (disponibile solo per MySQL 5,7) |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `db_s` | Nome del database connesso a |
| `\_ResourceId` | URI della risorsa |

### <a name="general"></a>Generale

Lo schema riportato di seguito si applica ai tipi di evento GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

> [!NOTE]
> Per `sql_text_s` , log verrà troncato se supera i 2048 caratteri.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome del server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (disponibile solo per MySQL 5,6) |
| `event_time` | Ora di inizio della query in formato timestamp UTC |
| `error_code_d` | Codice di errore se la query non è riuscita. `0` indica nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `sql_text_s` | Testo completo della query |
| `\_ResourceId` | URI della risorsa |

### <a name="table-access"></a>Accesso alla tabella

> [!NOTE]
> I log di accesso alle tabelle vengono restituiti solo per MySQL 5,7.<br>Per `sql_text_s` , log verrà troncato se supera i 2048 caratteri.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome del server |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` o `DELETE` |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `db_s` | Nome del database a cui si accede |
| `table_s` | Nome della tabella a cui si accede |
| `sql_text_s` | Testo completo della query |
| `\_ResourceId` | URI della risorsa |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizzare i log nei log di monitoraggio di Azure

Quando i log di controllo vengono inviati tramite pipe ai log di monitoraggio di Azure tramite i log di diagnostica, è possibile eseguire un'ulteriore analisi degli eventi controllati. Di seguito sono riportate alcune query di esempio utili per iniziare. Assicurarsi di aggiornare quanto segue con il nome del server.

- Elencare gli eventi generali in un determinato server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Elencare gli eventi di connessione in un determinato server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Riepilogare gli eventi controllati in un determinato server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Grafico della distribuzione del tipo di evento di controllo in un determinato server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Elencare gli eventi controllati in tutti i server MySQL con i log di diagnostica abilitati per i log di controllo

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [log di query lente](concepts-slow-query-logs.md)
- Configurare i log di query di controllo dalla [portale di Azure](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->