---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'App Web con distribuzione continua da Visual Studio Team Services | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'App Web con distribuzione continua da Visual Studio Team Services
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
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c10b7654c02fae631e34572f12482928b39ceb6
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Creare un'App Web con distribuzione continua da Visual Studio Team Services

Questo script di esempio crea un'App Web nel servizio app con le relative risorse correlate e quindi configura la distribuzione continua da un repository di Visual Studio Team Services (VSTS). Per questo esempio sono necessari gli elementi seguenti:

* Repository Visual Studio Team Services contenente il codice dell'applicazione, con le relative autorizzazioni di amministratore.
* [Token di accesso personale](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) per il proprio account Visual Studio Team Services.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Creare un'App Web con distribuzione continua da Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Consente di creare un piano di servizio app. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crea un'App Web di Azure. |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Associa un'App Web di Azure con un archivio Git o Mercurial. |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Apre un'App Web di Azure in un browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).

