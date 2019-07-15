---
title: Crea una risorsa di Analitica di testo di servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di Analitica di testo di servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877459"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Crea una risorsa di Analitica di testo di servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **crea una risorsa**, quindi fare clic sulla **intelligenza artificiale e Machine Learning** > **testo Analitica**.
   In alternativa, passare a [creare testo Analitica](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |Name|Immettere un nome (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località nelle vicinanze|
    |Piano tariffario| Immettere **S**, il piano tariffario standard|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibili|

1. Selezionare **Create** e attendere che la risorsa da creare. Il browser reindirizza automaticamente alla pagina di risorse appena creato.
1. Raccogliere configurato `endpoint` e una chiave API:

    |Scheda risorse nel portale|Impostazione|Value|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Chiavi**|API key|Copiare una delle due chiavi. È una stringa alfanumerica di 32 caratteri senza spazi o trattini: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
