---
title: Log del server per Database di Azure per MariaDB
description: Descrive i log disponibili nel Database di Azure per MariaDB e i parametri disponibili per l'abilitazione di diversi livelli di registrazione.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 10dbd4d7fa838ee7f8a3f70b3caadb570877d685
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259976"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Log di query lente nel database di Azure per MariaDB
Nel Database di Azure per MariaDB, il log delle query lente è disponibile per gli utenti. L'accesso al log delle transazioni non è supportato. Il log delle query lente può essere usato per identificare eventuali colli di bottiglia delle prestazioni e procedere alla risoluzione dei problemi.

Per altre informazioni sul log delle query lente, vedere la documentazione MariaDB per il [log delle query lente](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Accedere ai log di query lente
È possibile elencare e scaricare i log di query lente del database di Azure per MariaDB usando il portale di Azure e l'interfaccia della riga di comando di Azure.

Nel portale di Azure, selezionare il server Database di Azure per MariaDB. Nell'intestazione **Monitoraggio** selezionare la pagina **Log del server**.

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Conservazione dei log
I log sono disponibili per un massimo di sette giorni dalla data di creazione. Se le dimensioni totali dei log disponibili superano 7 GB, i file meno recenti vengono eliminati fino a quando non è disponibile dello spazio.

I log vengono ruotati ogni 24 ore o 7 GB, a seconda del valore raggiunto per primo.

## <a name="configure-slow-query-logging"></a>Configurare la registrazione lenta delle query
Per impostazione predefinita il log delle query lente è disabilitato. Per abilitarlo, impostare slow_query_log su ON.

Altri parametri che è possibile modificare includono:

- **long_query_time**: se una query richiede più tempo del valore di long_query_time (in secondi), la query viene registrata. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: se è ON include le istruzioni a livello amministrativo come ALTER_TABLE e ANALYZE_TABLE nelle istruzioni scritte in slow_query_log.
- **log_queries_not_using_indexes**: determina se le query che non usano gli indici vengono registrate in slow_query_log
- **log_throttle_queries_not_using_indexes**: Questo parametro limita il numero di query non di indice che possono essere scritte nel log di query lente. Questo parametro ha effetto quando log_queries_not_using_indexes è impostato su ON.

Vedere [documentazione riguardante il log delle query lente](https://mariadb.com/kb/en/library/slow-query-log-overview/) di MariaDB per una descrizione completa dei parametri del log delle query lente.

## <a name="diagnostic-logs"></a>Log di diagnostica
Il database di Azure per MariaDB è integrato con i log di diagnostica di monitoraggio di Azure. Dopo aver abilitato i log di query lente sul server MariaDB, è possibile scegliere di crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo nei [piani tariffari](concepts-pricing-tiers.md)per utilizzo generico e con ottimizzazione per la memoria.

La tabella seguente descrive il contenuto di ogni log. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated`UTC | Timestamp in cui il log è stato registrato in formato UTC. |
| `Type` | Tipo di log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID per la sottoscrizione a cui appartiene il server. |
| `ResourceGroup` | Nome del gruppo di risorse a cui appartiene il server. |
| `ResourceProvider` | Nome del provider di risorse. Sempre `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI della risorsa |
| `Resource` | Nome del server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome del server |
| `start_time_t`UTC | Ora di inizio della query |
| `query_time_s` | Tempo totale di esecuzione della query |
| `lock_time_s` | Tempo totale in cui la query è rimasta bloccata |
| `user_host_s` | Nome utente |
| `rows_sent_s` | Numero di righe inviate |
| `rows_examined_s` | Numero di righe esaminate |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Inserisci ID |
| `sql_text_s` | Query completa |
| `server_id_s` | ID server |
| `thread_id_s` | ID thread |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Passaggi successivi
- [Come configurare e accedere ai log del server usando il portale di Azure](howto-configure-server-logs-portal.md).
