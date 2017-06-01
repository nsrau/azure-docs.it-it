---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Connettere un&quot;App Web a un account di archiviazione | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Connettere un&quot;App Web a un account di archiviazione
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: c44e1da06bf7203c43132212abc80c3179090939
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>Connettere un'App Web a un account di archiviazione

Questo scenario illustra come creare un account di archiviazione di Azure e un'App Web di Azure. L'account di archiviazione viene quindi collegato all'App Web usando le impostazioni dell'app.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Archiviazione di Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'App Web, l'account di archiviazione e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Consente di creare un'App Web di Azure all'interno del piano di servizio app. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea un account di archiviazione. Qui vengono archiviati gli asset statici. |
| [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account#show-connection-string) | |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Crea o aggiorna un'impostazione per un'app Web di Azure. Le impostazioni delle app vengono esposte come variabili di ambiente dell'applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

