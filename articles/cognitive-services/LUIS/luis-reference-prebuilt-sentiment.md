---
title: Analisi del sentiment - LUIS
titleSuffix: Azure Cognitive Services
description: Se l'analisi del sentiment è configurata, è inclusa nella risposta json LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508023"
---
# <a name="sentiment-analysis"></a>Analisi del sentiment
Se l'analisi del sentiment è configurata, è inclusa nella risposta json LUIS. Per ulteriori informazioni sull'analisi del sentiment, vedere la documentazione [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


## <a name="resolution-for-sentiment"></a>Risoluzione per i sentimenti

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati.

#### <a name="english-languagetabenglish"></a>[Lingua inglese](#tab/english)

Quando le impostazioni cultura sono `en-us`, la risposta è:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Altri linguaggi](#tab/other-languages)

Per tutte le altre impostazioni cultura, la risposta è:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

