---
title: Trigger di funzioni di Azure per la configurazione avanzata di Cosmos DB
description: Informazioni su come configurare i criteri di connessione e di registrazione usati dal trigger di funzioni di Azure per Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 152454b3da0ff1efd33ac463aecec78155a8e6f7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490476"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Come configurare la registrazione e la connettività con il trigger di funzioni di Azure per Cosmos DB

Questo articolo descrive le opzioni di configurazione avanzate che è possibile impostare quando si usa il trigger di funzioni di Azure per Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Abilitazione di log specifici del trigger

Il trigger di Funzioni di Azure per Cosmos DB usa la [libreria del processore dei feed delle modifiche](./change-feed-processor.md) internamente. La libreria genera un set di log di integrità che può essere usato per monitorare le operazioni interne a scopo di [risoluzione dei problemi](./troubleshoot-changefeed-functions.md).

I log di integrità descrivono il comportamento del trigger di Funzioni di Azure per Cosmos DB quando si prova a eseguire operazioni durante l'inizializzazione o gli scenari di bilanciamento del carico.

### <a name="enabling-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione usando il trigger di Funzioni di Azure per Cosmos DB, individuare il file `host.json` nel progetto di Funzioni di Azure o nell'app Funzioni di Azure e [configurare il livello di registrazione necessario](../azure-functions/functions-monitoring.md#log-levels-and-categories). Abilitare le tracce per  `Host.Triggers.CosmosDB` come illustrato nell'esempio seguente:

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

### <a name="query-the-logs"></a>Eseguire query sui log

Eseguire la query seguente sui log generati dal trigger di Funzioni di Azure per Cosmos DB in [Analisi di Azure Application Insights](../azure-monitor/log-query/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Configurazione dei criteri di connessione

Esistono due modalità di connessione: Diretta e Gateway. Per ulteriori informazioni su queste modalità di connessione, vedere l'articolo relativo alle [modalità](sql-sdk-connection-modes.md) di connessione. Per impostazione predefinita, per stabilire tutte le connessioni nel trigger di Funzioni di Azure per Cosmos DB si usa la modalità **Gateway**. Tuttavia, per gli scenari basati sulle prestazioni questa potrebbe non essere l'opzione ottimale.

### <a name="changing-the-connection-mode-and-protocol"></a>Modifica della modalità e del protocollo di connessione

Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client, ovvero la **modalità di connessione** e il **protocollo di connessione**. È possibile cambiare la modalità e il protocollo di connessione predefiniti usati dal trigger di Funzioni di Azure per Cosmos DB e da tutti i [binding di Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md). Per cambiare le impostazioni predefinite, è necessario individuare il file `host.json` nel progetto di Funzioni di Azure o nell'app per le funzioni di Azure e aggiungere l'[impostazione aggiuntiva](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) seguente:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Dove `connectionMode` deve includere la modalità di connessione desiderata (Diretta o Gateway) e `protocol` il protocollo di connessione desiderato (TCP o HTTPS). 

Se il progetto di Funzioni di Azure viene eseguito con il runtime V1 di Funzioni di Azure, il nome della configurazione è leggermente diverso; è necessario usare `documentDB` invece di `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Se si usa l'hosting con piano a consumo di Funzioni di Azure, ogni istanza prevede un limite per la quantità di connessioni socket che può mantenere. Se si usa la modalità Diretta/TCP, da progettazione vengono create più connessioni ed è possibile che venga raggiunto il [limite del piano a consumo](../azure-functions/manage-connections.md#connection-limit), nel qual caso è possibile usare la modalità Gateway oppure eseguire Funzioni di Azure in [modalità Servizio app](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Passaggi successivi

* [Limiti di connessione in Funzioni di Azure](../azure-functions/manage-connections.md#connection-limit)
* [Abilitare il monitoraggio](../azure-functions/functions-monitoring.md) nelle applicazioni di Funzioni di Azure.
* Informazioni su come [diagnosticare e risolvere problemi comuni](./troubleshoot-changefeed-functions.md) durante l'uso del trigger di Funzioni di Azure per Cosmos DB.