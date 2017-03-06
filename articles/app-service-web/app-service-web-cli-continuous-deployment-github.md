---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire App Web da GitHub in modo continuo | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire App Web da GitHub in modo continuo
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>Distribuire App Web da GitHub in modo continuo

Questo script di esempio esegue le operazioni seguenti usando l'interfaccia della riga di comando di Azure 2.0: 

* Creare un'App Web nel Servizio app di Azure nell'area di Azure Europa occidentale. 
* Distribuire il codice dell'App Web da GitHub.
* Visualizzare l'App Web distribuita nel browser.

## <a name="prerequisites"></a>Prerequisiti

* Eseguire `az login` per accedere ad Azure.
* Inserire il codice dell'App Web in un archivio GitHub di cui si è proprietari.

> [!NOTE]
> Se si usa un archivio GitHub pubblico di cui non si è proprietari, il servizio app distribuirà codice da quell'archivio GitHub ma non potrà impostare la chiave SSH e i webhook necessari per la distribuzione continua.
>
>

## <a name="create-vm-sample"></a>Creare una macchina virtuale di esempio

[!code-azurecli[main](../../cli_scripts/app-service/deploy-github/deploy-github.sh "Distribuire App Web da GitHub in modo continuo")]

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
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Crea un'App Web. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Associa un'App Web con un archivio Git o Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Apre un'App Web in un browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per App Web del Servizio app di Azure sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure]().

