---
title: Log-database di Azure per PostgreSQL-server flessibile
description: Descrive la configurazione di registrazione, l'archiviazione e l'analisi nel database di Azure per PostgreSQL-server flessibile
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940395"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Log nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL consente di configurare e accedere ai log standard di postgres. I log possono essere usati per identificare, risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. Le informazioni di registrazione che è possibile configurare e l'accesso includono errori, informazioni sulle query, record autovacuum, connessioni e Checkpoint. (L'accesso ai log delle transazioni non è disponibile).

La registrazione di controllo viene resa disponibile tramite un'estensione Postgres, `pgaudit` . Per altre informazioni, vedere l'articolo relativo ai [concetti relativi al controllo](concepts-audit.md) .

## <a name="configure-logging"></a>Configurare la registrazione

È possibile configurare la registrazione standard Postgres nel server usando i parametri del server di registrazione. Per altre informazioni sui parametri del log Postgres, vedere le sezioni [When to log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e [What to log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) della documentazione di postgres. La maggior parte, ma non tutti, i parametri di registrazione Postgres sono disponibili per la configurazione nel database di Azure per PostgreSQL.

Per informazioni su come configurare i parametri in database di Azure per PostgreSQL, vedere la documentazione del [portale](howto-configure-server-parameters-using-portal.md) o la [documentazione dell'interfaccia](howto-configure-server-parameters-using-cli.md)della riga di comando.

> [!NOTE]
> La configurazione di un volume elevato di log, ad esempio la registrazione delle istruzioni, può aggiungere un sovraccarico significativo delle prestazioni. 

## <a name="accessing-logs"></a>Accesso ai log

Database di Azure per PostgreSQL è integrato con le impostazioni di diagnostica di monitoraggio di Azure. Le impostazioni di diagnostica consentono di inviare i log Postgres in formato JSON ai log di monitoraggio di Azure per l'analisi e gli avvisi, gli hub eventi per lo streaming e l'archiviazione di Azure per l'archiviazione. 

### <a name="log-format"></a>Formato di log

Nella tabella seguente vengono descritti i campi del tipo **PostgreSQLLogs** . A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti. 

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
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Livello di registrazione, ad esempio: LOG, ERROR, NOTICE |
| Message | Messaggio di log primario | 
| Dominio | Versione del server, ad esempio: postgres-10 |
| Dettaglio | Messaggio di log secondario (se applicabile) |
| ColumnName | Nome della colonna (se applicabile) |
| SchemaName | Nome dello schema (se applicabile) |
| DatatypeName | Nome del tipo di dati (se applicabile) |
| LogicalServerName | Nome del server | 
| _ResourceId | URI della risorsa |
| Prefisso | Prefisso della riga di log |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [configurare e accedere ai log](howto-configure-and-access-logs.md).
- Scopri di più sui [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
- Altre informazioni sui [log di controllo](concepts-audit.md)
