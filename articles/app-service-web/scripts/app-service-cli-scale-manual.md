---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Ridimensionare un'App Web manualmente usando l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Ridimensionare un'App Web manualmente usando l'interfaccia della riga di comando di Azure 2.0
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fe05661eb4e2d5c37aebdbfde002b34588db69e7
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="scale-a-web-app-manually"></a>Ridimensionare un'App Web manualmente

In questo scenario si apprenderà come creare un gruppo di risorse, un piano di servizio app e un'App Web. Quindi si dimensionerà il piano di servizio app da una singola istanza a più istanze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-manual/scale-manual.sh "Scalabilità manuale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'App Web e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | Crea un'App Web di Azure. |
| [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) | Aggiorna le proprietà del piano di servizio app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

