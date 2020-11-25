---
title: Creare una app per le funzioni in un piano di servizio app - Interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'app per le funzioni in un piano di servizio app
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 67efb5cc146b10b7e8194b7de4f6e0f0d6c08de9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565450"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Creare un'app per le funzioni in un piano di servizio app

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni che è un contenitore per le funzioni. L'app per le funzioni creata usa un piano di servizio app dedicato, quindi le risorse del server sono sempre attive.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

Questo script crea un'app per le funzioni di Azure usando un [piano di servizio app](../functions-scale.md#app-service-plan) dedicato.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea un account di Archiviazione di Azure. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Crea un piano Premium. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel piano di servizio app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
