---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire un&quot;App Web in un ambiente di gestione temporanea | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Distribuire un&quot;App Web in un ambiente di gestione temporanea
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>Distribuire un'App Web in un ambiente di gestione temporanea

Questo script di esempio esegue le operazioni seguenti usando l'interfaccia della riga di comando di Azure 2.0: 

* Creare un'App Web nel Servizio app di Azure nell'area di Azure Europa occidentale.
* Aggiornare il piano di servizio app al livello STANDARD (minimo per gli slot di distribuzione).
* Configurare uno slot di distribuzione denominato "staging".
* Distribuire il codice dell'App Web da GitHub allo slot di gestione temporanea.
* Aprire lo slot di gestione temporanea distribuito nel browser per la verifica.
* Trasferire lo slot di gestione temporanea nell'ambiente di produzione.

## <a name="prerequisites"></a>Prerequisiti

* Eseguire `az login` per accedere ad Azure.
* Inserire il codice dell'App Web in un archivio GitHub di cui si è proprietari.

> [!NOTE]
> Se si usa un archivio GitHub pubblico di cui non si è proprietari, il servizio app distribuirà codice da quell'archivio GitHub ma non potrà impostare la chiave SSH e i webhook necessari per la distribuzione continua.
>
>

## <a name="create-vm-sample"></a>Creare una macchina virtuale di esempio

[!code-azurecli[main](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Distribuire un'App Web in un ambiente di gestione temporanea")]

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
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Aggiorna un piano di servizio app per ridimensionare il piano tariffario. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | Creare uno slot di distribuzione. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Associa un'App Web con un archivio Git o Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Apre un'App Web in un browser. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | Trasferire un determinato slot di distribuzione nell'ambiente di produzione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per App Web del Servizio app di Azure sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure]().

