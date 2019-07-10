---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711824"
---
## <a name="create-a-speech-resource"></a>Crea una risorsa di riconoscimento vocale

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Create **vocale** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) risorsa
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |NOME|Nome desiderato (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare qualsiasi località nelle vicinanze e disponibile|
    |Piano tariffario|`F0` -il livello di prezzo minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibili|

1. Fare clic su **Create** e attendere che la risorsa da creare. Dopo averlo creato, passare alla pagina di risorse
1. Raccolta configurata `endpoint` e una chiave API:

    |Scheda risorse nel portale|Impostazione|Value|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. È simile a `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
