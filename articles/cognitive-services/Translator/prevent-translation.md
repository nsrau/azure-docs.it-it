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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184825"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Come impedire la traduzione del contenuto con l'API Traduzione testuale

L'API Traduzione testuale consente di aggiungere tag ai contenuti in modo che non vengano tradotti. Ad esempio è possibile aggiungere tag al codice, al nome di un marchio o a una parola o frase che non deve essere localizzata.

## <a name="methods-for-preventing-translation"></a>Metodi per impedire la traduzione
1. Eseguire l'escape di un tag @somethingtopassthrough di Twitter o #somethingtopassthrough. Annullare l'escape dopo la traduzione. Si tratta dell'espressione regolare per i tag Twitter validi: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Un tag deve iniziare con un segno "@", seguito da un carattere, seguito da uno o più caratteri, cifre o caratteri di sottolineatura. È consigliabile tenere i tag brevi e il tag di apertura deve essere preceduto da uno spazio.

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
