---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di LUIS in un'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711571"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuire il contenitore di LUIS (Language Understanding) in istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [LUIS](luis-container-howto.md) contenitore di Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura viene illustrata la creazione di una risorsa di rilevatore di anomalie. Quindi si discuterà il pull dell'immagine contenitore associato. Infine, vengono messi in evidenza la possibilità di provare l'orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
