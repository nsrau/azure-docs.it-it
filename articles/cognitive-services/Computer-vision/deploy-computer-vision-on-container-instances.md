---
title: Eseguire il contenitore Visione artificiale nelle istanze del contenitore di AzureRun Computer Vision container in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Visione artificiale in un'istanza del contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876088"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuire il contenitore Visione artificiale in Istanze del contenitore di AzureDeploy the Computer Vision container to Azure Container Instances

Informazioni su come distribuire il contenitore [Visione artificiale](computer-vision-how-to-install-containers.md) di Servizi cognitivi in [Istanze del contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. In questa procedura viene illustrata la creazione della risorsa Visione artificiale. Quindi viene illustrato il pull dell'immagine contenitore associata. Infine, evidenziamo la possibilità di esercitare l'orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]