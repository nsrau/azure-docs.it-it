---
title: Eseguire Visione artificiale contenitore in istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Visione artificiale in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1c858432a3382e6dbc8e479aab11b18dc5eebfe4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499103"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuire il contenitore Visione artificiale nelle istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [visione artificiale](computer-vision-how-to-install-containers.md) di servizi cognitivi in [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione della risorsa Visione artificiale. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]