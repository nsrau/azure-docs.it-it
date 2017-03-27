---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;App Web con distribuzione da GitHub | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;App Web con distribuzione da GitHub
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>Creare un'App Web con la distribuzione da GitHub

Questo script di esempio esegue le operazioni seguenti usando l'interfaccia della riga di comando di Azure 2.0:

* Creare un'App Web nel Servizio app di Azure nell'area di Azure Europa occidentale.
* Distribuire il codice dell'App Web da GitHub.
* Visualizzare l'App Web di Azure distribuita nel browser.

## <a name="prerequisites"></a>Prerequisiti

* Eseguire `az login` per accedere ad Azure.
* Inserire il codice dell'App Web in un repository GitHub.

> [!NOTE]
> Se si usa un archivio GitHub pubblico di cui non si è proprietari, il servizio app distribuirà codice da quell'archivio GitHub ma non potrà impostare la chiave SSH e i webhook necessari per la distribuzione continua.
>
>

## <a name="create-app-sample"></a>Esempio di creazione di app

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Creare un'App Web con la distribuzione da GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea un'App Web di Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa un'App Web di Azure con un archivio Git o Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Apre un'App Web di Azure in un browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).
