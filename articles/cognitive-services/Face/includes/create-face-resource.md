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
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523190"
---
## <a name="create-an-face-resource"></a>Creare una risorsa Face

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Crea risorsa **viso** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Immettere tutte le impostazioni obbligatorie:

    |Impostazione|Valore|
    |--|--|
    |Name|Nome desiderato (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Percorso|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`-piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere la creazione della risorsa. Al termine della creazione, passare alla pagina delle risorse
1. Raccogli `endpoint` configurati e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. L'aspetto è simile a `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
