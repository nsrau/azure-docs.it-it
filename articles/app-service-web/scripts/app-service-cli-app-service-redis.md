---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Connettere un'App Web a una cache Redis | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Connettere un'App Web a una cache Redis
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
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: b697c8508a6c3422b6b0d0ca36843a9c884b505f
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>Connettere un'App Web a una cache Redis

Questo scenario illustra come creare una cache Redis di Azure e un'App Web di Azure. La cache Redis viene quindi collegata all'App Web usando le impostazioni dell'app.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Cache Redis di Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'App Web, la cache Redis e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | Crea un'App Web di Azure. |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) | Creare una nuova istanza della cache Redis. Qui vengono archiviati i dati. |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#list-keys) | Elenca le chiavi di accesso per l'istanza della cache Redis. |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#set) | Crea o aggiorna un'impostazione per un'app Web di Azure. Le impostazioni delle app vengono esposte come variabili di ambiente dell'applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

