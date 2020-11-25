---
title: Creare un'app per le funzioni con Azure Cosmos DB - Interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando - Creare una funzione di Azure che si connette a un Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 760017439ddc65ee929db1612b34b093d8bed8a0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565399"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Creare una funzione di Azure che si connette a un database Azure Cosmos DB | Documentazione Microsoft

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni e connette la funzione a un database di Azure Cosmos DB. L'impostazione dell'app creata che contiene la connessione può essere usata con un [trigger o un binding di Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

In questo esempio si crea un'app per le funzioni di Azure e si aggiunge un endpoint Cosmos DB e la chiave di accesso alle impostazioni dell'app.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Creare un gruppo di risorse con una posizione |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Creare un account di archiviazione |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [Piano a consumo](../functions-scale.md#consumption-plan) serverless. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crea un database di Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Ottiene la connessione dell'account del database. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Ottiene le chiavi per il database. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Imposta la stringa di connessione come impostazione dell'app nell'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).




