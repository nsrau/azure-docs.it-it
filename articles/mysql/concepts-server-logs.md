---
title: Log di query lente-database di Azure per MySQL
description: Descrive i log di query lente disponibili nel database di Azure per MySQL e i parametri disponibili per l'abilitazione di diversi livelli di registrazione.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 6bd99a200a8f9e6be6d155a334b9b06ac05eacc3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972184"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Log di query lente nel database di Azure per MySQL
Nel Database di Azure per MySQL, il log delle query lente è disponibile per gli utenti. L'accesso al log delle transazioni non è supportato. Il log delle query lente può essere usato per identificare eventuali colli di bottiglia delle prestazioni e procedere alla risoluzione dei problemi.

Per altre informazioni sul log delle query lente MySQL, vedere la [sezione relativa ai log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) del manuale di riferimento per MySQL.

## <a name="access-slow-query-logs"></a>Accedere ai log di query lente
È possibile elencare e scaricare i log di query lente di database di Azure per MySQL usando il portale di Azure e l'interfaccia della riga di comando di Azure.

Nel portale di Azure selezionare il server del Database di Azure per MySQL. Nell'intestazione **Monitoraggio** selezionare la pagina **Log del server**.

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere [configurare e accedere ai log di query lente usando l'interfaccia](howto-configure-server-logs-in-cli.md)della riga

Analogamente, è possibile inviare tramite pipe i log a monitoraggio di Azure usando i log di diagnostica. Per ulteriori informazioni, vedere di [seguito](concepts-server-logs.md#diagnostic-logs) .

## <a name="log-retention"></a>Conservazione dei log
I log sono disponibili per un massimo di sette giorni dalla data di creazione. Se le dimensioni totali dei log disponibili superano 7 GB, i file meno recenti vengono eliminati fino a quando non è disponibile dello spazio. 

I log vengono ruotati ogni 24 ore o 7 GB, a seconda del valore raggiunto per primo.

## <a name="configure-slow-query-logging"></a>Configurare la registrazione lenta delle query 
Per impostazione predefinita il log delle query lente è disabilitato. Per abilitarlo, impostare slow_query_log su ON.

Altri parametri che è possibile modificare includono:

- **long_query_time**: se una query richiede più tempo del valore di long_query_time (in secondi), la query viene registrata. Il valore predefinito è 10 secondi.
- **log_slow_admin_statements**: se è ON include le istruzioni a livello amministrativo come ALTER_TABLE e ANALYZE_TABLE nelle istruzioni scritte in slow_query_log.
- **log_queries_not_using_indexes**: determina se le query che non usano gli indici vengono registrate in slow_query_log
- **log_throttle_queries_not_using_indexes**: questo parametro limita il numero di query non di indice che possono essere scritte nel log di query lente. Questo parametro ha effetto quando log_queries_not_using_indexes è impostato su ON.
- **log_output**: se "file", consente la scrittura del log di query lente sia nella risorsa di archiviazione del server locale che nei log di diagnostica di monitoraggio di Azure. Se "None", il log di query lente verrà scritto solo nell'archivio locale del server. 

> [!Note]
> Per `sql_text`, il log verrà troncato se supera i 2048 caratteri.

Per una descrizione completa dei parametri per il log di query lente, vedere la [documentazione sul log di query lente](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) per MySQL.

## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per MySQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo aver abilitato i log di query lente nel server MySQL, è possibile scegliere di crearli in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo nei [piani tariffari](concepts-pricing-tiers.md)per utilizzo generico e con ottimizzazione per la memoria.

La tabella seguente descrive il contenuto di ogni log. A seconda del metodo di output, è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti.

| **Proprietà** | **Descrizione** |
|---|---|
| `TenantId` | ID del tenant. |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
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
| `start_time_t` [UTC] | Ora di inizio della query |
| `query_time_s` | Tempo totale in secondi impiegato per l'esecuzione della query |
| `lock_time_s` | Tempo totale in secondi per cui la query è stata bloccata |
| `user_host_s` | Username |
| `rows_sent_s` | Numero di righe inviate |
| `rows_examined_s` | Numero di righe esaminate |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserisci ID |
| `sql_text_s` | Query completa |
| `server_id_s` | ID del server |
| `thread_id_s` | ID del thread |
| `\_ResourceId` | URI della risorsa |

## <a name="next-steps"></a>Fasi successive
- [Come configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-in-cli.md).
