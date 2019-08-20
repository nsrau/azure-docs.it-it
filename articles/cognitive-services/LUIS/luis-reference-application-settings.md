---
title: Impostazioni dell'applicazione-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni sulle impostazioni delle applicazioni per le app per la comprensione del linguaggio.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6d516590b90efd937789fa0847b707d2521f6459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932585"
---
# <a name="application-settings"></a>Impostazioni dell'applicazione

Queste impostazioni dell'applicazione vengono archiviate nell'app [esportata](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [aggiornate](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con le API REST. Modificando le impostazioni della versione dell'app si reimposta lo stato di training dell'app su non sottoposto a training.

|Impostazione|Valore predefinito|Note|
|--|--|--|
|NormalizePunctuation|True|Rimuove la punteggiatura.|
|NormalizeDiacritics|True|Rimuove i segni diacritici.|

## <a name="diacritics-normalization"></a>Normalizzazione segni diacritici 

Attivare la normalizzazione dell'espressione per i segni diacritici nel file dell' `settings` app Luis JSON nel parametro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Negli enunciati seguenti viene illustrato il modo in cui la normalizzazione dei segni diacritici influisca sulle espressioni:

|Con segni diacritici impostati su false|Con segni diacritici impostati su true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Supporto della lingua per i segni diacritici

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Segni diacritici `pt-br` del Portoghese brasiliano

|Segni diacritici impostati su false|Segni diacritici impostati su true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Segni `nl-nl` diacritici olandesi

|Segni diacritici impostati su false|Segni diacritici impostati su true|
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

#### <a name="french-fr--diacritics"></a>Segni `fr-` diacritici francesi

Sono incluse sia le impostazioni cultura di lingua francese sia quelle canadesi.

|Segni diacritici impostati su false|Segni diacritici impostati su true|
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

#### <a name="german-de-de-diacritics"></a>Segni `de-de` diacritici tedeschi

|Segni diacritici impostati su false|Segni diacritici impostati su true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Segni `it-it` diacritici italiani

|Segni diacritici impostati su false|Segni diacritici impostati su true|
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

#### <a name="spanish-es--diacritics"></a>Segni `es-` diacritici in spagnolo

Sono inclusi sia spagnolo che messicano.

|Segni diacritici impostati su false|Segni diacritici impostati su true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizzazione punteggiatura

Attivare la `settings` normalizzazione dell'espressione per la punteggiatura al file dell'app Luis JSON nel parametro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Le espressioni seguenti mostrano come i segni diacritici influiscano sulle espressioni:

|Con segni diacritici impostati su false|Con segni diacritici impostati su true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Segni di punteggiatura rimossi

La punteggiatura seguente viene rimossa `NormalizePunctuation` con la è impostata su true.

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
