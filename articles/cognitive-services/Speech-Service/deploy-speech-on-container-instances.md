---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di servizio di riconoscimento vocale in un'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711516"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Distribuire il contenitore del servizio di riconoscimento vocale in istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [servizio di riconoscimento vocale](speech-container-howto.md) contenitore di Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura illustra la creazione di una risorsa del servizio di riconoscimento vocale di Azure. Quindi si discuterà il pull dell'immagine contenitore associato. Infine, vengono messi in evidenza la possibilità di provare l'orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario innanzitutto completare e inviare il [modulo di richiesta di contenitori vocale servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
