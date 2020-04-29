---
title: Impedire la traduzione del contenuto - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Impedire la traduzione del contenuto con l'API Traduzione testuale. L'API Traduzione testuale consente di aggiungere tag ai contenuti in modo che non vengano tradotti.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052477"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Come impedire la traduzione del contenuto con l'API Traduzione testuale

L'API Traduzione testuale consente di aggiungere tag ai contenuti in modo che non vengano tradotti. Ad esempio è possibile aggiungere tag al codice, al nome di un marchio o a una parola o frase che non deve essere localizzata.

## <a name="methods-for-preventing-translation"></a>Metodi per impedire la traduzione

1. Aggiungere tag al contenuto con `notranslate`. Si tratta di un modello che funziona solo quando il textType di input è impostato come HTML

   Esempio:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Usare il [dizionario dinamico](dynamic-dictionary.md) per definire una traduzione specifica.

3. Non passare la stringa all'API Traduzione testuale per la traduzione.

4. Traduttore personalizzato: usare un [dizionario in un convertitore personalizzato](custom-translator/what-is-dictionary.md) per prescrivere la traduzione di una frase con probabilità del 100%.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Evitare la traduzione nella chiamata dell'API Traduzione](reference/v3-0-translate.md)
