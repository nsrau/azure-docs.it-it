---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;App Web con distribuzione continua da Visual Studio Team Services | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;App Web con distribuzione continua da Visual Studio Team Services
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: e5259bf40819aa799651e09ea5de7138e2a8ac7d
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Creare un'App Web con distribuzione continua da Visual Studio Team Services

Questo script di esempio crea un'App Web nel servizio app con le relative risorse correlate e quindi configura la distribuzione continua da un repository di Visual Studio Team Services (VSTS). Per questo esempio sono necessari gli elementi seguenti:

* Repository Visual Studio Team Services contenente il codice dell'applicazione, con le relative autorizzazioni di amministratore.
* [Token di accesso personale](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) per il proprio account Visual Studio Team Services.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="create-app-sample"></a>Esempio di creazione di app

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Creare un'App Web con distribuzione continua da Visual Studio Team Services")]

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

