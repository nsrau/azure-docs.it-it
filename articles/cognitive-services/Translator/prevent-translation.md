---
title: Impedisci conversione contenuto-Translator
titleSuffix: Azure Cognitive Services
description: Impedisci la conversione del contenuto con il traduttore. Il convertitore consente di contrassegnare il contenuto in modo che non venga tradotto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742061"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Come impedire la conversione del contenuto con il traduttore

Il convertitore consente di contrassegnare il contenuto in modo che non venga tradotto. Ad esempio, potrebbe essere necessario contrassegnare del codice, il nome di un marchio o una parola/frase la cui localizzazione non avrebbe senso.

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

2. Aggiungere tag al contenuto con `translate="no"`. Funziona solo quando il textType di input è impostato come HTML

   Esempio:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
