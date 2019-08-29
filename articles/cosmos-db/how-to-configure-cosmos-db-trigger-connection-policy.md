---
title: Trigger di Funzioni di Azure per i criteri di connessione di Cosmos DB
description: Informazioni su come configurare i criteri di connessione usati dal trigger di Funzioni di Azure per Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 6e4c50e5dcc35450463d02bbed040754ea778e70
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093610"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Come configurare i criteri di connessione usati dal trigger di Funzioni di Azure per Cosmos DB

Questo articolo descrive come configurare i criteri di connessione quando si usa il trigger di Funzioni di Azure per Cosmos DB per connettersi all'account Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Perché sono importanti i criteri di connessione?

Esistono due modalità di connessione: Diretta e Gateway. Per altre informazioni su queste modalità di connessione, vedere l'articolo di [suggerimenti per le prestazioni](./performance-tips.md#networking). Per impostazione predefinita, per stabilire tutte le connessioni nel trigger di Funzioni di Azure per Cosmos DB si usa la modalità **Gateway**. Tuttavia, per gli scenari basati sulle prestazioni questa potrebbe non essere l'opzione ottimale.

## <a name="changing-the-connection-mode-and-protocol"></a>Modifica della modalità e del protocollo di connessione

Sono disponibili due impostazioni di configurazione chiave per la configurazione dei criteri di connessione client, ovvero la **modalità di connessione** e il **protocollo di connessione**. È possibile cambiare la modalità e il protocollo di connessione predefiniti usati dal trigger di Funzioni di Azure per Cosmos DB e da tutti i [binding di Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output). Per cambiare le impostazioni predefinite, è necessario individuare il file `host.json` nel progetto di Funzioni di Azure o nell'app per le funzioni di Azure e aggiungere l'[impostazione aggiuntiva](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) seguente:

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
* [Suggerimenti per le prestazioni di Azure Cosmos DB](./performance-tips.md)
* [Esempi di codice](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
