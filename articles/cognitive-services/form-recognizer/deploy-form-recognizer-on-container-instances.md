---
title: Esegui il contenitore del riconoscitore del modulo nelle istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Recognizer form in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913185"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuire il contenitore dei form Recognizer in istanze di contenitore di Azure

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Informazioni su come distribuire il contenitore del sistema di [riconoscimento dei moduli](form-recognizer-container-howto.md) di servizi cognitivi in [istanze di contenitore](../../container-instances/index.yml)di Azure. Questa procedura illustra la creazione di una risorsa di riconoscimento di un modulo di Azure. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]