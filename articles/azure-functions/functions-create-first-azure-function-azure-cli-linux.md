---
title: Creare la prima funzione ospitata in Linux in Azure
description: Informazioni su come creare in Azure la prima funzione ospitata in Linux usando gli strumenti da riga di comando, Azure Functions Core Tools e l'interfaccia della riga di comando di Azure.
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
manager: gwallace
ms.openlocfilehash: 34f4dc4b36f4efd75c5a7cd14b3214886955a403
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329684"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Guida introduttiva: Creare la prima funzione ospitata in Linux con gli strumenti da riga di comando

Funzioni di Azure consente di eseguire il codice in un ambiente Linux [senza server](https://azure.com/serverless), senza dover prima creare una macchina virtuale o pubblicare un'applicazione Web. Per l'hosting in Linux è necessario il [runtime di Funzioni 2.x](functions-versions.md). Le funzioni serverless vengono eseguite nel [piano a consumo](functions-scale.md#consumption-plan).

Questa guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per creare la prima app per le funzioni in esecuzione in Linux. Il codice della funzione viene creato in locale e quindi distribuito in Azure usando [Azure Functions Core Tools](functions-run-local.md).

I passaggi seguenti sono supportati in computer Mac, Windows o Linux. Questo articolo descrive come creare funzioni in JavaScript o C#. Per informazioni su come creare funzioni Python, vedere [Creare la prima funzione Python con Core Tools e l'interfaccia della riga di comando di Azure](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

+ Installare [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successiva.

+ Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Abilitare le aggregazioni di estensioni

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Creare un'app per le funzioni Linux in Azure

Per ospitare l'esecuzione delle funzioni in Linux, è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente senza server per l'esecuzione del codice della funzione. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni in esecuzione in Linux usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il segnaposto `<storage_name>` con il nome dell'account di archiviazione. `<app_name>` è anche il dominio DNS predefinito per l'app per le funzioni. Questo nome deve essere univoco in tutte le app di Azure. È necessario impostare anche il runtime `<language>` per l'app per le funzioni, da `dotnet` (C#), `node` (JavaScript/TypeScript) o `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Dopo aver creato l'app per le funzioni, viene visualizzato il messaggio seguente:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Ora è possibile pubblicare il progetto nella nuova app per le funzioni in Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]