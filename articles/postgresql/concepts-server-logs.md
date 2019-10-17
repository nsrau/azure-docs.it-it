---
title: Log nel database di Azure per PostgreSQL-server singolo
description: Concetti relativi alla configurazione della registrazione, all'archiviazione e all'analisi nel database di Azure per PostgreSQL-server singolo
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: cc796733c9b0b1effd8043c49540f9b489610067
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331290"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Log nel database di Azure per PostgreSQL-server singolo
Database di Azure per PostgreSQL consente di configurare e accedere ai log standard di postgres. I log possono essere usati per identificare, risolvere i problemi e correggere errori di configurazione e prestazioni non ottimali. Le informazioni di registrazione che è possibile configurare e l'accesso includono errori, informazioni sulle query, record autovacuum, connessioni e Checkpoint. (L'accesso ai log delle transazioni non è disponibile).

La registrazione di controllo viene resa disponibile tramite un'estensione Postgres, pgaudit. Per altre informazioni, vedere l'articolo relativo ai [concetti relativi al controllo](concepts-audit.md) .


## <a name="configure-logging"></a>Configurare la registrazione 
È possibile configurare la registrazione standard Postgres nel server usando i parametri del server di registrazione. In ogni database di Azure per il server PostgreSQL, `log_checkpoints` e `log_connections` sono attivati per impostazione predefinita. Esistono anche parametri aggiuntivi che è possibile modificare in base alle esigenze di registrazione: 

![Database di Azure per PostgreSQL - Parametri di registrazione](./media/concepts-server-logs/log-parameters.png)

Per altre informazioni sui parametri del log Postgres, vedere le sezioni [When to log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e [What to log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) della documentazione di postgres. La maggior parte, ma non tutti, i parametri di registrazione Postgres sono disponibili per la configurazione nel database di Azure per PostgreSQL.

Per informazioni su come configurare i parametri in database di Azure per PostgreSQL, vedere la documentazione del [portale](howto-configure-server-parameters-using-portal.md) o la [documentazione dell'interfaccia](howto-configure-server-parameters-using-cli.md)della riga di comando. 

> [!NOTE]
> La configurazione di un volume elevato di log, ad esempio la registrazione delle istruzioni, può aggiungere un sovraccarico significativo delle prestazioni. 

## <a name="access-log-files"></a>File Access. log
Il formato di log predefinito nel database di Azure per PostgreSQL è. log. Una riga di esempio da questo log ha un aspetto simile al seguente:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Database di Azure per PostgreSQL fornisce un percorso di archiviazione a breve termine per i file con estensione log. Un nuovo file inizia ogni 1 ora o 100 MB, A seconda del valore che viene raggiunto per primo. I log vengono aggiunti al file corrente perché vengono emessi da postgres.  

È possibile impostare il periodo di conservazione per questo archivio di log a breve termine utilizzando il parametro `log_retention_period`. Il valore predefinito è 3 giorni e il valore massimo è 7 giorni. Il percorso di archiviazione a breve termine può ospitare fino a 1 GB di file di log. Dopo 1 GB, i file meno recenti, indipendentemente dal periodo di memorizzazione, verranno eliminati per fare spazio ai nuovi log. 

Per la conservazione a lungo termine dei log e dell'analisi dei log, è possibile scaricare i file con estensione log e spostarli in un servizio di terze parti. È possibile scaricare i file usando l'interfaccia della riga di comando di [Azure](howto-configure-server-logs-using-cli.md) [portale di Azure](howto-configure-server-logs-in-portal.md). In alternativa, è possibile configurare le impostazioni di diagnostica di monitoraggio di Azure che emettono automaticamente i log (in formato JSON) a percorsi a lungo termine. Per altre informazioni su questa opzione, vedere la sezione seguente. 

È possibile interrompere la generazione dei file con estensione log impostando il parametro `logging_collector` su OFF. Disattivazione. la generazione di file di log è consigliata se si usano le impostazioni di diagnostica di monitoraggio di Azure. Questa configurazione ridurrà l'effetto sulle prestazioni della registrazione aggiuntiva.

## <a name="diagnostic-logs"></a>Log di diagnostica
Database di Azure per PostgreSQL è integrato con le impostazioni di diagnostica di monitoraggio di Azure. Le impostazioni di diagnostica consentono di inviare i log Postgres in formato JSON ai log di monitoraggio di Azure per l'analisi e gli avvisi, gli hub eventi per lo streaming e l'archiviazione di Azure per l'archiviazione. 

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo nei [piani tariffari](concepts-pricing-tiers.md)per utilizzo generico e con ottimizzazione per la memoria.


### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica
È possibile abilitare le impostazioni di diagnostica per il server Postgres usando il portale di Azure, l'interfaccia della riga di comando, l'API REST e PowerShell. La categoria di log da selezionare è **PostgreSQLLogs**. Se si usa [query Store](concepts-query-store.md), è possibile configurare altri log.

Per abilitare i log di diagnostica usando il portale di Azure:

   1. Nel portale passare a impostazioni di *diagnostica* nel menu di navigazione del server postgres.
   2. Selezionare *Aggiungi impostazioni di diagnostica*.
   3. Assegnare un nome a questa impostazione. 
   4. Selezionare l'endpoint preferito (account di archiviazione, Hub eventi, log Analytics). 
   5. Selezionare il tipo di log **PostgreSQLLogs**.
   7. Salvare l’impostazione.

Per abilitare i log di diagnostica tramite PowerShell, l'interfaccia della riga di comando o l'API REST, vedere l'articolo [impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md) .

### <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

Il modo in cui si accede ai log dipende dall'endpoint scelto. Per archiviazione di Azure, lo schema è descritto nell'articolo [logs storage account](../azure-monitor/platform/resource-logs-collect-storage.md) . Per gli hub eventi, vedere l'articolo [flusso di log di Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .

Per i log di monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics** , in modo che possano essere sottoposti a query dalla tabella AzureDiagnostics. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e avvisi, vedere Panoramica delle [query dei log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) .

Di seguito sono riportate le query che è possibile provare a iniziare. È possibile configurare gli avvisi in base alle query.

Cerca tutti i log Postgres per un determinato server nell'ultimo giorno
```
AzureDiagnostics
| where LogicalServerName_s == 'myservername'
| where TimeGenerated > ago(1d) 
```

Cerca tutti gli errori per tutti i server Postgres in questa area di lavoro nelle ultime 6 ore
```
AzureDiagnostics
| where errorLevel_s == "error" and category == "PostgreSQLogs"
| where TimeGenerated > ago(6h)
```

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
| Gruppi | Nome del server |
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
| Prefisso | Prefisso della riga di log |


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'accesso ai log dal [portale di Azure](howto-configure-server-logs-in-portal.md) oppure dall'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md).
- Altre informazioni sui [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
- Altre informazioni sui [log di controllo](concepts-audit.md)
