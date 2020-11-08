---
title: Eseguire la migrazione a V3-Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra i passaggi per eseguire la migrazione da V2 a V3 del traduttore di servizi cognitivi di Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: f22213bf192b12e046bab12346e5d5d83fb36b96
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364276"
---
# <a name="translator-v2-to-v3-migration"></a>Migrazione da convertitore V2 a V3

> [!NOTE]
> V2 è stato deprecato il 30 aprile 2018. Eseguire la migrazione delle applicazioni a V3 per sfruttare i vantaggi delle nuove funzionalità disponibili esclusivamente in V3.
> 
> L'Hub Microsoft Translator verrà ritirato il 17 maggio 2019. [Visualizzare le informazioni e le date importanti per la migrazione](https://www.microsoft.com/translator/business/hub/).  

Il team Microsoft Translator ha rilasciato la versione 3 (v3) del traduttore. Questa versione include nuove funzionalità, metodi deprecati e un nuovo formato per inviare e ricevere dati dal servizio Microsoft Translator. Questo documento fornisce informazioni per modificare le applicazioni da usare nella V3. 

Alla fine di questo documento sono riportati collegamenti utili per avere maggiori informazioni.

## <a name="summary-of-features"></a>Riepilogo delle funzionalità

* Senza tracciabilità: nella V3 la funzione Senza tracciabilità si applica a tutti i piani tariffari nel portale di Azure. Questa funzionalità indica che nessun testo inviato all'API V3 verrà salvato da Microsoft.
* JSON: XML viene sostituito da JSON. Tutti i dati inviati al servizio e ricevuti dal servizio sono in formato JSON.
* Più lingue di destinazione in una singola richiesta: il metodo translate accetta più lingue ' to ' per la conversione in un'unica richiesta. Una singola richiesta, ad esempio, può essere ' from ' English and ' to ' German, spagnolo, giapponese o qualsiasi altro gruppo di lingue.
* Dizionario bilingue: all'API è stato aggiunto un metodo "Bilingual dictionary". Questo metodo include ' Lookup ' ed ' Examples '.
* Traslitterazione: all'API è stato aggiunto un metodo "Transliterate". Questo metodo convertirà parole e frasi in un unico script (ad esempio in arabo) in un altro script (ad esempio in latino).
* Lingue: un nuovo metodo "languages" fornisce informazioni sulla lingua, in formato JSON, da usare con i metodi ' translate ',' Dictionary ' è transliterate '.
* Novità di translate: nuove funzionalità sono state aggiunte al metodo ' translate ' per supportare alcune delle funzionalità incluse nell'API v2 come metodi distinti. Un esempio è TranslateArray.
* Pronunciare il metodo: la funzionalità di sintesi vocale non è più supportata in Microsoft Translator. La funzionalità di sintesi vocale è disponibile nel [servizio Voce Microsoft](../speech-service/text-to-speech.md).

L'elenco di metodi delle versioni 2 e 3 seguente individua le API e i metodi della versione 3 che forniranno le funzionalità della versione 2.

| Metodo API V2   | Compatibilità API V3 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Linguaggi](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Linguaggi](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Servizio Voce Microsoft](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Servizio Voce Microsoft](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | La funzionalità non è più supportata       |
| `AddTranslationArray`    | La funzionalità non è più supportata          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | La funzionalità non è più supportata         |
| `GetTranslationsArray`      | La funzionalità non è più supportata         |

## <a name="move-to-json-format"></a>Passare al formato JSON

Microsoft Translator Translation V2 ha accettato e restituito dati in formato XML. Nella V3 tutti i dati inviati e ricevuti tramite l'API sono in formato JSON. La V3 non accetterà o restituirà più il formato XML.

Tale modifica interesserà diversi aspetti di un'applicazione scritta per l'API Traduzione testuale della V2. Ad esempio, l'API Languages restituisce informazioni sulla lingua per i metodi di traduzione testuale, traslitterazione e i due metodi del dizionario. È possibile richiedere tutte le informazioni sulla lingua per tutti i metodi in un'unica chiamata o richiederle singolarmente.

Il metodo Languages non richiede l'autenticazione. Facendo clic sul collegamento seguente è possibile visualizzare tutte le informazioni sulla lingua per la V3 in formato JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, dictionary, traslitterazione](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chiave di autenticazione

La chiave di autenticazione in uso per la V2 verrà accettata per la V3. Non è necessario ottenere una nuova sottoscrizione. Sarà possibile combinare la V2 e la V3 nelle app in tutto il periodo di migrazione, semplificando il rilascio di nuove versioni durante la migrazione dal formato XML della V2 al formato JSON della V3.

## <a name="pricing-model"></a>Modello di prezzi

Microsoft Translator V3 ha lo stesso prezzo della V2; per ogni carattere, inclusi gli spazi. Le nuove funzionalità della V3 apportano alcune modifiche al conteggio dei caratteri per la fatturazione.

| Metodo V3   | Caratteri conteggiati per la fatturazione |
|:----------- |:-------------|
| `Languages`     | Nessun carattere inviato, nessuno conteggiato, nessun addebito.          |
| `Translate`     | Il conteggio si basa sul numero di caratteri inviato per la traduzione e sul numero delle lingue in cui vengono tradotti i caratteri. 50 caratteri inviati e 5 lingue richieste faranno 50 x 5.           |
| `Transliterate`     | Viene conteggiato il numero di caratteri inviati per la traslitterazione.         |
| `Dictionary lookup & example`     | Viene conteggiato il numero di caratteri inviato per la ricerca nel dizionario e gli esempi.         |
| `BreakSentence`     | Nessun addebito.       |
| `Detect`     | Nessun addebito.      |

## <a name="v3-end-points"></a>Obiettivi della V3

Globale

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metodi delle traduzioni testuali dell'API V3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibilità e personalizzazione

> [!NOTE]
> 
> L'Hub Microsoft Translator verrà ritirato il 17 maggio 2019. [Visualizzare le informazioni e le date importanti per la migrazione](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 usa la traduzione neurale automatica per impostazione predefinita. Di conseguenza, non può essere usato con l'hub di Microsoft Translator, che supporta solo la traduzione automatica statistica legacy. La personalizzazione per la traduzione neurale è ora disponibile usando Custom Translator (traduttore personalizzato). [Altre informazioni sulla personalizzazione della traduzione automatica neurale](custom-translator/overview.md)

La traduzione neurale con l'API testuale V3 non supporta l'uso delle categorie standard (SMT, voce, tecnologia, generalnn).

| Versione | Endpoint | Conformità al processore GDPR | Usare l'Hub di Translator | Usare il traduttore personalizzato (anteprima) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Traduttore versione 2|    api.microsofttranslator.com|    No    |Sì    |No|
|Translator versione 3|    api.cognitive.microsofttranslator.com|    Sì|    No|    Sì|

**Translator versione 3**
* È disponibile a livello generale e completamente supportata.
* È un processore conforme al GDPR e soddisfa tutti i requisiti ISO 20001 e 20018 nonché i requisiti della certificazione SOC 3. 
* Consente di richiamare i sistemi di traduzione di rete neurale che sono stati personalizzati con il traduttore personalizzato (anteprima), la nuova funzionalità di personalizzazione di Translator NMT. 
* Non fornisce l'accesso ai sistemi di traduzione personalizzati creati tramite l'Hub di Microsoft Translator.

Si sta usando la versione 3 di Translator se si usa l'endpoint api.cognitive.microsofttranslator.com.

**Traduttore versione 2**
* Non soddisfa tutti i requisiti di certificazione 20001,20018 ISO e SOC 3. 
* Non consente di richiamare i sistemi di traduzione di rete neurale che sono stati personalizzati con la funzionalità di personalizzazione di Translator.
* Fornisce l'accesso ai sistemi di traduzione personalizzati creati tramite l'Hub di Microsoft Translator.
* Si usa la versione 2 del convertitore se si usa l'endpoint api.microsofttranslator.com.

Nessuna versione di Translator crea un record delle traduzioni. Le traduzioni non vengono mai condivise con altri. Altre informazioni sulla pagina Web [Nessuna traccia relativa a Translator](https://www.aka.ms/NoTrace).

## <a name="links"></a>Collegamenti

* [Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informazioni legali su Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Condizioni per i servizi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizza la documentazione sulla V3.0](reference/v3-0-reference.md)