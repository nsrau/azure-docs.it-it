---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878367"
---
## <a name="create-an-face-resource"></a>Creare una risorsa Face

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Crea risorsa **viso** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Immettere tutte le impostazioni richieste:

    |Impostazione|Valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Al termine della creazione, passare alla pagina delle risorse
1. Raccolta configurata `endpoint` e chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Overview**|Endpoint|Copiare l'endpoint. Ha un aspetto simile a `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chiavi**|Chiave API|Copiare 1 delle due chiavi. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
