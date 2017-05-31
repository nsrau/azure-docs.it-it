---
title: 'Script dell&quot;interfaccia della riga di comando di Azure: creare un firewall per Azure Cosmos DB | Microsoft Docs'
description: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: creazione di un firewall per Azure Cosmos DB'
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
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 710413f8bf0cd50abc5df9e117103fec5fc43e7c
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="azure-cosmos-db-create-a-firewall-using-the-azure-cli"></a>Azure Cosmos DB: creare un firewall con l'interfaccia della riga di comando di Azure

Questo esempio di script dell'interfaccia della riga di comando consente di creare un criterio firewall per qualsiasi tipo di account Azure Cosmos DB. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[principale](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Creare un firewall Azure Cosmos DB")]

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
| [az cosmosdb create](/cli/azure/cosmosdb/name#create) | Crea un account Azure CosmosDB. |
| [az cosmosdb update](/cli/azure/sql/server#create) | Aggiorna un account Azure CosmosDB per includere le impostazioni del firewall. |
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).

