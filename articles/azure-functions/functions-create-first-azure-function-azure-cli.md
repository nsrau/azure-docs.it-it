---
title: Creare la prima funzione usando l'interfaccia della riga di comando di Azure
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite l'interfaccia della riga di comando di Azure e Core Tools di Funzioni di Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 625fcda4b612fdeda940f9219238ac79354dd056
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444657"
---
# <a name="create-your-first-function-from-the-command-line"></a>Creare la prima funzione dalla riga di comando

Questa guida introduttiva illustra come creare la prima funzione dalla riga di comando o terminale. Si userà l'interfaccia della riga di comando di Azure per creare un'app per le funzioni, ovvero l'infrastruttura [senza server](https://azure.microsoft.com/solutions/serverless/) che ospita la funzione. Il progetto del codice della funzione viene generato da un modello tramite [Core Tools di Funzioni di Azure](functions-run-local.md), che viene usato anche per distribuire il progetto dell'app della funzione in Azure.

È possibile eseguire queste procedure con un computer Mac, Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

+ Installare [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successive.

+ Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Creare un progetto di app per la funzione locale

Eseguire il comando seguente dalla riga di comando per creare un progetto di app della funzione nella cartella `MyFunctionProj` della directory locale corrente. Viene creato anche un repository di GitHub in `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Quando richiesto, selezionare un runtime del ruolo di lavoro dalle seguenti opzioni linguaggio:

+ `dotnet`: crea un progetto libreria di classi .NET (.csproj).
+ `node`: crea un progetto JavaScript.

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Usare il comando seguente per passare alla nuova cartella del progetto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

Nel comando seguente sostituire il segnaposto `<APP_NAME>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<STORAGE_NAME>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<APP_NAME>` sia univoco tra tutte le app in Azure. È necessario impostare anche il runtime `<language>` per l'app per le funzioni, da `dotnet` (C#) o `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

L'impostazione del parametro _consumption-plan-location_ indica che l'app per le funzioni è ospitata in un piano di hosting a consumo. In questo piano serverless, le risorse vengono aggiunte in modo dinamico come richiesto dalle funzioni e si paga solo quando le funzioni sono in esecuzione. Per altre informazioni, vedere [Scegliere il piano di hosting corretto](functions-scale.md).

Al termine della creazione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

