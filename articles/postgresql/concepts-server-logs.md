---
title: Log del server in database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive in che modo database di Azure per PostgreSQL-server singolo genera log di query e errori e come viene configurata la conservazione dei log.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091129"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Log del server in database di Azure per PostgreSQL-server singolo
Il database di Azure per PostgreSQL genera log di query e registri errori. I log di query e degli errori possono essere usati per individuare e risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. L'accesso ai log delle transazioni non è incluso. 

## <a name="configure-logging"></a>Configurare la registrazione 
È possibile configurare la registrazione nel server usando i parametri del server di registrazione. In ogni nuovo server **log_checkpoints** e **log_connections** sono attivati per impostazione predefinita. Esistono anche parametri aggiuntivi che è possibile modificare in base alle esigenze di registrazione: 

![Database di Azure per PostgreSQL - Parametri di registrazione](./media/concepts-server-logs/log-parameters.png)

Per altre informazioni su questi parametri, vedere la documentazione [Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Creazione di report e registrazione di errori). Per informazioni su come configurare i parametri di Database di Azure per PostgreSQL, vedere la [documentazione sul portale](howto-configure-server-parameters-using-portal.md) o la [documentazione sull'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Accedere ai log del server usando il portale o l'interfaccia della riga di comando
Se i log sono stati abilitati, è possibile accedervi dallo spazio di archiviazione log di Database di Azure per PostgreSQL usando il [portale di Azure](howto-configure-server-logs-in-portal.md), l'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md) e le API REST di Azure. I file di log ruotano ogni ora o 100 MB di spazio, a seconda della condizione che si verifica per prima. È possibile impostare il periodo di conservazione di questo spazio di archiviazione log usando il parametro  **log\_retention\_period**  associato al server. Il valore predefinito è 3 giorni e il valore massimo è 7 giorni. Il server deve disporre di risorse di archiviazione allocate sufficienti per contenere i file di log. Questo parametro di conservazione non gestisce i log di diagnostica di Azure.


## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per PostgreSQL è integrato con i log di diagnostica di Monitoraggio di Azure. Dopo aver abilitato i log nel server PostgreSQL, è possibile scegliere di crearli in [log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md), Hub eventi o archiviazione di Azure. 

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo nei [piani tariffari](concepts-pricing-tiers.md)per utilizzo generico e con ottimizzazione per la memoria.

Per abilitare i log di diagnostica usando il portale di Azure:

   1. Nel portale passare a impostazioni di *diagnostica* nel menu di navigazione del server postgres.
   2. Selezionare *Aggiungi impostazioni di diagnostica*.
   3. Assegnare un nome a questa impostazione. 
   4. Selezionare la località a Valle preferita (account di archiviazione, Hub eventi, log Analytics). 
   5. Selezionare i tipi di dati desiderati.
   6. Salvare l’impostazione.

Nella tabella seguente viene descritto il significato di ogni log. A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti. 

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
| Prefisso | Prefisso della riga di log |



## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'accesso ai log dal [portale di Azure](howto-configure-server-logs-in-portal.md) oppure dall'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md).
- Altre informazioni sui [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
