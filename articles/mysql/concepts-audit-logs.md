---
title: Log di controllo per database di Azure per MySQL
description: Descrive i log di controllo disponibili nel database di Azure per MySQL e i parametri disponibili per l'abilitazione dei livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434412"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Log di controllo nel database di Azure per MySQL

Nel database di Azure per MySQL il log di controllo è disponibile per gli utenti. Il log di controllo può essere usato per tenere traccia delle attività a livello di database e viene comunemente usato per la conformità.

> [!IMPORTANT]
> La funzionalità del log di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita, il log di controllo è disabilitato. Per abilitarla, impostare `audit_log_enabled` su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla gli eventi da registrare. Vedere la tabella seguente per gli eventi di controllo specifici.
- `audit_log_include_users`: utenti MySQL da includere per la registrazione. Il valore predefinito per questo parametro è vuoto, che include tutti gli utenti per la registrazione. Ha una priorità maggiore rispetto a `audit_log_exclude_users`. La lunghezza massima del parametro è di 512 caratteri.
> [!Note]
> `audit_log_include_users` ha una priorità maggiore rispetto a `audit_log_exclude_users`, ad esempio se audit_log_include_users = `demouser` e audit_log_exclude_users = `demouser`, i log vengono controllati perché `audit_log_include_users` ha una priorità più elevata.
- `audit_log_exclude_users`: utenti MySQL da escludere dalla registrazione. La lunghezza massima del parametro è di 512 caratteri.

> [!Note]
> Per `sql_text`, il log verrà troncato se supera i 2048 caratteri.

| **Event** | **Descrizione** |
|---|---|
| `CONNECTION` | -Avvio della connessione (esito positivo o negativo) <br> -Riautenticazione utente con diversi utenti/password durante la sessione <br> -Terminazione connessione |
| `DML_SELECT`| Seleziona query |
| `DML_NONSELECT` | Query di inserimento, eliminazione e aggiornamento |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query come "DROP DATABASE" |
| `DCL` | Query come "Concedi autorizzazione" |
| `ADMIN` | Query come "Mostra stato" |
| `GENERAL` | Tutto in DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | -Disponibile solo per MySQL 5,7 <br> -Istruzioni Read della tabella, ad esempio SELECT o INSERT INTO... Selezionare <br> -Istruzioni DELETE della tabella, ad esempio DELETE o TRUNCATE TABLE <br> -Istruzioni Table Insert, ad esempio INSERT o REPLACE <br> -Istruzioni Update della tabella, ad esempio UPDATE |

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di controllo nel server MySQL, è possibile crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per altre informazioni su come abilitare i log di diagnostica nella portale di Azure, vedere l' [articolo](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)relativo al portale di log di controllo.

## <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnostica

Le sezioni seguenti descrivono i dati di output dei log di controllo di MySQL in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (disponibile solo per MySQL 5,7) |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
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
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome del server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (disponibile solo per MySQL 5,6) |
| `event_time` | Ora di inizio della query in formato timestamp UTC |
| `error_code_d` | Codice di errore se la query non è riuscita. `0` indica nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
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

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di Azure](howto-configure-audit-logs-portal.md)