---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore per il riconoscimento modulo a un'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711395"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuire il contenitore per il riconoscimento modulo per istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [Form riconoscimento](form-recognizer-container-howto.md) contenitore di Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura viene illustrata la creazione di una risorsa per il riconoscimento modulo di Azure. Quindi si discuterà il pull dell'immagine contenitore associato. Infine, vengono messi in evidenza la possibilità di provare l'orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima di tutto completare e inviare il [modulo di richiesta dei contenitori di Riconoscimento modulo in Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess) per richiedere l'accesso al contenitore. In questo modo verrà ance effettuata l'iscrizione a Visione artificiale. Non è necessario iscriversi con il modulo di richiesta di Visione artificiale separatamente. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
