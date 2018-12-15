---
title: Supporto per le lingue
titleSuffix: Azure Cognitive Services
description: LUIS ha un'ampia gamma di funzionalità all'interno del servizio. Non tutte le funzionalità hanno la stessa parità di linguaggio. Assicurarsi che le funzionalità a cui si è interessati siano supportate nella lingua di destinazione. Un'applicazione LUIS è specifica per la cultura e non può essere modificata una volta impostata.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: 452700593d7145e828155c3bd999ad90e2437ba9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099002"
---
# <a name="language-and-region-support-for-luis"></a>Supporto di lingua e area geografica per LUIS

LUIS ha un'ampia gamma di funzionalità all'interno del servizio. Non tutte le funzionalità hanno la stessa parità di linguaggio. Assicurarsi che le funzionalità a cui si è interessati siano supportate nella lingua di destinazione. Un'applicazione LUIS è specifica per la cultura e non può essere modificata una volta impostata.

## <a name="multi-language-luis-apps"></a>Applicazioni LUIS multilingue

Se occorre un'applicazione client LUIS multilingue come una chatbot, sono disponibili alcune opzioni. Se LUIS supporta tutte le lingue, viene sviluppata un'app LUIS per ogni lingua. Ogni app LUIS ha un ID univoco dell'app e un registro di endpoint. Se è necessario fornire il riconoscimento linguistico per una lingua non supportata da LUIS, è possibile utilizzare l’[API Microsoft Translator](../Translator/translator-info-overview.md) per tradurre l'espressione in una lingua supportata, inviarla all'endpoint LUIS e ricevere i punteggi risultanti.

## <a name="languages-supported"></a>Lingue supportate

LUIS riconosce espressioni nelle lingue seguenti:

| Linguaggio |Impostazioni locali  |  Dominio predefinito | Entità predefinita | Suggerimenti di siti | \**[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Valutazione e<br>parole chiave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglese americano |`en-US` | ✔ | ✔  |✔|✔|
| *[Cinese](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Olandese |`nl-NL` |-|  -   |-|✔|
| Francese (Francia) |`fr-FR` |-| ✔ |✔ |✔|
| Francese (Canada) |`fr-CA` |-|   -   |-|✔|
| Tedesco |`de-DE` |-| ✔ |✔ |✔|
| Italiano |`it-IT` |-| ✔ |✔|✔|
| *[Giapponese](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Solo la frase chiave|
| Coreano |`ko-KR` |-|   -   |-|Solo la frase chiave|
| Portoghese (Brasile) |`pt-BR` |-| ✔ |✔ |non tutte le impostazioni cultura secondarie|
| Spagnolo (Spagna) |`es-ES` |-| ✔ |✔|✔|
| Spagnolo (Messico)|`es-MX` |-|  -   |✔|✔|


Le lingue supportate variano per [entità predefinite](luis-reference-prebuilt-entities.md) e [domini predefiniti](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>*Note sul supporto cinese

 - Nella cultura `zh-cn`, LUIS prevede il set di caratteri semplificato cinese al posto del set di caratteri tradizionale.
 - I nomi di finalità, entità, funzioni ed espressioni regolari possono essere in caratteri cinesi o romani.
 - Per informazioni sui domini predefiniti supportati nella cultura `zh-cn`, vedere il [riferimento ai domini predefiniti ](luis-reference-prebuilt-domains.md).
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*Note sul supporto giapponese

 - Poiché LUIS non fornisce analisi sintattiche e non riconosce la differenza tra il Keigo e il giapponese informale, è necessario incorporare i diversi livelli di formalità come esempi di training per le proprie applicazioni.
     - でございます non è uguale a です.
     - です non è uguale a だ.

### <a name="text-analytics-support-notes"></a>\*\*Note di supporto sull'analisi del testo
L'analisi del testo include l'entità predefinita keyPhrase e l'analisi del sentiment. Solo il portoghese è supportato per le impostazioni cultura secondarie: `pt-PT` e `pt-BR`. Tutte le altre lingue sono supportate a livello di impostazioni di cultura primarie. Altre informazioni sulle [lingue supportate](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) per Analisi del testo.

### <a name="speech-api-supported-languages"></a>Lingue supportate per Speech API
Vedere [Lingue supportate](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) dal servizio Voce per le lingue della modalità di dettatura vocale.

### <a name="bing-spell-check-supported-languages"></a>Lingue supportate per il Controllo ortografico Bing
Per un elenco delle lingue e dello stato supportati, vedere [ Lingue supportate](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) per il Controllo ortografico Bing.

## <a name="rare-or-foreign-words-in-an-application"></a>Parole rare o in lingua straniera in un'applicazione
Nelle impostazioni cultura `en-us`, LUIS impara a distinguere la maggior parte delle parole inglesi, compreso lo slang. Nelle impostazioni cultura `zh-cn`, LUIS impara a distinguere la maggior parte dei caratteri cinesi. Se si utilizza una parola rara in `en-us` o un carattere in `zh-cn`, e si nota che LUIS non sembra in grado di distinguere tale parola o carattere, è possibile aggiungere tale parola o carattere a una [funzione dell'elenco di frasi](luis-how-to-add-features.md). Ad esempio, le parole al di fuori della cultura dell'applicazione, ovvero le parole straniere, dovrebbero essere aggiunte a una funzione dell'elenco di frasi. Tale elenco deve essere contrassegnato come non intercambiabile per indicare che il set di parole rare costituisce una classe che LUIS dovrebbe imparare a riconoscere, ma tali parole non sono sinonimi o non sono intercambiabili tra loro.

### <a name="hybrid-languages"></a>Lingue ibride
Le lingue ibride combinano parole di due culture come l'inglese e il cinese. Queste lingue non sono supportate in LUIS perché un'applicazione è basata su un'unica cultura.

## <a name="tokenization"></a>Tokenizzazione
Per eseguire l'apprendimento automatico, LUIS suddivide un’espressione in [token](luis-glossary.md#token) basati sulla cultura.

|Linguaggio|  ogni spazio o carattere speciale | livello di caratteri|parole composte|[entità in formato token restituita](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Cinese||✔||✔|
|Olandese|||✔|✔|
|Inglese (en-us)|✔ ||||
|Francese (fr-FR)|✔||||
|Francese (fr-CA)|✔||||
|Tedesco|||✔|✔|
|Italiano|✔||||
|Giapponese||||✔|
|Coreano||✔||✔|
|Portoghese (Brasile)|✔||||
|Spagnolo (es-ES)|✔||||
|Spagnolo (es-MX)|✔||||
