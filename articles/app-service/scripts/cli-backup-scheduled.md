---
title: 'Interfaccia della riga di comando: Creare un backup pianificato'
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per automatizzare la distribuzione e la gestione dell'app Servizio app. Questo esempio illustra come creare un backup pianificato per un'app.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: e301b5503cee939846bdcfd1d0c4e755f3debe21
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74686764"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Creare un backup pianificato per un'app del servizio app usando l'interfaccia della riga di comando

Questo script di esempio crea un'app nel servizio app con le relative risorse e quindi crea un backup pianificato per tale app. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Crea un account di archiviazione. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Crea un contenitore di Archiviazione di Azure. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Genera un token di firma di accesso condiviso per un contenitore di archiviazione di Azure.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-update) | Configura una nuova pianificazione del backup per un'app del servizio app. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-show) | Visualizza la pianificazione del backup per un'app del servizio app. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Consente di ottenere un elenco di backup per un'app del servizio app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
