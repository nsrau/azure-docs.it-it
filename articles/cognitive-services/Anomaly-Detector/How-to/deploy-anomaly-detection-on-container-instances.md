---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di rilevatore di anomalie in un'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711692"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuire un contenitore di rilevatore di anomalie in istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [rilevatore di anomalie](../anomaly-detector-container-howto.md) contenitore di Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura viene illustrata la creazione di una risorsa di rilevatore di anomalie. Quindi si discuterà il pull dell'immagine contenitore associato. Infine, vengono messi in evidenza la possibilità di provare l'orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

È necessario innanzitutto completare e inviare il [modulo di richiesta di contenitore di rilevatore di anomalie](https://aka.ms/adcontainer) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]