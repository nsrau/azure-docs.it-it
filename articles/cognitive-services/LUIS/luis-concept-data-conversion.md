---
title: Concetti relativi alla conversione di dati in LUIS - Language Understanding
titleSuffix: Azure Cognitive Services
description: Informazioni su come modificare le espressioni prima delle stime in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3862a0dbd94b5764cf506b05201d8dc60430fc7d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038970"
---
# <a name="data-conversion-concepts-in-luis"></a>Concetti di conversione dei dati in LUIS
LUIS usa Servizi cognitivi e il Servizio di riconoscimento vocale per convertire le espressioni da vocali a testuali prima delle stime. 

## <a name="speech-to-intent-conversion-concepts"></a>Concetti di conversione del riconoscimento finalità voce
La conversione del riconoscimento vocale in LUIS consente di inviare espressioni vocali a un endpoint e ricevere una risposta di stima da LUIS. Il processo è un'integrazione del servizio [Voce](https://docs.microsoft.com/azure/cognitive-services/Speech) di LUIS. 

### <a name="key-requirements"></a>Requisiti chiave
Per questa integrazione non è necessario creare una chiave **API Riconoscimento vocale Bing** . È sufficiente la chiave **Language Understanding** creata nel portale di Azure. Non usare la chiave di avvio di LUIS in quanto non funziona con questa integrazione.

### <a name="new-endpoint"></a>Nuovo endpoint 
Questa integrazione crea un nuovo endpoint e un modello dei [prezzi](luis-boundaries.md#key-limits). Tramite [Voce SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk), l'endpoint può ricevere espressioni sia vocali che testuali e può quindi essere usato come endpoint singolo. 

### <a name="quota-usage"></a>Uso delle quote
Per informazioni, vedere [Key limits](luis-boundaries.md#key-limits) (Limiti chiave). 

### <a name="data-retention"></a>Conservazione dei dati
I dati inviati all'endpoint tramite Voce SDK, sia vocali che testuali, vengono usati solo per migliorare il modello conversione voce/testo. Non vengono usati per migliorare la funzionalità Voce o LUIS. Quando l'app LUIS viene eliminata, anche i dati mantenuti vengono eliminati.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usa riconoscimento vocale](luis-tutorial-speech-to-intent.md)

