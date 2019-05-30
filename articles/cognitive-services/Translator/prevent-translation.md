---
title: Impedire la traduzione del contenuto - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Impedire la traduzione del contenuto con l'API Traduzione testuale.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: ec47c22ed38b7de261653eeeeafa4540b5d2164c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387608"
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

5. Traduttore personalizzato: Usare un [dizionario nel traduttore personalizzato](custom-translator/what-is-dictionary.md) per definire la traduzione di una frase con probabilità 100%.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Evitare la traduzione nella chiamata dell'API Traduzione](reference/v3-0-translate.md)
