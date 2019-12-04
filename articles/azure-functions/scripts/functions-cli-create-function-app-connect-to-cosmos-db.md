---
title: Creare un'app per le funzioni con Azure Cosmos DB - Interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando - Creare una funzione di Azure che si connette a un Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: d881a5eb6132f95239418f032da51d002af01905
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532855"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Creare una funzione di Azure che si connette a un database Azure Cosmos DB | Documentazione Microsoft

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni e connette la funzione a un database di Azure Cosmos DB. L'impostazione dell'app creata che contiene la connessione può essere usata con un [trigger o un binding di Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si usa l'interfaccia della riga di comando in locale, assicurarsi di eseguire l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

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
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [piano a consumo](../functions-scale.md#consumption-plan) senza server. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crea un database di Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Ottiene la connessione dell'account del database. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Ottiene le chiavi per il database. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Imposta la stringa di connessione come impostazione dell'app nell'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).




