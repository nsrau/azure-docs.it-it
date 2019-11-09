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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888129"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Come impedire la traduzione del contenuto con l'API Traduzione testuale

L'API Traduzione testuale consente di aggiungere tag ai contenuti in modo che non vengano tradotti. Ad esempio è possibile aggiungere tag al codice, al nome di un marchio o a una parola o frase che non deve essere localizzata.

## <a name="methods-for-preventing-translation"></a>Metodi per impedire la traduzione
1. Eseguire l'escape di un tag @somethingtopassthrough di Twitter o #somethingtopassthrough. Annullare l'escape dopo la traduzione.

2. Aggiungere tag al contenuto con `notranslate`.

   Esempio:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Usare il [dizionario dinamico](dynamic-dictionary.md) per definire una traduzione specifica.

4. Non passare la stringa all'API Traduzione testuale per la traduzione.

5. Traduttore personalizzato: usare un [dizionario in un convertitore personalizzato](custom-translator/what-is-dictionary.md) per prescrivere la traduzione di una frase con probabilità del 100%.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Evitare la traduzione nella chiamata dell'API Traduzione](reference/v3-0-translate.md)
