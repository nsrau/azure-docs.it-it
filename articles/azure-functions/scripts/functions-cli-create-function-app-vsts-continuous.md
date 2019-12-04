---
title: Creare un'app per le funzioni con la distribuzione da DevOps - Interfaccia della riga di comando di Azure
description: Creare un'app per le funzioni e distribuire il codice di funzione da Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 3fa11d5cd81d93b89b6e8ae63fd491842be78633
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532795"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Creare una funzione in Azure distribuita da Azure DevOps

Questo argomento illustra come usare Funzioni di Azure per creare un'app per le funzioni [senza server](https://azure.microsoft.com/solutions/serverless/) che usa il [piano a consumo](../functions-scale.md#consumption-plan). L'app per le funzioni, che rappresenta un contenitore per le funzioni, viene distribuita in modo continuo da un repository di Azure DevOps. 

Per completare questo argomento sono necessari:

* Un repository di Azure DevOps che contiene il progetto dell'app per le funzioni e per cui si dispone delle autorizzazioni amministrative.
* Un [token di accesso personale](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) per accedere al repository di Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario installare e usare la versione 2.0 o una versione successiva. Per determinare la versione dell'interfaccia della riga di comando di Azure, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un account di archiviazione, un'app per le funzioni e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea l'account di archiviazione necessario per l'app per le funzioni. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [piano a consumo](../functions-scale.md#consumption-plan) senza server. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa un'app per le funzioni con un archivio Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
