---
title: Creare una risorsa Analisi del testo di servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa per Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876428"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**, quindi passare ad **intelligenza artificiale + Machine Learning** > **analisi del testo**.
   In alternativa, passare a [crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|valore|
    |--|--|
    |Nome|Immettere un nome (2-64 caratteri).|
    |Subscription|Scegliere la sottoscrizione appropriata.|
    |Location|Selezionare una località vicina.|
    |Piano tariffario| Immettere **S**, il piano tariffario standard.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea**e attendere la creazione della risorsa. Il browser reindirizza automaticamente alla pagina delle risorse appena creata.
1. Raccogliere i dati `endpoint` configurati e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chiavi**|API key|Copiare una delle due chiavi. Si tratta di una stringa alfanumerica di 32 caratteri senza spazi o trattini: `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` <>.|
