---
title: Configurare e leggere i log quando si usa il trigger di Funzioni di Azure per Cosmos DB
description: Informazioni su come esporre i log nella pipeline di registrazione di Funzioni di Azure usando il trigger di Funzioni di Azure per Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: ad53762ad490c5e8feedb83007b8721fe4883806
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334526"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Come configurare e leggere i log quando si usa il trigger di Funzioni di Azure per Cosmos DB

Questo articolo descrive come configurare l'ambiente di Funzioni di Azure per inviare i log del trigger di Funzioni di Azure per Cosmos DB alla [soluzione di monitoraggio](../azure-functions/functions-monitoring.md) configurata.

## <a name="included-logs"></a>Log inclusi

Il trigger di Funzioni di Azure per Cosmos DB usa la [libreria del processore dei feed delle modifiche](./change-feed-processor.md) internamente. La libreria genera un set di log di integrità che può essere usato per monitorare le operazioni interne a scopo di [risoluzione dei problemi](./troubleshoot-changefeed-functions.md).

I log di integrità descrivono il comportamento del trigger di Funzioni di Azure per Cosmos DB quando si prova a eseguire operazioni durante l'inizializzazione o gli scenari di bilanciamento del carico.

## <a name="enabling-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione usando il trigger di Funzioni di Azure per Cosmos DB, individuare il file `host.json` nel progetto di Funzioni di Azure o nell'app Funzioni di Azure e [configurare il livello di registrazione necessario](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). È necessario abilitare le tracce per `Host.Triggers.CosmosDB`, come illustrato nell'esempio seguente:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Dopo la distribuzione di Funzioni di Azure con la configurazione aggiornata, verranno visualizzati i log del trigger di Funzioni di Azure per Cosmos DB come parte delle tracce. È possibile visualizzare i log nel provider di registrazione configurato in *Category* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Eseguire query sui log

Eseguire la query seguente sui log generati dal trigger di Funzioni di Azure per Cosmos DB in [Analisi di Azure Application Insights](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio](../azure-functions/functions-monitoring.md) nelle applicazioni di Funzioni di Azure.
* Informazioni su come [diagnosticare e risolvere problemi comuni](./troubleshoot-changefeed-functions.md) durante l'uso del trigger di Funzioni di Azure per Cosmos DB.