---
title: Script dell&quot;interfaccia della riga di comando di Azure - Ottenere la stringa di connessione di Azure Cosmos DB per app MongoDB | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Ottenere la stringa di connessione di Azure Cosmos DB per app MongoDB
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27c790db456c3e650a624b46df77d4cdce202fa7
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>Ottenere una stringa di connessione di Azure Cosmos DB per app MongoDB mediante l'interfaccia della riga di comando di Azure

Questo esempio ottiene una stringa di connessione di Azure Cosmos DB per app MongoDB mediante l'interfaccia della riga di comando di Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "Ottenere la stringa di connessione di Azure Cosmos DB per app MongoDB")]

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
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb/list-connection-strings) | Ottiene la stringa di connessione per l'account.|
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).

