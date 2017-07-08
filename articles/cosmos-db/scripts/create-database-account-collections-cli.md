---
title: Creare un account API di Azure Cosmos DB DocumentDB, un database e una raccolta con lo script dell'interfaccia della riga di comando di Azure | Microsoft Docs
description: Creare un account API di Azure Cosmos DB DocumentDB, un database e una raccolta con un esempio di script dell'interfaccia della riga di comando di Azure
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/06/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 28f99d56404e47adcd375d9f3106cc234469cbfd
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="azure-cosmos-db-create-an-documentdb-api-account-using-cli"></a>Azure Cosmos DB: creare un account API di DocumentDB con l'interfaccia della riga di comando

Questo script dell'interfaccia della riga di comando di Azure di esempio permette di creare un account API di Azure Cosmos DB DocumentDB, un database e una raccolta.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[principale](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh?highlight=15-35 "Creare un account API di Azure Cosmos DB DocumentDB, un database e una raccolta")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az cosmosdb create](/cli/azure/cosmosdb#create) | Crea un account Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Cosmos DB](../cli-samples.md).

