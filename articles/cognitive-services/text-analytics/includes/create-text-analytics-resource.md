---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di analitica di testo di servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455138"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Crea una risorsa di Analitica di testo di servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Selezionare **+ crea una risorsa**, passare alla **intelligenza artificiale e Machine Learning > testo Analitica**, oppure fare clic su [Create **Analitica di testo**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |NOME|Nome desiderato (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località nelle vicinanze|
    |Piano tariffario|`S` -il piano tariffario standard|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibili|

1. Fare clic su **Create** e attendere che la risorsa da creare. Dopo averlo creato, il browser reindirizza automaticamente alla pagina di risorse appena creato
1. Raccolta configurata `endpoint` e una chiave API:

    |Scheda risorse nel portale|Impostazione|Value|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. È simile a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Chiavi**|API key|Copiare 1 delle due chiavi. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|