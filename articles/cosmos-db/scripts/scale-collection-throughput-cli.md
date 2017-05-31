---
title: "Script dell&quot;interfaccia della riga di comando di Azure - Scalare la velocità effettiva del contenitore di Azure Cosmos DB | Documentazione Microsoft"
description: "Esempio di script dell&quot;interfaccia della riga di comando Azure CLI - Scalare la velocità effettiva del contenitore di Azure Cosmos DB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 04/20/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cfa23a21a10f53a042b3a5cd85265b0ed1130d07
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="scale-azure-cosmos-db-container-throughput-using-the-azure-cli"></a>Scalare la velocità effettiva del contenitore di Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure

Questo esempio scala la velocità effettiva del contenitore per qualsiasi tipo di contenitore di Azure Cosmos DB.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-throughput/scale-cosmosdb-throughput.sh?highlight=40-46 "Scalare la velocità effettiva di Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Aggiorna un account Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).

