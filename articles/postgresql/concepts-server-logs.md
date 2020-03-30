---
title: Log - Database di Azure per PostgreSQL - Server singoloLogs - Azure Database for PostgreSQL - Single Server
description: Descrive la configurazione, l'archiviazione e l'analisi di registrazione nel database di Azure per PostgreSQL - Server singolo
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280495"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Log nel database di Azure per PostgreSQL - Server singoloLogs in Azure Database for PostgreSQL - Single Server
Il database di Azure per PostgreSQL consente di configurare e accedere ai log standard di Postgres. I registri possono essere utilizzati per identificare, risolvere i problemi e correggere gli errori di configurazione e le prestazioni non ottimali. Le informazioni di registrazione che è possibile configurare e accedere includono errori, informazioni sulle query, record autovacuum, connessioni e checkpoint. (L'accesso ai registri delle transazioni non è disponibile).

La registrazione di controllo viene resa disponibile tramite un'estensione Postgres, pgaudit. Per altre informazioni, vedere l'articolo sui concetti di [controllo.](concepts-audit.md)


## <a name="configure-logging"></a>Configurare la registrazione 
È possibile configurare la registrazione standard postgres sul server utilizzando i parametri del server di registrazione. In ogni database di Azure `log_checkpoints` per `log_connections` il server PostgreSQL e sono attivati per impostazione predefinita. Esistono anche parametri aggiuntivi che è possibile modificare in base alle esigenze di registrazione: 

![Database di Azure per PostgreSQL - Parametri di registrazione](./media/concepts-server-logs/log-parameters.png)

Per ulteriori informazioni sui parametri del registro Postgres, visitare le sezioni [When To Log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e What To [Log](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) della documentazione postgres. La maggior parte, ma non tutti, i parametri di registrazione Postgres sono disponibili per la configurazione in Database di Azure per PostgreSQL.

Per informazioni su come configurare i parametri nel database di Azure per PostgreSQL, vedere la [documentazione del portale](howto-configure-server-parameters-using-portal.md) o la [documentazione dell'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> La configurazione di un volume elevato di registri, ad esempio la registrazione delle istruzioni, può aggiungere un sovraccarico significativo delle prestazioni. 

## <a name="access-log-files"></a>Accedere ai file con estensione log
Il formato di log predefinito nel database di Azure per PostgreSQL è .log.The default log format in Azure Database for PostgreSQL is .log. Una riga di esempio da questo registro è simile alla seguente:A sample line from this log looks like:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Il database di Azure per PostgreSQL offre un percorso di archiviazione a breve termine per i file con estensione log. Un nuovo file inizia ogni 1 ora o 100 MB, a seconda di quale viene prima. I registri vengono aggiunti al file corrente quando vengono generati da Postgres.  

È possibile impostare il periodo di conservazione `log_retention_period` per questo archivio di log a breve termine utilizzando il parametro . Il valore predefinito è 3 giorni e il valore massimo è 7 giorni. Il percorso di archiviazione a breve termine può contenere fino a 1 GB di file di registro. Dopo 1 GB, i file meno recenti, indipendentemente dal periodo di conservazione, verranno eliminati per fare spazio ai nuovi log. 

Per la conservazione a lungo termine dei registri e l'analisi dei log, è possibile scaricare i file con estensione log e spostarli in un servizio di terze parti. È possibile scaricare i file usando il [portale di Azure,](howto-configure-server-logs-in-portal.md) [l'interfaccia della riga di comando](howto-configure-server-logs-using-cli.md)di Azure. In alternativa, è possibile configurare le impostazioni di diagnostica di Monitoraggio di Azure che genera automaticamente i log (in formato JSON) in posizioni a lungo termine. Ulteriori informazioni su questa opzione sono riportate nella sezione seguente. 

È possibile interrompere la generazione di `logging_collector` file con estensione log impostando il parametro su OFF. La disattivazione della generazione di file con estensione log è consigliata se si usano le impostazioni di diagnostica di Monitoraggio di Azure.Off .log file generation is recommended if you are using Azure Monitor diagnostic settings. Questa configurazione ridurrà l'impatto sulle prestazioni della registrazione aggiuntiva.

## <a name="diagnostic-logs"></a>Log di diagnostica
Il database di Azure per PostgreSQL è integrato con le impostazioni di diagnostica di Monitoraggio di Azure.Azure Database for PostgreSQL is integrated with Azure Monitor diagnostic settings. Le impostazioni di diagnostica consentono di inviare i log Postgres in formato JSON ai log di monitoraggio di Azure per l'analisi e gli avvisi, gli hub eventi per lo streaming e Archiviazione di Azure per l'archiviazione. 

> [!IMPORTANT]
> Questa funzionalità di diagnostica per i log del server è disponibile solo nei [livelli dei prezzi](concepts-pricing-tiers.md)Scopo generale e Ottimizzazione della memoria.


### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica
È possibile abilitare le impostazioni di diagnostica per il server Postgres usando il portale di Azure, l'interfaccia della riga di comando, l'API REST e Powershell.You can enable diagnostic settings for your Postgres server using the Azure portal, CLI, REST API, and Powershell. La categoria di log da selezionare è **PostgreSQLLogs**. Esistono altri log che è possibile configurare se si utilizza [Query Store.](concepts-query-store.md)

Per abilitare i log di diagnostica tramite il portale di Azure:To enable Diagnostic logs using the Azure portal:

   1. Nel portale passare a *Impostazioni di diagnostica* nel menu di navigazione del server Postgres.
   2. Selezionare *Aggiungi impostazione diagnostica*.
   3. Assegnare un nome a questa impostazione. 
   4. Selezionare l'endpoint preferito (account di archiviazione, hub eventi, analisi dei log). 
   5. Selezionare il tipo di registro **PostgreSQLLogs**.
   7. Salvare l’impostazione.

Per abilitare i log di diagnostica tramite Powershell, l'interfaccia della riga di comando o l'API REST, vedere l'articolo sulle impostazioni di [diagnostica.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

La modalità di accesso ai log dipende dall'endpoint scelto. Per Archiviazione di Azure, vedere l'articolo [account di archiviazione dei log.](../azure-monitor/platform/resource-logs-collect-storage.md) Per gli hub eventi, vedere l'articolo Log di Azure del [flusso.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Per i log di Monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics,** pertanto possono essere sottoposti a query dalla tabella AzureDiagnostics.The Postgres logs use the AzureDiagnostics collection mode, so they can be queryed from the AzureDiagnostics table. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e sugli avvisi, vedere Panoramica della [query Log di Monitoraggio di Azure.Learn](../azure-monitor/log-query/log-query-overview.md) more about querying and alerting in the Azure Monitor Logs query overview.

Di seguito sono riportate le query che è possibile provare a iniziare. È possibile configurare gli avvisi in base alle query.

Cercare tutti i registri Postgres per un determinato server nell'ultimo giorno
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Cercare tutti i tentativi di connessione non localhostSearch for all non-localhost connection attempts
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
La query precedente mostrerà i risultati nelle ultime 6 ore per qualsiasi registrazione del server Postgres in questa area di lavoro.

### <a name="log-format"></a>Formato del registro

Nella tabella seguente vengono descritti i campi per il tipo **PostgreSQLLogs.** A seconda dell'endpoint di output scelto è possibile che i campi inclusi e il relativo ordine di visualizzazione siano differenti. 

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
| Dettagli | Messaggio di log secondario (se applicabile) |
| ColumnName | Nome della colonna (se applicabile) |
| SchemaName | Nome dello schema (se applicabile) |
| DatatypeName | Nome del tipo di dati (se applicabile) |
| LogicalServerName | Nome del server | 
| _ResourceId | URI della risorsa |
| Prefisso | Prefisso della riga di registro |


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'accesso ai log dal [portale di Azure](howto-configure-server-logs-in-portal.md) oppure dall'[interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md).
- Altre informazioni sui [prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
- Ulteriori informazioni sui log di [controllo](concepts-audit.md)
