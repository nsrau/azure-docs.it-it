---
title: Log di controllo per Database di Azure per MySQL
description: Descrive i log di controllo disponibili nel Database di Azure per MySQL e i parametri disponibili per l'abilitazione di livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443853"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Log di controllo nel Database di Azure per MySQL

Nel Database di Azure per MySQL, il log di controllo è disponibile per gli utenti. Il log di controllo può essere utilizzato per tenere traccia delle attività a livello di database e viene usato comunemente per la conformità.

> [!IMPORTANT]
> Funzionalità di log di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita il log di controllo è disabilitato. Per abilitarla, impostare `audit_log_enabled` su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla il registrazione di eventi. Vedere di seguito nella tabella per gli eventi di controllo specifico.
- `audit_log_exclude_users`: Utenti MySQL da escludere dalla registrazione. Consente al massimo quattro utenti. Lunghezza massima consentita del parametro è 256 caratteri.

| **Event** | **Descrizione** |
|---|---|
| `CONNECTION` | -Avvio della connessione (esito positivo o negativo) <br> -Riautenticazione utente con utente/password diversa durante la sessione <br> : Interruzioni di connessione |
| `DML_SELECT`| Query SELECT |
| `DML_NONSELECT` | Query INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Esegue una query come "DROP DATABASE" |
| `DCL` | Esegue una query come "Concedi autorizzazione" |
| `ADMIN` | Esegue una query come "Mostra stato" |
| `GENERAL` | Tutto in DML_SELECT DML_NONSELECT, DML, DDL, raccolta connessioni dati e amministrazione |
| `TABLE_ACCESS` | -Disponibile solo per MySQL 5.7 <br> -Tabella altre istruzioni, ad esempio SELECT o INSERT INTO... SELECT <br> -Le istruzioni delete table, ad esempio DELETE o TRUNCATE TABLE <br> -Le istruzioni insert table, ad esempio inserimento o sostituzione <br> -Istruzioni update table, ad esempio aggiornamento |

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di controllo nel proprio server MySQL, è possibile generarli da log di monitoraggio di Azure, hub eventi o archiviazione di Azure. Per altre informazioni su come abilitare i log di diagnostica nel portale di Azure, vedere la [audit log portale articolo](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schemi dei log di diagnostica

Le sezioni seguenti descrivono ciò che è l'output del log di controllo di MySQL in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (disponibile solo per MySQL 5.7) |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `db_s` | Nome del database connesso a |
| `\_ResourceId` | URI della risorsa |

### <a name="general"></a>Generale

Schema seguente si applica ai tipi di evento generale, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e amministratore.

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
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (disponibile solo per MySQL 5.6) |
| `event_time` | Query avviare secondi in timestamp UNIX dell'ora |
| `error_code_d` | Codice di errore se query non è riuscita. `0` indica che nessun errore |
| `thread_id_d` | ID del thread che ha eseguito la query |
| `host_s` | Vuoto |
| `ip_s` | Indirizzo IP del client che si connette a MySQL |
| `user_s` | Nome dell'utente che esegue la query |
| `sql_text_s` | Testo query completo |
| `\_ResourceId` | URI della risorsa |

### <a name="table-access"></a>Accesso alle tabelle

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
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, o `DELETE` |
| `connection_id_d` | ID connessione univoco generato da MySQL |
| `db_s` | Nome di accesso al database |
| `table_s` | Nome della tabella accessibile |
| `sql_text_s` | Testo query completo |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di Azure](howto-configure-audit-logs-portal.md)