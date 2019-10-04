---
title: Creare una risorsa Analisi del testo di servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa Analisi del testo servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377386"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**, quindi passare ad **intelligenza artificiale + Machine Learning** > **analisi del testo**.
   In alternativa, passare a [crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |Name|Immettere un nome (2-64 caratteri).|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata.|
    |Location|Selezionare una località vicina.|
    |Piano tariffario| Immettere **S**, il piano tariffario standard.|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea**e attendere la creazione della risorsa. Il browser reindirizza automaticamente alla pagina delle risorse appena creata.
1. Raccogliere i dati `endpoint` configurati e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Value|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chiavi**|API key|Copiare una delle due chiavi. Si tratta di una stringa alfanumerica di 32 caratteri senza spazi o trattini:`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`< >.|
