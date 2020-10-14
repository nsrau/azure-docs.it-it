---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: e896ac5f4625d36060d713d66fa885f8b24756f1
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014741"
---
## <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Crea risorsa **Rilevamento anomalie** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Al termine della creazione, passare alla pagina delle risorse
1. Raccolta configurata `endpoint` e chiave API:

    |Scheda risorsa nel portale|Impostazione|valore|
    |--|--|--|
    |**Overview**|Endpoint|Copiare l'endpoint. Ha un aspetto simile a `https://westus2.api.cognitive.microsoft.com/`|
    |**Chiavi**|Chiave API|Copiare 1 delle due chiavi. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



