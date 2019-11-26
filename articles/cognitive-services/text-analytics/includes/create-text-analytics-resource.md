---
title: Creare una risorsa Analisi del testo di servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa per Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383494"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**, quindi passare ad **ai + Machine Learning** > **analisi del testo**.
   In alternativa, passare a [crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |Nome|Immettere un nome (2-64 caratteri).|
    |sottoscrizione|Selezionare la sottoscrizione appropriata.|
    |Location|Selezionare una località vicina.|
    |Pricing tier| Immettere **S**, il piano tariffario standard.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea**e attendere la creazione della risorsa. Il browser reindirizza automaticamente alla pagina delle risorse appena creata.
1. Raccogliere il `endpoint` configurato e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chiavi**|API key|Copiare una delle due chiavi. Si tratta di una stringa alfanumerica di 32 caratteri senza spazi o trattini: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
