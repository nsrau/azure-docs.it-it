---
title: Impostazioni dell'applicazione - LUIS
description: Le impostazioni delle applicazioni per le app per la comprensione della lingua di Servizi cognitivi di Azure vengono archiviate nell'app e nel portale.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382194"
---
# <a name="application-settings"></a>Impostazioni dell'applicazione

Queste impostazioni dell'applicazione vengono archiviate nell'app [esportata](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [aggiornate](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con le API REST. La modifica delle impostazioni della versione dell'app reimposta lo stato di formazione dell'app su non addestrato.

Scopri [i concetti](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) di segni diacritici e punteggiatura.

|Impostazione|Valore predefinito|Note|
|--|--|--|
|NormalizeUnctuation (NormalizePctuazione)|True|Rimuove la punteggiatura.|
|NormalizeDiacritics|True|Rimuove i segni diacritici.|

## <a name="diacritics-normalization"></a>Normalizzazione dei segni diacritici

Attivare la normalizzazione delle espressioni per i segni diacritici nel file dell'app `settings` JSON LUIS nel parametro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Le espressioni seguenti mostrano l'impatto della normalizzazione dei diacritici sulle espressioni:The following utterances show how diacritics normalization impacts utterances:

|Con segni diacritici impostati su false|Con segni diacritici impostati su true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Supporto linguistico per i segni diacritici

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Segni `pt-br` diacritici portoghesi brasiliani

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

Questo include sia le sottoculture francesi che canadesi.

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

#### <a name="spanish-es--diacritics"></a>Segni `es-` diacritici spagnoli

Questo include sia spagnolo e messicano canadese.

|Segni diacritici impostati su false|Segni diacritici impostati su true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizzazione della punteggiatura

Attivare la normalizzazione dell'espressione per la punteggiatura al `settings` file dell'app JSON LUIS nel parametro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Le espressioni seguenti mostrano l'impatto della punteggiatura sulle espressioni:The following utterances show how punctuation impacts utterances:

|Con la punteggiatura impostata su False|Con la punteggiatura impostata su True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Punteggiatura rimossa

La punteggiatura seguente viene `NormalizePunctuation` rimossa con è impostata su true.

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

* Scopri [i concetti](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) di segni diacritici e punteggiatura.
