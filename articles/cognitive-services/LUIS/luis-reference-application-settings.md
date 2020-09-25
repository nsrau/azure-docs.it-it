---
title: Impostazioni dell'applicazione-LUIS
description: Le impostazioni delle applicazioni per i servizi cognitivi di Azure informazioni sulle app vengono archiviate nell'app e nel portale.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319164"
---
# <a name="app-and-version-settings"></a>Impostazioni di app e versioni

Queste impostazioni vengono archiviate nell'app [esportata](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e aggiornate con le API REST o il portale Luis.

Modificando le impostazioni della versione dell'app si reimposta lo stato di training dell'app su non sottoposto a training.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


Gli esempi e i riferimenti testuali includono:

* [Punteggiatura](#punctuation-normalization)
* [Diacritici](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalizzazione segni diacritici

Negli enunciati seguenti viene illustrato il modo in cui la normalizzazione dei segni diacritici influisca sulle espressioni:

|Con segni diacritici impostati su false|Con segni diacritici impostati su true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Supporto della lingua per i segni diacritici

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Segni diacritici del Portoghese brasiliano `pt-br`

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

#### <a name="dutch-nl-nl-diacritics"></a>`nl-nl`Segni diacritici olandesi

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

#### <a name="french-fr--diacritics"></a>`fr-`Segni diacritici francesi

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

#### <a name="german-de-de-diacritics"></a>`de-de`Segni diacritici tedeschi

|Segni diacritici impostati su false|Segni diacritici impostati su true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>`it-it`Segni diacritici italiani

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

#### <a name="spanish-es--diacritics"></a>`es-`Segni diacritici in spagnolo

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

Negli enunciati seguenti viene illustrato il modo in cui la punteggiatura influisca sulle espressioni:

|Con la punteggiatura impostata su false|Con la punteggiatura impostata su true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Segni di punteggiatura rimossi

La punteggiatura seguente viene rimossa con la `NormalizePunctuation` è impostata su true.

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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [concetti](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) di segni diacritici e punteggiatura.
