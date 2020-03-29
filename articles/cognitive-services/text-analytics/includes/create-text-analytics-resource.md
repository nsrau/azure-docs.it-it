---
title: Creare una risorsa Analisi del testo di Servizi cognitiviCreate a Cognitive Services Text Analytics resource
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383494"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di Servizi cognitiviCreate a Cognitive Services Text Analytics resource

1. Accedere al [portale](https://portal.azure.com)di Azure .
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
