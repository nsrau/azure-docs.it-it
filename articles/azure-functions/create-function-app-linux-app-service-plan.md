---
title: Creare un'app per le funzioni in Linux in un piano di servizio app di Azure
description: Informazioni su come creare un'app per le funzioni eseguita su Linux in un piano di servizio app tramite l'interfaccia della riga di comando di Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855552"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Creare un'app per le funzioni in Linux in un piano di servizio app di Azure (anteprima)

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore di Servizio app di Azure predefinito. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un'app per le funzioni Linux in Azure, che viene eseguita in un [piano di servizio app](functions-scale.md#app-service-plan). È anche possibile [usare un contenitore personalizzato](functions-create-function-linux-custom-image.md). L'hosting Linux è attualmente in fase di anteprima.

In un piano di servizio app l'utente è responsabile del ridimensionamento dell'app per le funzioni. Per sfruttare i vantaggi delle funzionalità serverless di Funzioni di Azure è anche possibile ospitare le funzioni in Linux in un [piano a consumo](functions-scale.md#consumption-plan).

È possibile eseguire queste procedure con un computer Mac, Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0.21 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Creare un'app per le funzioni in Linux

Per ospitare l'esecuzione delle funzioni in Linux, è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) con un piano di servizio app Linux.

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. È necessario impostare anche il runtime `<language>` per l'app per le funzioni, da `dotnet` (C#) o `node` (JavaScript) o `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Al termine della creazione e distribuzione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Dato che `myAppServicePlan` è un piano di Linux, viene usata l'immagine docker incorporata per creare il contenitore che esegue l'app per le funzioni in Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come creare in Azure un'app per le funzioni ospitata in Linux. Ora è possibile [distribuire un progetto di funzione](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) a questa app per le funzioni. È possibile usare Azure Functions Core Tools per [creare un progetto Functions](functions-run-local.md#create-a-local-functions-project) nel computer locale e distribuirlo alla nuova app per le funzioni Linux.  

> [!div class="nextstepaction"] 
> [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
