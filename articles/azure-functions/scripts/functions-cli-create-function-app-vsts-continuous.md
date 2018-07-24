---
title: Creare una funzione in Azure distribuita da Visual Studio Team Services | Microsoft Docs
description: Creare un'app per le funzioni e distribuire codice di funzione da Visual Studio Team Services
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988953"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Creare un'app per le funzioni e distribuire codice di funzione da Visual Studio Team Services

Questo argomento illustra come usare Funzioni di Azure per creare un'app per le funzioni [senza server](https://azure.microsoft.com/overview/serverless-computing/) che usa il [piano a consumo](../functions-scale.md#consumption-plan). L'app per le funzioni, che è un contenitore per le funzioni, viene distribuita in modo continuo da un repository di Visual Studio Team Services (VSTS). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Per completare questo argomento sono necessari:

* Un repository di Visual Studio Team Services che contiene il progetto dell'app per le funzioni e per cui si dispone di autorizzazioni amministrative.
* Un [token di accesso personale](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) per accedere al repository di Visual Studio Team Services.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario installare e usare la versione 2.0 o una versione successiva. Per determinare la versione dell'interfaccia della riga di comando di Azure, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un account di archiviazione, un'app per le funzioni e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Crea l'account di archiviazione necessario per l'app per le funzioni. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [piano a consumo](../functions-scale.md#consumption-plan) senza server. |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa un'app per le funzioni con un archivio Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
