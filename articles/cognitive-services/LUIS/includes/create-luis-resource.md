---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821998"
---
## <a name="create-a-luis-resource"></a>Creare una risorsa LUIS

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [crea **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Immettere tutte le impostazioni obbligatorie:

    |Impostazione|Value|
    |--|--|
    |name|Nome desiderato (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Località|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`-piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere la creazione della risorsa. Al termine della creazione, passare alla pagina delle risorse
1. Raccogli `endpoint` configurati e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Value|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. L'aspetto è simile a `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
