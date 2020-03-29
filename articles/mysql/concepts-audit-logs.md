---
title: Log di controllo - Database di Azure per MySQLAudit logs - Azure Database for MySQL
description: Descrive i log di controllo disponibili in Database di Azure per MySQL e i parametri disponibili per l'abilitazione dei livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062551"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Log di controllo nel database di Azure per MySQLAudit Logs in Azure Database for MySQL

In Database di Azure per MySQL il log di controllo è disponibile per gli utenti. Il log di controllo può essere utilizzato per tenere traccia dell'attività a livello di database ed è comunemente usato per la conformità.

> [!IMPORTANT]
> La funzionalità del registro di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita, il registro di controllo è disabilitato. Per attivarla, `audit_log_enabled` impostare su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla gli eventi da registrare. Vedere la tabella seguente per eventi di controllo specifici.
- `audit_log_include_users`: utenti MySQL da includere per la registrazione. Il valore predefinito per questo parametro è vuoto, che includerà tutti gli utenti per la registrazione. Questo ha una `audit_log_exclude_users`maggiore priorità rispetto a . La lunghezza massima del parametro è di 512 caratteri.
> [!Note]
> `audit_log_include_users`ha una `audit_log_exclude_users`priorità più alta rispetto a . Ad esempio, `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`se e , l'utente verrà `audit_log_include_users` incluso nei registri di controllo perché ha una priorità più alta.
- `audit_log_exclude_users`: utenti MySQL da escludere dalla registrazione. La lunghezza massima del parametro è di 512 caratteri.

> [!Note]
> Per `sql_text`, il log verrà troncato se supera i 2048 caratteri.

| **Evento** | **Descrizione** |
|---|---|
| `CONNECTION` | - Avvio connessione (operazione riuscita o non riuscita) <br> - Riautenticazione utente con utente/password diversi durante la sessione <br> - Chiusura della connessione |
| `DML_SELECT`| Query SELECT |
| `DML_NONSELECT` | Query INSERT/DELETE/UPDATE |
| `DML` | DML - DML_SELECT DML_NONSELECT |
| `DDL` | Query come "DROP DATABASE" |
| `DCL` | Query come "GRANT PERMISSION" |
| `ADMIN` | Query come "SHOW STATUS" |
| `GENERAL` | Tutti in DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | - Disponibile solo per MySQL 5.7 <br> - Istruzioni di lettura tabella, ad esempio SELECT o INSERT INTO ... Selezionare <br> - Istruzioni delete tabella, ad esempio DELETE o TRUNCATE TABLE <br> - Istruzioni di inserimento tabella, ad esempio INSERT o REPLACE <br> - Istruzioni di aggiornamento tabella, ad esempio UPDATE- Table update statements, such as UPDATE |

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di Monitoraggio di Azure.Audit logs are integrated with Azure Monitor Diagnostic Logs. Dopo aver abilitato i log di controllo nel server MySQL, è possibile generarli in log di Monitoraggio di Azure, Hub eventi o Archiviazione di Azure.Once you've enabled audit logs on your MySQL server, you can emit them to Azure Monitor logs, Event Hubs, or Azure Storage. Per altre informazioni su come abilitare i log di diagnostica nel portale di Azure, vedere l'articolo del portale del log di [controllo.](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)

## <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnosticaDiagnostic Logs Schemas

Nelle sezioni seguenti vengono descritti i risultati restituiti dai log di controllo MySQL in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

### <a name="connection"></a>Connessione

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` , (disponibile solo per MySQL 5.7) |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `db_s` | Nome del database connesso a |
| `\_ResourceId` | URI della risorsa |

### <a name="general"></a>Generale

Lo schema seguente si applica ai tipi di evento GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

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
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` , (disponibile solo per MySQL 5.6) |
| `event_time` | Ora di inizio della query nel timestamp UTC |
| `error_code_d` | Codice di errore se la query non è riuscita. `0`significa che nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `sql_text_s` | Testo completo della query |
| `\_ResourceId` | URI della risorsa |

### <a name="table-access"></a>Accesso alla tabella

> [!NOTE]
> I log di accesso alle tabelle vengono restituiti solo per MySQL 5.7.Table access logs are only output for MySQL 5.7.

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

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analizzare i log nei log di Monitoraggio di AzureAnalyze logs in Azure Monitor Logs

Dopo aver reindirizzato i log di controllo ai log di Monitoraggio di Azure tramite i log di diagnostica, è possibile eseguire un'ulteriore analisi degli eventi controllati. Di seguito sono riportate alcune query di esempio che consentono di iniziare. Assicurarsi di aggiornare quanto segue con il nome del server.

- Elencare gli eventi GENERAL in un determinato server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Elencare gli eventi CONNECTION in un server specificoList CONNECTION events on a particular server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Riepilogare gli eventi controllati in un server specifico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Grafico della distribuzione del tipo di evento di controllo su un server specifico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Elencare gli eventi controllati in tutti i server MySQL con i log di diagnostica abilitati per i log di controlloList audited events across all MySQL servers with Diagnostic Logs enabled for audit logs

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di AzureHow to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)