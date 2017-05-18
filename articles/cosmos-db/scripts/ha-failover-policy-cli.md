---
title: "Script dell&quot;interfaccia della riga di comando di Azure - Creare un criterio di failover per la disponibilità elevata | Documentazione Microsoft"
description: "Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un criterio di failover per la disponibilità elevata"
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
ms.openlocfilehash: 24ce695bdc48e1fc196415dcee8c3dab269e1e91
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>Creare un criterio di failover per la disponibilità elevata con l'interfaccia della riga di comando di Azure

Questo esempio di script dell'interfaccia della riga di comando crea un account Azure Cosmos DB e lo configura per la disponibilità elevata.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "Creare un criterio di failover in Azure Cosmos DB")]

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
| [az cosmosdb create](/cli/azure/sql/server#create) | Crea un account Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#update) | Aggiorna un account Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).

