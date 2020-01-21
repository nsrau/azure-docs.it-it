---
title: Creare un'app per le funzioni con la distribuzione da GitHub - Interfaccia della riga di comando di Azure
description: Creare un'app per le funzioni e distribuire il codice di funzione da un repository GitHub usando Funzioni di Azure.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: e424737b67be58ac0c3880ad2454c87fab803a15
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922725"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Creare un'app per le funzioni in Azure distribuita da GitHub

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni usando il [Piano a consumo](../functions-scale.md#consumption-plan) con le relative risorse. Lo script configura anche il codice di funzione per la distribuzione continua dal repository GitHub. 

In questo esempio è necessario:

* Un archivio GitHub contenente il codice di funzione per il quale si hanno autorizzazioni amministrative.
* [Token di accesso personale](https://help.github.com/articles/creating-an-access-token-for-command-line-use) per il proprio account GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce usare l'interfaccia della riga di comando di Azure in locale, è necessario installare e usare la versione 2.0 o una versione successiva. Per determinare la versione dell'interfaccia della riga di comando di Azure, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

Questo esempio crea un'app per le funzioni di Azure e distribuisce il codice di funzione da GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea l'account di archiviazione necessario per l'app per le funzioni. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [Piano a consumo](../functions-scale.md#consumption-plan) serverless e la associa a un repository Git o Mercurial. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
