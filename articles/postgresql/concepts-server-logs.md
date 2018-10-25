---
title: Registri server nel database di Azure per PostgreSQL
description: Questo articolo descrive come Database di Azure per PostgreSQL genera log degli errori e delle query e come viene configurata la conservazione dei log.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2a6744bdec48e59b820605bb4d1cc01d32702bcf
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867766"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Log di server in Database di Azure per PostgreSQL 
Il database di Azure per PostgreSQL genera log di query e registri errori. I log di query e degli errori possono essere usati per individuare e risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. L'accesso ai log delle transazioni non è incluso. 

## <a name="configure-logging"></a>Configurare la registrazione 
È possibile configurare la registrazione nel server usando i parametri del server di registrazione. In ogni nuovo server **log_checkpoints** e **log_connections** sono attivati per impostazione predefinita. Esistono anche parametri aggiuntivi che è possibile modificare in base alle esigenze di registrazione: 

![Database di Azure per PostgreSQL - Parametri di registrazione](./media/concepts-server-logs/log-parameters.png)

Per altre informazioni su questi parametri, vedere la documentazione [Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Creazione di report e registrazione di errori). Per informazioni su come configurare i parametri di Database di Azure per PostgreSQL, vedere la [documentazione sul portale](howto-configure-server-parameters-using-portal.md) o la [documentazione sull'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Accedere ai log del server usando il portale o l'interfaccia della riga di comando
Se i log sono stati abilitati, è possibile accedervi dallo spazio di archiviazione log di Database di Azure per PostgreSQL usando il [portale di Azure](howto-configure-server-logs-in-portal.md), l'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md) e le API REST di Azure. I file di log ruotano ogni ora o 100 MB di spazio, a seconda della condizione che si verifica per prima. È possibile impostare il periodo di conservazione di questo spazio di archiviazione log usando il parametro **log\_retention\_period** associato al server. Il valore predefinito è 3 giorni e il valore massimo è 7 giorni. Il server deve disporre di risorse di archiviazione allocate sufficienti per contenere i file di log. Questo parametro di conservazione non gestisce i log di diagnostica di Azure.


## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per PostgreSQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo avere abilitato i log nel server PostgreSQL, è possibile scegliere se inviarli a [Log Analytics](../log-analytics/log-analytics-queries.md), Hub eventi o Archiviazione di Azure. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). 


La tabella seguente descrive il contenuto di ogni log. A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti. 

|**Campo** | **Descrizione** |
|---|---|
| TenantId | ID del tenant. |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| type | Tipo di log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID per la sottoscrizione a cui appartiene il server. |
| ResourceGroup | Nome del gruppo di risorse a cui appartiene il server. |
| ResourceProvider | Nome del provider di risorse. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI della risorsa |
| Risorsa | Nome del server |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Livello di registrazione, ad esempio: LOG, ERROR, NOTICE |
| Message | Messaggio di log primario | 
| Domain | Versione del server, ad esempio: postgres-10 |
| Dettagli | Messaggio di log secondario (se applicabile) |
| ColumnName | Nome della colonna (se applicabile) |
| SchemaName | Nome dello schema (se applicabile) |
| DatatypeName | Nome del tipo di dati (se applicabile) |
| LogicalServerName | Nome del server | 
| _ResourceId | URI della risorsa |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'accesso ai log dal [portale di Azure](howto-configure-server-logs-in-portal.md) oppure dall'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md).
- Altre informazioni sui [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
