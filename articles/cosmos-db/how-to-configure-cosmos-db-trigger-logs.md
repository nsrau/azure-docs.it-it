---
title: Log del trigger di Azure Cosmos DB
description: Informazioni su come esporre i log del trigger di Azure Cosmos DB nella pipeline di registrazione di Funzioni di Azure
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: bf5216dc3b296c98176387c6e2cfff7c31daedab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241033"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Come configurare e leggere i log del trigger di Azure Cosmos DB

Questo articolo descrive come configurare l'ambiente di Funzioni di Azure per inviare i log del trigger di Azure Cosmos DB alla [soluzione di monitoraggio](../azure-functions/functions-monitoring.md) configurata.

## <a name="included-logs"></a>Log inclusi

Il trigger di Azure Cosmos DB usa la [libreria del processore dei feed delle modifiche](./change-feed-processor.md) internamente. La libreria genera un set di log di integrità che può essere usato per monitorare le operazioni interne a scopo di [risoluzione dei problemi](./troubleshoot-changefeed-functions.md).

I log di integrità descrivono il comportamento del trigger di Azure Cosmos DB quando si tenta di eseguire operazioni durante l'inizializzazione o gli scenari di bilanciamento del carico.

## <a name="enabling-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione del trigger di Azure Cosmos DB, individuare il file `host.json` nel progetto di Funzioni di Azure o nell'app Funzioni di Azure e [configurare il livello di registrazione necessario](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). È necessario abilitare le tracce per `Host.Triggers.CosmosDB`, come illustrato nell'esempio seguente:

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

Dopo la distribuzione di Funzioni di Azure con la configurazione aggiornata, sarà possibile visualizzare i log del trigger di Azure Cosmos DB come parte delle tracce. È possibile visualizzare i log nel provider di registrazione configurato in *Category* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Eseguire query sui log

Avviare la query seguente per eseguire query sui log generati dal trigger di Azure Cosmos DB in [Analisi di Azure Application Insights](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio](../azure-functions/functions-monitoring.md) nelle applicazioni di Funzioni di Azure.
* Informazioni su come [diagnosticare e risolvere problemi comuni](./troubleshoot-changefeed-functions.md) durante l'uso del trigger di Azure Cosmos DB in Funzioni di Azure.