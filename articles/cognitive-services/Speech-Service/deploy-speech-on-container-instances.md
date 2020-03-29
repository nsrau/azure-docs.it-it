---
title: Eseguire istanze del contenitore di Azure - Servizio di riconoscimento vocaleRun Azure Container Instances - Speech service
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore del servizio di riconoscimento vocale in un'istanza del contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717417"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Distribuire il contenitore del servizio di riconoscimento vocale nelle istanze del contenitore di AzureDeploy the Speech service container to Azure Container Instances

Informazioni su come distribuire il contenitore del [servizio di riconoscimento vocale](speech-container-howto.md) di Servizi cognitivi nelle istanze del [contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione di una risorsa del servizio di riconoscimento vocale di Azure.This procedure demonstrates the creation of an Azure Speech service resource. Quindi viene illustrato il pull dell'immagine contenitore associata. Infine, evidenziamo la possibilità di esercitare l'orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È innanzitutto necessario completare e inviare il [modulo di richiesta di contenitori vocali](https://aka.ms/speechcontainerspreview/) di Servizi cognitivi per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
