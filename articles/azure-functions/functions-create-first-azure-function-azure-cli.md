---
title: Creare la prima funzione dall'interfaccia della riga di comando di Azure| Microsoft Docs
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite l'interfaccia della riga di comando di Azure.
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/08/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 4356d00b2694224f52a9359cd4a57d3a70a34d18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Creare la prima funzione usando l'interfaccia della riga di comando di Azure

Questa guida introduttiva illustra come usare Funzioni di Azure per creare la prima funzione. Si userà l'interfaccia della riga di comando di Azure per creare un'app per le funzioni, ovvero l'infrastruttura [senza server](https://azure.microsoft.com/overview/serverless-computing/) che ospita la funzione. Il codice della funzione viene distribuito da un repository GitHub di esempio.    

È possibile eseguire queste procedure con un computer Mac, Windows o Linux. 

## <a name="prerequisites"></a>Prerequisiti 

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

+ Un account [GitHub](https://github.com) attivo. 
+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#create). 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. Il parametro _deployment-source-url_ è un repository di esempio in GitHub che contiene una funzione attivata da HTTP "Hello World".

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope --deployment-source-url https://github.com/Azure-Samples/functions-quickstart
```
L'impostazione del parametro _consumption-plan-location_ indica che l'app per le funzioni è ospitata in un piano di hosting a consumo. In questo piano, le risorse vengono aggiunte in modo dinamico come richiesto dalle funzioni e si paga solo quando le funzioni sono in esecuzione. Per altre informazioni, vedere [Scegliere il piano di hosting corretto](functions-scale.md). 

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

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]