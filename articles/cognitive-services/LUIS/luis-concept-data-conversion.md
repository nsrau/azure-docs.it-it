---
title: Conversione dati-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su come modificare le espressioni prima delle stime in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdce1a49ce6c6531ce344de5aa157717fe72c609
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560800"
---
# <a name="convert-data-format-of-utterances"></a>Convertire il formato dati delle espressioni
LUIS usa Servizi cognitivi e il Servizio di riconoscimento vocale per convertire le espressioni da vocali a testuali prima delle stime. 

## <a name="speech-to-intent-conversion-concepts"></a>Concetti di conversione del riconoscimento finalità voce
La conversione del riconoscimento vocale in LUIS consente di inviare espressioni vocali a un endpoint e ricevere una risposta di stima da LUIS. Il processo è un'integrazione del servizio [Voce](https://docs.microsoft.com/azure/cognitive-services/Speech) di LUIS. Per altre informazioni sul riconoscimento finalità voce, eseguire un'[esercitazione](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Requisiti chiave
Per questa integrazione non è necessario creare una chiave **API Riconoscimento vocale Bing** . È sufficiente la chiave **Language Understanding** creata nel portale di Azure. Non usare la chiave di avvio LUIS.

### <a name="pricing-tier"></a>Piano tariffario
Questa integrazione usa un modello [prezzi](luis-boundaries.md#key-limits) diverso rispetto ai normali piani tariffari di Language Understanding. 

### <a name="quota-usage"></a>Uso delle quote
Per informazioni, vedere [Key limits](luis-boundaries.md#key-limits) (Limiti chiave). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrazione di dati](luis-concept-data-extraction.md)

