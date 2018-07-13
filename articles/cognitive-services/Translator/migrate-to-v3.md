---
title: API Traduzione testuale Microsoft - Migrazione alla versione 3 | Microsoft Docs
description: Informazioni su come eseguire la migrazione dalla versione 2 alla versione 3 dell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377540"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>Migrazione dell'API Traduzione testuale Microsoft dalla versione 2 alla versione 3

Il team di Microsoft Translator ha rilasciato la versione 3 (V3) dell'API Traduzione testuale. Questa versione include nuove funzionalità, metodi deprecati e un nuovo formato per inviare e ricevere dati dal servizio Microsoft Translator. Questo documento fornisce informazioni per modificare le applicazioni da usare nella V3. La V2 verrà deprecata il 30 aprile 2018 e verrà interrotta il 30 aprile 2019.

Alla fine di questo documento sono riportati collegamenti utili per avere maggiori informazioni.

## <a name="summary-of-features"></a>Riepilogo delle funzionalità

* Senza tracciabilità: nella V3 la funzione Senza tracciabilità si applica a tutti i piani tariffari nel portale di Azure. Ciò significa che nessun testo inviato all'API V3 verrà salvato da Microsoft.
* JSON: XML viene sostituito da JSON. Tutti i dati inviati al servizio e ricevuti dal servizio sono in formato JSON.
* Più lingue di destinazione in una singola richiesta: il metodo "Translate" accetta più lingue di destinazione per la traduzione in una singola richiesta. Ad esempio, una singola richiesta può essere ''dalla'' lingua inglese e ''alla'' lingua tedesca, spagnola e giapponese o qualsiasi altro gruppo di lingue.
* Dizionario bilingue: all'API è stato aggiunto un metodo "Bilingual dictionary". Questo metodo include ''lookup'' (ricerca) e ''examples'' (esempi).
* Traslitterazione: all'API è stato aggiunto un metodo "Transliterate". Questo metodo convertirà parole e frasi in un unico script (ad esempio in arabo) in un altro script (ad esempio in latino).
* Lingue: un nuovo metodo "Languages" offre informazioni sulla lingua, in formato JSON, per l'utilizzo con i metodi "translate", "dictionary" e "transliterate".
* Novità di Translate: sono state aggiunte nuove funzioni per il metodo "Translate", in modo da supportare alcune delle funzionalità dell'API V2 come metodi separati. Un esempio è TranslateArray.
* Metodo Speak: la funzionalità di sintesi vocale non è più supportata nell'API di Microsoft Translator. La funzionalità di sintesi vocale è disponibile nell'API Riconoscimento vocale Bing dei Servizi cognitivi di Microsoft Azure.

L'elenco di metodi delle versioni 2 e 3 seguente individua le API e i metodi della versione 3 che forniranno le funzionalità della versione 2.

| Metodo API V2   | Compatibilità API V3 |
|:----------- |:-------------|
| Translate     | Translate          |
| TranslateArray      | Translate          |
| GetLanguageNames      | Lingue          |
| GetLanguagesForTranslate     | Lingue        |
| GetLanguagesForSpeak      | Cognitive Services Speech API         |
| Speak     | Cognitive Services Speech API          |
| Rilevamento     | Rilevamento         |
| DetectArray     | Rilevamento         |
| AddTranslation     | Microsoft Translator HUB API         |
| AddTranslationArray    | Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | La funzionalità non è più supportata         |
| GetTranslationsArray      | La funzionalità non è più supportata         |

## <a name="move-to-json-format"></a>Passare al formato JSON

La Traduzione testuale Microsoft V2 ha accettato e restituito i dati in formato XML. Nella V3 tutti i dati inviati e ricevuti tramite l'API sono in formato JSON. La V3 non accetterà o restituirà più il formato XML. 

Tale modifica interesserà diversi aspetti di un'applicazione scritta per l'API Traduzione testuale della V2. Ad esempio: l'API Languages restituisce informazioni sulla lingua per i metodi di traduzione testuale, traslitterazione e di due dizionari. È possibile richiedere tutte le informazioni sulla lingua per tutti i metodi in un'unica chiamata o richiederle singolarmente.

Il metodo Languages non richiede l'autenticazione. Facendo clic sul collegamento seguente è possibile visualizzare tutte le informazioni sulla lingua per la V3 in formato JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chiave di autenticazione

La chiave di autenticazione in uso per la V2 verrà accettata per la V3. Non è necessario ottenere una nuova sottoscrizione. Sarà possibile combinare la V2 e la V3 nelle app in tutto il periodo di migrazione, semplificando il rilascio di nuove versioni durante la migrazione dal formato XML della V2 al formato JSON della V3.

## <a name="pricing-model"></a>Modello di prezzi

Microsoft Translator V3 ha lo stesso prezzo della V2; per ogni carattere, inclusi gli spazi. Le nuove funzionalità della V3 apportano alcune modifiche al conteggio dei caratteri per la fatturazione.

| Metodo V3   | Caratteri conteggiati per la fatturazione |
|:----------- |:-------------|
| Lingue     | Nessun carattere inviato, nessuno conteggiato, nessun addebito.          |
| Translate     | Il conteggio si basa sul numero di caratteri inviato per la traduzione e sul numero delle lingue in cui vengono tradotti i caratteri. 50 caratteri inviati e 5 lingue richieste faranno 50 x 5.           |
| Transliterate     | Viene conteggiato il numero di caratteri inviati per la traslitterazione.         |
| Dictionary lookup & example     | Viene conteggiato il numero di caratteri inviato per la ricerca nel dizionario e gli esempi.         |
| BreakSentence     | Nessun addebito.       |
| Rilevamento     | Nessun addebito.      |

## <a name="v3-end-points"></a>Obiettivi della V3

Globale

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Metodi delle traduzioni testuali dell'API V3

[Linguaggi](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Personalizzazione

Microsoft Translator V3 usa la traduzione neurale automatica per impostazione predefinita. Di conseguenza, non può essere usato con Hub di Microsoft Translator, che supporta la traduzione automatica statistica legacy. La personalizzazione per la traduzione neurale è ora disponibile usando Custom Translator (traduttore personalizzato). [Altre informazioni sulla personalizzazione della traduzione automatica neurale](customization.md)

La traduzione neurale con l'API testuale della V3 non supporta l'uso delle categorie standard (smt, voce, testo, generalnn).


## <a name="links"></a>Collegamenti

* [Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informazioni legali su Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Condizioni per i servizi online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizza la documentazione sulla V3.0](reference/v3-0-reference.md)
