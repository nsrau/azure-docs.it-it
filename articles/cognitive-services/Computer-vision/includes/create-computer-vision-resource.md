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
ms.openlocfilehash: cbf11c13bfb5c90739ea67fab92df08796a88e50
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711604"
---
## <a name="create-an-computer-vision-resource"></a>Creare una risorsa di visione artificiale

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Create **visione artificiale** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) risorsa
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
    |**Panoramica**|Endpoint|Copiare l'endpoint. È simile a `https://computer-vision.cognitiveservices.azure.com/`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
