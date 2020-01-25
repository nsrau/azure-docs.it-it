---
title: Esegui il contenitore viso in istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore viso in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716255"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuire il contenitore viso in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [viso](../face-how-to-install-containers.md) di servizi cognitivi in [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione di una risorsa viso di Azure. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]