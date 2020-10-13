---
title: Registrazione di controllo-database di Azure per PostgreSQL-server flessibile
description: Concetti per la registrazione di controllo di pgAudit nel database di Azure per PostgreSQL-server flessibile.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2fccf5431666990919faf7e6378b46c41d682437
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937087"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Registrazione di controllo nel database di Azure per PostgreSQL-server flessibile

Registrazione di controllo delle attività del database nel database di Azure per PostgreSQL: il server flessibile è disponibile tramite l'estensione di controllo PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornisce la registrazione dettagliata della sessione e/o del controllo degli oggetti.

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è in anteprima

Se si vuole che i log a livello di risorsa di Azure per operazioni come il ridimensionamento di calcolo e archiviazione, vedere [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo
Per impostazione predefinita, le istruzioni di log di pgAudit vengono emesse insieme alle istruzioni di log regolari usando la funzionalità di registrazione standard di Postgres. Nel database di Azure per PostgreSQL-server flessibile è possibile configurare tutti i log da inviare all'archivio dei log di monitoraggio di Azure per l'analisi successiva in Log Analytics. Se si Abilita la registrazione delle risorse di monitoraggio di Azure, i log verranno inviati automaticamente (in formato JSON) ad archiviazione di Azure, Hub eventi e/o log di monitoraggio di Azure, a seconda della scelta effettuata.

Per informazioni su come configurare la registrazione in archiviazione di Azure, Hub eventi o log di monitoraggio di Azure, vedere la sezione log delle risorse dell' [articolo log del server](concepts-logging.md).

## <a name="enabling-pgaudit"></a>Abilitazione di pgAudit

Per abilitare pgAudit, è necessario connettersi al server usando un client, ad esempio PSQL, e abilitare l'estensione pgAudit eseguendo il comando seguente:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>impostazioni pgAudit

pgAudit consente di configurare la registrazione di controllo di una sessione o di un oggetto. La [registrazione di controllo della sessione](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emette log dettagliati delle istruzioni eseguite. La [registrazione di controllo degli oggetti](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) è un ambito di controllo per relazioni specifiche. È possibile scegliere di configurare uno o entrambi i tipi di registrazione. 

> [!NOTE]
> le impostazioni pgAudit vengono specificate a livello globale e non possono essere specificate a livello di database o di ruolo.

Dopo aver [abilitato pgAudit](#enabling-pgaudit), è possibile configurarne i parametri per avviare la registrazione. La [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornisce la definizione di ogni parametro. Testare prima i parametri e confermare che si sta ottenendo il comportamento previsto.

> [!NOTE]
> `pgaudit.log_client`Se si imposta su on, i log vengono reindirizzati a un processo client, ad esempio PSQL, anziché essere scritti nel file. Questa impostazione viene in genere disabilitata. <br> <br>
> `pgaudit.log_level` viene abilitato solo quando `pgaudit.log_client` è on.

> [!NOTE]
> In database di Azure per PostgreSQL: `pgaudit.log` non è possibile impostare un server flessibile usando un `-` collegamento di segno (meno) come descritto nella documentazione di pgAudit. Tutte le classi di istruzioni obbligatorie (lettura, scrittura e così via) devono essere specificate singolarmente.

## <a name="audit-log-format"></a>Formato del log di controllo
Ogni voce di controllo è indicata da `AUDIT:` vicino all'inizio della riga del log. Il formato del resto della voce è descritto in dettaglio nella [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Guida introduttiva
Per iniziare rapidamente, impostare `pgaudit.log` su `WRITE` e aprire i log del server per esaminare l'output. 

## <a name="viewing-audit-logs"></a>Visualizzazione dei log di controllo
Il modo in cui si accede ai log dipende dall'endpoint scelto. Per archiviazione di Azure, vedere l'articolo relativo all' [account di archiviazione dei log](../../azure-monitor/platform/resource-logs-collect-storage.md) . Per gli hub eventi, vedere l'articolo [flusso di log di Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) .

Per i log di monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics** , in modo che possano essere sottoposti a query dalla tabella AzureDiagnostics. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e avvisi, vedere Panoramica delle [query dei log di monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) .

È possibile utilizzare questa query per iniziare. È possibile configurare gli avvisi in base alle query.

Cerca tutte le voci pgAudit nei log Postgres per un determinato server nell'ultimo giorno
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla registrazione nel database di Azure per PostgreSQL-server flessibile](concepts-logging.md)
- [Informazioni su come configurare la registrazione nel database di Azure per PostgreSQL-server flessibile e come accedere ai log](howto-configure-and-access-logs.md)