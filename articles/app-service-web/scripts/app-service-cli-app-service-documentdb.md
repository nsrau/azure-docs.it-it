---
title: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure : connettere un&quot;app Web a Cosmos DB | Microsoft Docs'
description: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: connettere un&quot;app Web a Cosmos DB'
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
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
ms.openlocfilehash: 46cffb8949f6c8d8b51f952e52c3ddc01839389a
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="connect-a-web-app-to-cosmos-db"></a>Connettere un'app Web a Cosmos DB

Questo scenario illustra come creare un account Cosmos DB di Azure e un'app Web di Azure. Cosmos DB viene quindi collegato all'app Web tramite le impostazioni dell'app.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[principale](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'app Web, il Cosmos DB e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Consente di creare un'App Web di Azure all'interno del piano di servizio app. |
| [az cosmosdb create](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#create) | Crea un account Cosmos DB. Qui vengono archiviati i dati. |
| [az cosmosdb list-keys](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#list-keys) | Elenca le chiavi di accesso per l'account Cosmos DB specificato. |
| [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Crea o aggiorna un'impostazione per un'app Web di Azure. Le impostazioni delle app vengono esposte come variabili di ambiente dell'applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

