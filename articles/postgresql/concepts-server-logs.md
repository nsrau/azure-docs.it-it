---
title: Registri server nel Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive i Database di Azure per PostgreSQL - singolo Server genera query e i log degli errori e la configurazione di conservazione dei log.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4d1cf2c59e324cedd9b747b1ac65d6edcb9deb45
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067403"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Registri server nel Database di Azure per PostgreSQL - Server singolo
Il database di Azure per PostgreSQL genera log di query e registri errori. I log di query e degli errori possono essere usati per individuare e risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. L'accesso ai log delle transazioni non è incluso. 

## <a name="configure-logging"></a>Configurare la registrazione 
È possibile configurare la registrazione nel server usando i parametri del server di registrazione. In ogni nuovo server **log_checkpoints** e **log_connections** sono attivati per impostazione predefinita. Esistono anche parametri aggiuntivi che è possibile modificare in base alle esigenze di registrazione: 

![Database di Azure per PostgreSQL - Parametri di registrazione](./media/concepts-server-logs/log-parameters.png)

Per altre informazioni su questi parametri, vedere la documentazione [Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Creazione di report e registrazione di errori). Per informazioni su come configurare i parametri di Database di Azure per PostgreSQL, vedere la [documentazione sul portale](howto-configure-server-parameters-using-portal.md) o la [documentazione sull'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Accedere ai log del server usando il portale o l'interfaccia della riga di comando
Se i log sono stati abilitati, è possibile accedervi dallo spazio di archiviazione log di Database di Azure per PostgreSQL usando il [portale di Azure](howto-configure-server-logs-in-portal.md), l'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md) e le API REST di Azure. I file di log ruotano ogni ora o 100 MB di spazio, a seconda della condizione che si verifica per prima. È possibile impostare il periodo di conservazione di questo spazio di archiviazione log usando il parametro  **log\_retention\_period**  associato al server. Il valore predefinito è 3 giorni e il valore massimo è 7 giorni. Il server deve disporre di risorse di archiviazione allocate sufficienti per contenere i file di log. Questo parametro di conservazione non gestisce i log di diagnostica di Azure.


## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per PostgreSQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo aver abilitato i log nel server PostgreSQL, è possibile scegliere in modo che vengano inviati a [monitoraggio di Azure registra](../azure-monitor/log-query/log-query-overview.md), hub eventi o archiviazione di Azure. Per altre informazioni sull'abilitazione dei log di diagnostica, vedere la sezione sulle procedure della [documentazione sui log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo in utilizzo generico e ottimizzate per la memoria [sui piani tariffari](concepts-pricing-tiers.md).

La tabella seguente descrive il contenuto di ogni log. A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti. 

|**Campo** | **Descrizione** |
|---|---|
| TenantId | ID del tenant. |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Timestamp in cui il log è stato registrato in formato UTC. |
| Type | Tipo di log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID per la sottoscrizione a cui appartiene il server. |
| ResourceGroup | Nome del gruppo di risorse a cui appartiene il server. |
| ResourceProvider | Nome del provider di risorse. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI della risorsa |
| Risorsa | Nome del server |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Livello di registrazione, esempio: LOG, ERROR, NOTICE |
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
