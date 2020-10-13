---
title: Creare una risorsa Analisi del testo di Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa per Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779535"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di Servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa** e quindi passare a **Intelligenza artificiale e Machine Learning** > **Analisi del testo**.
   In alternativa, passare a [crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |Nome|Immettere un nome (2-64 caratteri).|
    |Sottoscrizione|Scegliere la sottoscrizione appropriata.|
    |Località|Selezionare una località limitrofa.|
    |Piano tariffario| Immettere **S**, ovvero il piano tariffario standard.|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea** e attendere che venga creata la risorsa. Il browser reindirizza automaticamente alla pagina della risorsa appena creata.
1. Raccogliere i dati configurati `endpoint` e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Overview**|Endpoint|Copiare l'endpoint. Sembra simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Chiavi**|Chiave API|Copiare una delle due chiavi. Si tratta di una stringa alfanumerica di 32 caratteri senza spazi o trattini: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
