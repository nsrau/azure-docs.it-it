---
title: Log di controllo - Database di Azure per MariaDB
description: Descrive i log di controllo disponibili in Database di Azure per MariaDB e i parametri disponibili per l'abilitazione dei livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063822"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Log di controllo nel database di Azure per MariaDBAudit Logs in Azure Database for MariaDB

In Database di Azure per MariaDB il log di controllo è disponibile per gli utenti. Il log di controllo può essere utilizzato per tenere traccia dell'attività a livello di database ed è comunemente usato per la conformità.

> [!IMPORTANT]
> La funzionalità del registro di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita, il registro di controllo è disabilitato. Per attivarla, `audit_log_enabled` impostare su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla gli eventi da registrare. Vedere la tabella seguente per eventi di controllo specifici.
- `audit_log_include_users`: utenti MariaDB da includere per la registrazione. Il valore predefinito per questo parametro è vuoto, che includerà tutti gli utenti per la registrazione. Questo ha una `audit_log_exclude_users`maggiore priorità rispetto a . La lunghezza massima del parametro è di 512 caratteri.
> [!Note]
> `audit_log_include_users`ha una `audit_log_exclude_users`priorità più alta rispetto a . Ad esempio, `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`se e , l'utente verrà `audit_log_include_users` incluso nei registri di controllo perché ha una priorità più alta.
- `audit_log_exclude_users`: utenti MariaDB da escludere dalla registrazione. Consente al massimo quattro utenti. La lunghezza massima del parametro è 256 caratteri.

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

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di Monitoraggio di Azure.Audit logs are integrated with Azure Monitor Diagnostic Logs. Dopo aver abilitato i log di controllo nel server MariaDB, è possibile generarli in log di Monitoraggio di Azure, Hub eventi o Archiviazione di Azure.Once you've enabled audit logs on your MariaDB server, you can emit them to Azure Monitor logs, Event Hubs, or Azure Storage. Per altre informazioni su come abilitare i log di diagnostica nel portale di Azure, vedere l'articolo del portale del log di [controllo.](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)

## <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnosticaDiagnostic Logs Schemas

Nelle sezioni seguenti viene descritto l'output dei log di controllo MariaDB in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

### <a name="connection"></a>Connessione

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | ID connessione univoco generato da MariaDB |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MariaDB |
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
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome del server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Secondi di avvio della query nel timestamp UNIX |
| `error_code_d` | Codice di errore se la query non è riuscita. `0`significa che nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MariaDB |
| `user_s` | Nome dell'utente che esegue la query |
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

- Elencare gli eventi controllati in tutti i server MariaDB con i registri diagnostici abilitati per i log di controllo

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di AzureHow to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)