---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire un&quot;App Web da un archivio Git locale | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire un&quot;App Web da un archivio Git locale
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>Distribuire un'App Web da un archivio Git locale

Questo script di esempio esegue le operazioni seguenti usando l'interfaccia della riga di comando di Azure 2.0: 

* Creare un'App Web nel Servizio app di Azure nell'area di Azure Europa occidentale.
* Distribuire il codice di un'App Web da un archivio Git locale.
* Visualizzare l'App Web distribuita nel browser.

## <a name="prerequisites"></a>Prerequisiti

* Eseguire `az login` per accedere ad Azure.
* Eseguire il commit del codice di un'App Web in un archivio Git locale.

## <a name="create-vm-sample"></a>Creare una macchina virtuale di esempio

[!code-azurecli[main](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "Distribuire un'App Web da un archivio Git locale")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Crea un'App Web |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | Crea una configurazione di controllo di origine per un archivio Git locale. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Apre un'App Web in un browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per App Web del Servizio app di Azure sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure]().
