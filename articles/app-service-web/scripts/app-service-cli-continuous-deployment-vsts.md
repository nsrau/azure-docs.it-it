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
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a2009cb07ae2ae7cf716d77a7d9eac7482138ab1
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Creare un'App Web con distribuzione continua da Visual Studio Team Services

Questo script di esempio crea un'App Web nel servizio app con le relative risorse correlate e quindi configura la distribuzione continua da un repository di Visual Studio Team Services (VSTS). 

Se necessario, installare l'interfaccia della riga di comando di Azure usando l'istruzione presente nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Verificare inoltre se:

- È stata creata una connessione con Azure usando il comando `az login`.
- Il codice dell'applicazione è in un repository di Visual Studio Team Services di cui si è proprietari.
- È stato [creato un token di accesso nell'account di Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="create-app-sample"></a>Esempio di creazione di app

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Creare un'App Web con distribuzione continua da Visual Studio Team Services")]

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
