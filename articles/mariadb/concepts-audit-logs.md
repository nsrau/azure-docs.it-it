---
title: Log di controllo per Database di Azure per MariaDB
description: Descrive i log di controllo disponibili nel Database di Azure per MariaDB e i parametri disponibili per l'abilitazione di livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 765db8461465b74ac068782c1b91d3c68b73f7d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079522"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Log di controllo nel Database di Azure per MariaDB

Nel Database di Azure per MariaDB, il log di controllo è disponibile per gli utenti. Il log di controllo può essere utilizzato per tenere traccia delle attività a livello di database e viene usato comunemente per la conformità.

> [!IMPORTANT]
> Funzionalità di log di controllo è attualmente in anteprima.

## <a name="configure-audit-logging"></a>Configurare la registrazione di controllo

Per impostazione predefinita il log di controllo è disabilitato. Per abilitarla, impostare `audit_log_enabled` su ON.

Altri parametri che è possibile modificare includono:

- `audit_log_events`: controlla il registrazione di eventi. Vedere di seguito nella tabella per gli eventi di controllo specifico.
- `audit_log_exclude_users`: MariaDB gli utenti da escludere dalla registrazione. Consente al massimo quattro utenti. Lunghezza massima consentita del parametro è 256 caratteri.

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

## <a name="access-audit-logs"></a>Accedere ai log di controllo

I log di controllo sono integrati con i log di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di controllo nel server di MariaDB, è possibile generarli da log di monitoraggio di Azure, hub eventi o archiviazione di Azure. Per altre informazioni su come abilitare i log di diagnostica nel portale di Azure, vedere la [audit log portale articolo](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Schemi

Le sezioni seguenti descrivono ciò che è l'output del log di controllo di MariaDB in base al tipo di evento. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

### <a name="connection"></a>Connessione

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT` |
| `connection_id` | ID connessione univoco generato da MariaDB |
| `host` | Vuoto |
| `ip` | Indirizzo IP del client che si connette a MariaDB |
| `user` | Nome dell'utente che esegue la query |
| `db` | Nome del database connesso a |
| `\_ResourceId` | URI della risorsa |

### <a name="general"></a>Generale

Schema seguente si applica ai tipi di evento generale, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e amministratore.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Timestamp quando è stato registrato tshe log in formato UTC |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Query avviare secondi in timestamp UNIX dell'ora |
| `error_code` | Codice di errore se query non è riuscita. `0` indica che nessun errore |
| `thread_id` | ID del thread che ha eseguito la query |
| `host` | Vuoto |
| `ip` | Indirizzo IP del client che si connette a MariaDB |
| `user` | Nome dell'utente che esegue la query |
| `sql_text` | Testo query completo |
| `\_ResourceId` | URI della risorsa |

### <a name="table-access"></a>Accesso alle tabelle

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, o `DELETE` |
| `connection_id` | ID connessione univoco generato da MariaDB |
| `db` | Nome di accesso al database |
| `table` | Nome della tabella accessibile |
| `sql_text` | Testo query completo |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i log di controllo nel portale di Azure](howto-configure-audit-logs-portal.md)