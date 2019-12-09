---
title: "Interfaccia della riga di comando: Distribuire i file dell'app con FTP"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per automatizzare la distribuzione e la gestione dell'app Servizio app. Questo esempio illustra come creare un'app e distribuire file con FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 320f293073ce0a97cf4b73b420f38d91c5206013
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688365"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Creare un'app del servizio app e distribuire i file con FTP usando l'interfaccia della riga di comando di Azure

Questo script di esempio crea un'app nel servizio app con le relative risorse e quindi distribuisce una pagina HTML statica con FTP. Per il caricamento FTP, lo script usa [cURL](https://en.wikipedia.org/wiki/CURL) come esempio. È possibile usare qualsiasi strumento FTP per caricare i file.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script 

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az-webapp-deployment-list-publishing-profiles) | Consente di ottenere i dettagli dei profili disponibili per la distribuzione dell'app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
