---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422275"
---
## <a name="create-a-speech-resource"></a>Creare una risorsa di riconoscimento vocaleCreate a Speech resource

1. Accedere al portale di [AzureSign](https://portal.azure.com) into the Azure portal
1. Fare clic su Crea risorsa [ **di riconoscimento vocale** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Dopo la creazione, passare alla pagina delle risorse
1. Raccogliere `endpoint` configurato e una chiave API:Collect configured and an API key:

    |Scheda Risorsa nel portale|Impostazione|valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Chiavi**|API key|Copia 1 delle due chiavi. Si tratta di una stringa di 32 caratteri alfanumerici senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
