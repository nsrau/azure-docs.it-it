---
title: 'Log di controllo: database di Azure per MariaDB'
description: Descrive i log di controllo disponibili nel database di Azure per MariaDB e i parametri disponibili per l'abilitazione dei livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 9c5f6aa2900570aa00ddbc50ec8be4dbb0d16a34
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978050"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Log di controllo nel database di Azure per MariaDB

Nel database di Azure per MariaDB il log di controllo è disponibile per gli utenti. Il log di controllo può essere usato per tenere traccia delle attività a livello di database e viene comunemente usato per la conformità.

> [!IMPORTANT]
> La funzionalità del log di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita, il log di controllo è disabilitato. Per abilitarla, impostare `audit_log_enabled` su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla gli eventi da registrare. Vedere la tabella seguente per gli eventi di controllo specifici.
- `audit_log_include_users`: MariaDB gli utenti da includere per la registrazione. Il valore predefinito per questo parametro è vuoto, che include tutti gli utenti per la registrazione. Questa operazione ha una priorità più elevata rispetto a `audit_log_exclude_users`. La lunghezza massima del parametro è di 512 caratteri.
> [!Note]
> `audit_log_include_users` ha una priorità maggiore rispetto a `audit_log_exclude_users`. Se, ad esempio, `audit_log_include_users` = `demouser` e `audit_log_exclude_users` = `demouser`, l'utente verrà incluso nei log di controllo perché `audit_log_include_users` ha una priorità più elevata.
- `audit_log_exclude_users`: MariaDB gli utenti da escludere dalla registrazione. Consente al massimo quattro utenti. La lunghezza massima del parametro è di 256 caratteri.

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

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di controllo nel server MariaDB, è possibile crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per altre informazioni su come abilitare i log di diagnostica nella portale di Azure, vedere l' [articolo](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)relativo al portale di log di controllo.

## <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnostica

Le sezioni seguenti descrivono gli elementi di output dei log di controllo di MariaDB in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

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

### <a name="general"></a>Informazioni di carattere generale

Lo schema riportato di seguito si applica ai tipi di evento GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

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
| `error_code_d` | Codice di errore se la query non è riuscita. `0` indica nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MariaDB |
| `user_s` | Nome dell'utente che esegue la query |
| `sql_text_s` | Testo completo della query |
| `\_ResourceId` | URI della risorsa |

### <a name="table-access"></a>Accesso alla tabella

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
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` o `DELETE` |
| `connection_id_d` | ID connessione univoco generato da MariaDB |
| `db_s` | Nome del database a cui si accede |
| `table_s` | Nome della tabella a cui si accede |
| `sql_text_s` | Testo completo della query |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di Azure](howto-configure-audit-logs-portal.md)