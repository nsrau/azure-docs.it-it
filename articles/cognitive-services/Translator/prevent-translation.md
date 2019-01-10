---
title: Impedire la traduzione del contenuto - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Impedire la traduzione del contenuto con l'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 70bc4a50240abd8e4b67ff572f8c472f7519569a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556850"
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
