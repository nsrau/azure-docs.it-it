---
title: Creare una risorsa Analisi del testo di Servizi cognitiviCreate a Cognitive Services Text Analytics resource
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876428"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di Servizi cognitiviCreate a Cognitive Services Text Analytics resource

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**e quindi passare all'iaeldi e l'analisi del**testo**di **Machine Learning** > .
   In alternativa, vai a [Crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Immettere un nome (2-64 caratteri).|
    |Subscription|Scegliere la sottoscrizione appropriata.|
    |Location|Selezionare una posizione vicina.|
    |Piano tariffario| Immettere **S**, il piano tariffario standard.|
    |Resource group|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea**e attendere la creazione della risorsa. Il browser reindirizza automaticamente alla pagina delle risorse appena creata.
1. Raccogliere la `endpoint` configurazione e una chiave API:Collect the configured and an API key:

    |Scheda Risorsa nel portale|Impostazione|valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`a .|
    |**Chiavi**|API key|Copiare una delle due chiavi. È una stringa alfanumerica di 32 caratteri senza `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` spazi o trattini: <>.|
