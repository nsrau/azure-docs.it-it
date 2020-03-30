---
title: Registrazione di controllo - Database di Azure per PostgreSQL - Server singoloAudit logging - Azure Database for PostgreSQL - Single Server
description: Concetti per la registrazione di controllo pgAudit nel database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842470"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registrazione di controllo nel database di Azure per PostgreSQL - Server singoloAudit logging in Azure Database for PostgreSQL - Single Server

La registrazione di controllo delle attività di database nel database di Azure per PostgreSQL - Server singolo è disponibile tramite l'estensione di controllo PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornisce una registrazione dettagliata del controllo di sessioni e/o oggetti.

> [!NOTE]
> pgAudit è in anteprima nel database di Azure per PostgreSQL.
> L'estensione può essere abilitata solo sui server General Purpose e Memory Optimized.

Se si vogliono log a livello di risorse di Azure per operazioni come il calcolo e la scalabilità dell'archiviazione, vedere Log attività di Azure.If you want Azure resource-level logs for operations like compute and storage scaling, see [the Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo
Per impostazione predefinita, le istruzioni di log di pgAudit vengono emesse insieme alle istruzioni di log regolari usando la funzionalità di registrazione standard di Postgres. In Database di Azure per PostgreSQL è possibile scaricare questi file con estensione log tramite il portale di Azure o l'interfaccia della riga di comando. Lo spazio di archiviazione massimo per la raccolta di file è 1 GB e ogni file è disponibile per un massimo di sette giorni (il valore predefinito è tre giorni). Questo servizio è un'opzione di archiviazione a breve termine.

In alternativa, è possibile configurare tutti i log da generare nel servizio di log di diagnostica di Monitoraggio di Azure.Alternatively, you can configure all logs to be mitted to azure Monitor's diagnostic log service. Se si abilita la registrazione diagnostica di Monitoraggio di Azure, i log verranno inviati automaticamente (in formato JSON) ai log di Archiviazione di Azure, Hub eventi e/o Monitoraggio di Azure, a seconda della scelta effettuata.

L'abilitazione di pgAudit genera un volume elevato di log in un server, cosa che influisc sulle prestazioni e sull'archiviazione dei log. Si consiglia di usare il servizio log di diagnostica di Azure, che offre opzioni di archiviazione a lungo termine, nonché le funzionalità di analisi e avvisi. Si consiglia di disattivare la registrazione standard per ridurre l'effetto sulle prestazioni delle registrazioni aggiuntive:

   1. Impostare `logging_collector` il parametro su OFF. 
   2. Riavviare il server per applicare questa modifica.

Per informazioni su come configurare la registrazione nei log di Archiviazione di Azure, Hub eventi o Monitoraggio di Azure, vedere la sezione dei log di diagnostica dell'articolo dei log del [server.](concepts-server-logs.md)

## <a name="installing-pgaudit"></a>Installazione di pgAudit

Per installare pgAudit, è necessario includerlo nelle librerie di precaricamento condivise del server. Una modifica al parametro `shared_preload_libraries` di Postgres richiede un riavvio del server per rendere effettive. È possibile modificare i parametri usando il portale di [Azure,](howto-configure-server-parameters-using-portal.md) [l'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md)di Azure o l'API [REST.](/rest/api/postgresql/configurations/createorupdate)

Uso del portale di [Azure:](https://portal.azure.com)

   1. Selezionare il server di Database di Azure per PostgreSQL.
   2. Nella barra laterale, seleziona **Parametri server**.
   3. Cercare il `shared_preload_libraries` parametro.
   4. Selezionare **pgaudit**.
   5. Riavviare il server per applicare la modifica.

   6. Connettersi al server utilizzando un client (ad esempio psql) e abilitare l'estensione pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se viene visualizzato un errore, verificare di `shared_preload_libraries`aver riavviato il server dopo il salvataggio.

## <a name="pgaudit-settings"></a>Impostazioni pgAudit

pgAudit consente di configurare la registrazione di controllo della sessione o dell'oggetto. [La registrazione di controllo della sessione](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) genera registri dettagliati delle istruzioni eseguite. [La registrazione di controllo](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) degli oggetti ha come ambito relazioni specifiche. È possibile scegliere di impostare uno o entrambi i tipi di registrazione. 

> [!NOTE]
> Le impostazioni di pgAudit vengono specificate in modo gloabale e non possono essere specificate a livello di database o di ruolo.

Una volta [installato pgAudit](#installing-pgaudit), è possibile configurarne i parametri per avviare la registrazione. La [documentazione pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornisce la definizione di ogni parametro. Verificare prima i parametri e verificare che si stia ottenendo il comportamento previsto.

> [!NOTE]
> L'impostazione `pgaudit.log_client` su ON reindirizzerà i log a un processo client (ad esempio psql) anziché essere scritti in un file. Questa impostazione viene in genere disabilitata. <br> <br>
> `pgaudit.log_level`è abilitato `pgaudit.log_client` solo quando è attivato.

> [!NOTE]
> In Database di Azure `pgaudit.log` per PostgreSQL `-` non può essere impostato usando un collegamento con segno (meno) come descritto nella documentazione di pgAudit.In Azure Database for PostgreSQL, cannot be set using a (minus) sign shortcut as described in the pgAudit documentation. Tutte le classi di istruzioni obbligatorie (READ, WRITE e così via) devono essere specificate singolarmente.

### <a name="audit-log-format"></a>Formato del registro di controllo
Ogni voce di controllo `AUDIT:` è indicata da quasi l'inizio della riga di registro. Il formato del resto della voce è descritto in dettaglio nella documentazione di [pgAudit.](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)

Se sono necessari altri campi per soddisfare i requisiti `log_line_prefix`di controllo, utilizzare il parametro Postgres . `log_line_prefix`è una stringa che viene restituita all'inizio di ogni riga di log Postgres. Ad esempio, `log_line_prefix` l'impostazione seguente fornisce timestamp, nome utente, nome del database e ID processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Per ulteriori `log_line_prefix`informazioni su , consultare la [documentazione PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introduzione
Per iniziare rapidamente, `pgaudit.log` `WRITE`impostare su e aprire i log per esaminare l'output. 

## <a name="viewing-audit-logs"></a>Visualizzazione dei registri di controllo
Se si utilizzano file con estensione log, i log di controllo verranno inclusi nello stesso file dei log degli errori PostgreSQL. È possibile scaricare i file di log dal [portale di](howto-configure-server-logs-in-portal.md) Azure o dall'interfaccia della riga [di comando.](howto-configure-server-logs-using-cli.md) 

Se si usa la registrazione diagnostica di Azure, il modo in cui si accede ai log dipende dall'endpoint scelto. Per Archiviazione di Azure, vedere l'articolo [account di archiviazione dei log.](../azure-monitor/platform/resource-logs-collect-storage.md) Per gli hub eventi, vedere l'articolo Log di Azure del [flusso.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Per i log di Monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics,** pertanto possono essere sottoposti a query dalla tabella AzureDiagnostics.The Postgres logs use the AzureDiagnostics collection mode, so they can be queryed from the AzureDiagnostics table. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e sugli avvisi, vedere Panoramica della [query Log di Monitoraggio di Azure.Learn](../azure-monitor/log-query/log-query-overview.md) more about querying and alerting in the Azure Monitor Logs query overview.

È possibile utilizzare questa query per iniziare. È possibile configurare gli avvisi in base alle query.

Cercare tutti i registri Postgres per un determinato server nell'ultimo giorno
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla registrazione nel database di Azure per PostgreSQLLearn about logging in Azure Database for PostgreSQL](concepts-server-logs.md)
- Informazioni su come impostare i parametri usando il portale di [Azure,](howto-configure-server-parameters-using-portal.md) [l'interfaccia della riga di comando](howto-configure-server-parameters-using-cli.md)di Azure o [l'API REST.](/rest/api/postgresql/configurations/createorupdate)
