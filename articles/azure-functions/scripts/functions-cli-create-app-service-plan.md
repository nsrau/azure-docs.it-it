---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;app per le funzioni in un piano di servizio app | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;app per le funzioni in un piano di servizio app
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d887999d1118e498293fb13b9612828d914b0ae
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-in-an-app-service-plan"></a>Creare un'app per le funzioni in un piano di servizio app

Questo script di esempio crea un'app per le funzioni di Azure che è un contenitore per le funzioni. L'app per le funzioni verrà creata usando un piano di servizio app dedicato, ovvero le risorse del server sono sempre attive.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

Esempio di creazione di app

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Creare una funzione di Azure in un piano di servizio app")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, eseguire questo comando per rimuovere il gruppo di risorse, l'app del servizio app e tutte le risorse correlate.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appserviceplan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Consente di creare un piano di servizio app. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Crea un'app per le funzioni di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
