---
title: Gestire le risorse di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure per gestire l'account, il database e i contenitori di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d45f5b5c5945796e30c86b2e3ef48d6b8e693b99
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038977"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Gestire le risorse di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure

La guida seguente illustra i comandi per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure. Include anche i comandi per ridimensionare la velocità effettiva dei contenitori. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Altri esempi sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md), incluse le procedure per creare e gestire gli account, i database e i contenitori di Cosmos DB per MongoDB, Gremlin, Cassandra e API Tabella.

Questo script di esempio dell'interfaccia della riga di comando di Azure crea un account API SQL, un database e un contenitore di Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Per creare un account Azure Cosmos DB con SQL API, la coerenza di sessione, il supporto multimaster abilitato in Stati Uniti orientali e Stati Uniti occidentali, aprire l'interfaccia della riga di comando di Azure o Cloud Shell ed eseguire il comando seguente:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Creare un database

Per creare un database Cosmos DB, aprire l'interfaccia della riga di comando di Azure o Cloud Shell ed eseguire il comando seguente:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore Cosmos DB con UR/s di 1000 e una chiave di partizione, aprire l'interfaccia della riga di comando di Azure o Cloud Shell ed eseguire il comando seguente:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Cambiare la velocità effettiva di un contenitore

Per cambiare la velocità effettiva di un contenitore Azure Cosmos DB impostando il valore UR/s di 400, aprire l'interfaccia della riga di comando di Azure o Cloud Shell ed eseguire il comando seguente:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Elencare le chiavi dell'account

Quando si crea un account Azure Cosmos DB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account di Azure Cosmos DB. Generando due chiavi di accesso, Azure Cosmos DB consente di rigenerare le chiavi senza interruzioni dell'account Azure Cosmos DB. Sono disponibili anche chiavi di sola lettura per l'autenticazione delle operazioni di sola lettura. Esistono due chiavi di lettura/scrittura (primaria e secondaria) e due chiavi di sola lettura (primaria e secondaria). È possibile ottenere le chiavi per l'account eseguendo il comando seguente:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Elencare le stringhe di connessione

È possibile recuperare la stringa di connessione per connettere l'applicazione all'account Cosmos DB usando il comando seguente.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Rigenerare una chiave dell'account

È consigliabile modificare periodicamente le chiavi di accesso all'account Azure Cosmos DB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account Azure Cosmos DB con una delle due chiavi mentre si rigenera l'altra.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere:

- [Installare l'interfaccia da riga di comando di Azure](/cli/azure/install-azure-cli)
- [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md)
