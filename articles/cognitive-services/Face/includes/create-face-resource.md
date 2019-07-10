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
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711582"
---
## <a name="create-an-face-resource"></a>Creare una risorsa di visi

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Create **Face** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) risorsa
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
    |**Panoramica**|Endpoint|Copiare l'endpoint. È simile a `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
