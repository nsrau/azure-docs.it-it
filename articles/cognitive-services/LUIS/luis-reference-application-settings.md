---
title: Impostazioni dell'applicazione
titleSuffix: Azure Cognitive Services
description: Informazioni sulle impostazioni di applicazioni per le app di comprensione del linguaggio.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: a8a1f6fe9372d013d310c557161ceb813b8a478b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163645"
---
# <a name="application-settings"></a>Impostazioni dell'applicazione

Le impostazioni dell'applicazione vengono archiviate nel [esportati](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app e [aggiornata](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con le API REST. Modifica delle impostazioni di versione app Reimposta lo stato training dell'app non sottoposto a training.

|Impostazione|Valore predefinito|Note|
|--|--|--|
|NormalizePunctuation|True|Rimuove la punteggiatura.|
|NormalizeDiacritics|True|Rimuove i segni diacritici.|

## <a name="diacritics-normalization"></a>Normalizzazione dei segni diacritici 

Attivare la normalizzazione utterance dei segni diacritici al file app LUIS JSON nel `settings` parametro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Le espressioni seguenti mostrano come normalizzazione dei segni diacritici influisce sulle espressioni:

|Con i segni diacritici impostati su false|Con i segni diacritici impostato su true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Supporto delle lingue per i segni diacritici

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portoghese (Brasile) `pt-br` segni diacritici

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>Olandese `nl-nl` segni diacritici

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francese `fr-` segni diacritici

Si tratta di impostazioni cultura secondarie sia una francese canadese.

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>Tedesco `de-de` segni diacritici

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italiano `it-it` segni diacritici

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spagnolo `es-` segni diacritici

Ciò include canadese sia spagnolo messicano.

|I segni diacritici impostato su false|I segni diacritici impostato su false|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizzazione dei segni di punteggiatura

Attivare la normalizzazione utterance la punteggiatura al file app LUIS JSON nel `settings` parametro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Le espressioni seguenti mostrano come i segni diacritici influisce sulle espressioni:

|Con i segni diacritici impostati su False|Con i segni diacritici impostati su True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Punteggiatura

La punteggiatura seguente è stata rimossa con `NormalizePunctuation` è impostato su true.

|Punteggiatura|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
