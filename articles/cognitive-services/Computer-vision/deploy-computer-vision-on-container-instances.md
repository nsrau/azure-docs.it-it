---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di visione artificiale a un'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711615"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuire il contenitore di visione artificiale per istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [visione artificiale](computer-vision-how-to-install-containers.md) contenitore di Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura viene illustrata la creazione della risorsa di visione artificiale. Quindi si discuterà il pull dell'immagine contenitore associato. Infine, vengono messi in evidenza la possibilità di provare l'orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]