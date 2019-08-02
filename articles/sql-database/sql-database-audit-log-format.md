---
title: Formato del log di controllo del database SQL | Microsoft Docs
description: Informazioni sul modo in cui sono strutturati i log di controllo del database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 334d277370bb8d6678679c887f6a2b89d65652c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569480"
---
# <a name="sql-database-audit-log-format"></a>Formato del log di controllo del database SQL

Il [controllo del database SQL di Azure](sql-database-auditing.md) tiene traccia degli eventi di database e li scrive in un log di controllo nell'account di archiviazione di Azure o li invia a hub eventi o log Analytics per l'elaborazione e l'analisi downstream.

## <a name="naming-conventions"></a>Convenzioni di denominazione

### <a name="blob-audit"></a>Controllo BLOB

I log di controllo archiviati nell'archivio BLOB vengono archiviati in un `sqldbauditlogs` contenitore denominato nell'account di archiviazione di Azure. La gerarchia di directory all'interno del contenitore è nel `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`formato. Il formato del nome file `<CreationTime>_<FileNumberInSession>.xel`del BLOB è, dove `hh_mm_ss_ms` è nel formato `FileNumberInSession` UTC, ed è un indice in esecuzione nel caso in cui `CreationTime` i log della sessione si estendano su più file BLOB.

Per `Database1` `Server1` il database riportato di seguito, ad esempio, è possibile un percorso valido:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Hub eventi

Gli eventi di controllo vengono scritti nello spazio dei nomi e nell'hub eventi che è stato definito durante la configurazione di controllo e vengono acquisiti nel corpo degli eventi [Apache avro](https://avro.apache.org/) e archiviati usando la formattazione JSON con la codifica UTF-8. Per leggere i log di controllo, è possibile usare [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) o strumenti simili in grado di elaborare tale formato.

### <a name="log-analytics"></a>Log Analytics

Gli eventi di controllo vengono scritti nell'area di lavoro log Analytics definita durante la configurazione `AzureDiagnostics` di controllo, nella `SQLSecurityAuditEvents`tabella con la categoria. Per altre informazioni utili sul linguaggio di ricerca e i comandi di Log Analytics, vedere [Guida di riferimento alla ricerca in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Campi del log di controllo

| Nome (BLOB) | Nome (hub eventi/Log Analytics) | Descrizione | Tipo BLOB | Tipo di hub eventi/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID dell'azione | varchar (4) | string |
| action_name | action_name_s | Nome dell'azione | N/D | string |
| additional_information | additional_information_s | Qualsiasi informazione aggiuntiva sull'evento, archiviata come XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Numero di righe interessate dalla query | bigint | int |
| application_name | application_name_s| Nome dell'applicazione client | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo di entità controllabile in cui si verifica il controllo | varchar (2) | string |
| class_type_desc | class_type_description_s | Descrizione dell'entità controllabile in cui si verifica il controllo | N/D | string |
| client_ip | client_ip_s | IP di origine dell'applicazione client | nvarchar(128) | string |
| connection_id | N/D | ID della connessione nel server | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipi di informazioni e etichette di riservatezza restituite dalla query controllata, basate sulle colonne classificate nel database. Altre informazioni sull' [individuazione e la classificazione dei dati del database SQL di Azure](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | Contesto del database in cui si è verificata l'azione | sysname | string |
| database_principal_id | database_principal_id_d | ID del contesto utente del database in cui viene eseguita l'azione | int | int |
| database_principal_name | database_principal_name_s | Nome del contesto utente del database in cui viene eseguita l'azione | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Durata esecuzione query in millisecondi | bigint | int |
| event_time | event_time_t | Data e ora in cui viene attivata l'azione controllabile | datetime2 | Datetime |
| HOST_NAME | N/D | Nome host client | string | N/D |
| is_column_permission | is_column_permission_s | Flag che indica se si tratta di un'autorizzazione a livello di colonna. 1 = true, 0 = false | bit | string |
| N/D | is_server_level_audit_s | Flag che indica se il controllo è a livello di server | N/D | string |
| ID object_ | object_id_d | ID dell'entità in cui si è verificato il controllo. Sono inclusi gli oggetti server, i database, gli oggetti di database e gli oggetti dello schema. 0 se l'entità è il server stesso o se il controllo non viene eseguito a livello di oggetto | int | int |
| object_name | object_name_s | Nome dell'entità in cui si è verificato il controllo. Sono inclusi gli oggetti server, i database, gli oggetti di database e gli oggetti dello schema. 0 se l'entità è il server stesso o se il controllo non viene eseguito a livello di oggetto | sysname | string |
| permission_bitmask | permission_bitmask_s | Se applicabile, Visualizza le autorizzazioni concesse, negate o revocate | varbinary(16) | string |
| response_rows | response_rows_d | Numero di righe restituite nel set di risultati | bigint | int |
| schema_name | schema_name_s | Contesto dello schema in cui si è verificata l'azione. NULL per i controlli che si verificano all'esterno di uno schema | sysname | string |
| N/D | securable_class_type_s | Oggetto a protezione diretta mappato al class_type sottoposto a controllo | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificatore univoco | varbinary | GUID |
| sequence_number | sequence_number_d | Tiene traccia della sequenza di record all'interno di un singolo record di controllo troppo grande per il buffer di scrittura per i controlli | int | int |
| server_instance_name | server_instance_name_s | Nome dell'istanza del server in cui si è verificato il controllo | sysname | string |
| server_principal_id | server_principal_id_d | ID del contesto di accesso in cui viene eseguita l'azione | int | int |
| server_principal_name | server_principal_name_s | Account di accesso corrente | sysname | string |
| server_principal_sid | server_principal_sid_s | SID di accesso corrente | varbinary | string |
| session_id | session_id_d | ID della sessione in cui si è verificato l'evento | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Entità server per la sessione | sysname | string |
| istruzione | statement_s | Istruzione T-SQL eseguita (se presente) | nvarchar(4000) | string |
| fatto | succeeded_s | Indica se l'azione che ha attivato l'evento è riuscita. Per gli eventi diversi da login e batch, questo indica solo se il controllo delle autorizzazioni ha avuto esito positivo o negativo, non l'operazione. 1 = esito positivo, 0 = esito negativo | bit | string |
| target_database_principal_id | target_database_principal_id_d | Entità di database su cui viene eseguita l'operazione GRANT/DENY/REVOKE. 0 se non applicabile | int | int |
| target_database_principal_name | target_database_principal_name_s | Utente di destinazione dell'azione. NULL se non applicabile | string | string |
| target_server_principal_id | target_server_principal_id_d | Entità server in cui viene eseguita l'operazione GRANT/DENY/REVOKE. Restituisce 0 se non applicabile | int | int |
| target_server_principal_name | target_server_principal_name_s | Account di accesso di destinazione dell'azione. NULL se non applicabile | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID dell'account di accesso di destinazione. NULL se non applicabile | varbinary | string |
| transaction_id | transaction_id_d | Solo SQL Server (a partire da 2016)-0 per il database SQL di Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | ID evento definito dall'utente passato come argomento a sp_audit_write. NULL per gli eventi di sistema (impostazione predefinita) e diverso da zero per l'evento definito dall'utente. Per ulteriori informazioni, vedere [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informazioni definite dall'utente passate come argomento a sp_audit_write. NULL per gli eventi di sistema (impostazione predefinita) e diverso da zero per l'evento definito dall'utente. Per ulteriori informazioni, vedere [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Fasi successive

Altre informazioni sul [controllo del database SQL di Azure](sql-database-auditing.md).