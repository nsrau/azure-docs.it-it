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
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996177"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Come impedire la conversione del contenuto con il traduttore

Il convertitore consente di contrassegnare il contenuto in modo che non venga tradotto. Ad esempio è possibile aggiungere tag al codice, al nome di un marchio o a una parola o frase che non deve essere localizzata.

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

3. Non passare la stringa al convertitore per la conversione.

4. Traduttore personalizzato: usare un [dizionario in un convertitore personalizzato](custom-translator/what-is-dictionary.md) per prescrivere la traduzione di una frase con probabilità del 100%.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Evitare la conversione nella chiamata di conversione](reference/v3-0-translate.md)
