---
title: Eseguire istanze di contenitore di Azure-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore del servizio riconoscimento vocale in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 9b77474d63cb47b561f9913ff06be5718aba4152
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96009645"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Distribuire il contenitore del servizio riconoscimento vocale in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore del [servizio riconoscimento vocale](speech-container-howto.md) di servizi cognitivi in [istanze di contenitore](../../container-instances/index.yml)di Azure. Questa procedura illustra la creazione di una risorsa del servizio riconoscimento vocale di Azure. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima completare e inviare il [modulo di richiesta di contenitori di sintesi vocale di servizi cognitivi](https://aka.ms/csgate/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]