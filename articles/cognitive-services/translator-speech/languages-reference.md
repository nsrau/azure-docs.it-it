---
title: Metodo per le lingue dell'API Traduzione vocale
titleSuffix: Azure Cognitive Services
description: Usare il metodo per le lingue dell'API Traduzione vocale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: b7005811898df9132be6bc199e26f6c6dc358618
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345204"
---
# <a name="translator-speech-api-languages"></a>API Traduzione vocale: lingue

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Traduzione vocale espande continuamente l'elenco delle lingue supportate dai suoi servizi. Usare quest'API per individuare il set di lingue attualmente disponibili per l'uso con il servizio Traduzione vocale.

Nel [sito GitHub di Microsoft Translator](https://github.com/MicrosoftTranslator) è possibile trovare esempi di codice che illustrano l'uso dell'API per ottenere le lingue disponibili.

## <a name="implementation-notes"></a>Note sull'implementazione

GET /languages 

È disponibile una vasta gamma di lingue per la trascrizione di contenuti vocali, la traduzione del testo trascritto e la produzione di un output vocale sintetizzato della traslazione.

Un client usa il parametro di query `scope` per definire i set di lingue desiderati.

* **Conversione della voce in testo scritto:** usare il parametro di query `scope=speech` per recuperare il set di lingue disponibili per la trascrizione di contenuti vocali.
* **Traduzione testuale:** usare il parametro di query `scope=text` per recuperare il set di lingue disponibili per la traduzione del testo trascritto.
* **Sintesi vocale:** usare il parametro di query `scope=tts` per recuperare il set di lingue e voci disponibili per sintetizzare il testo tradotto di nuovo in output vocale.

Un client può recuperare più set contemporaneamente specificando un elenco di opzioni delimitato da virgole. Ad esempio: `scope=speech,text,tts`.

Una risposta corretta è un oggetto JSON con una proprietà per ogni set richiesto.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Poiché un client può usare il parametro di query `scope` per selezionare i set di lingue supportate da restituire, una risposta effettiva può includere solo un subset di tutte le proprietà illustrate in precedenza.

Di seguito è indicato il valore fornito con ogni proprietà.

### <a name="speech-to-text-speech"></a>Conversione della voce in testo scritto (speech)

Il valore associato alla proprietà di conversione della voce in testo scritto, `speech`, è un dizionario di coppie (chiave, valore). Ogni chiave identifica una lingua supportata per la conversione della voce in testo scritto. La chiave è l'identificatore che il client passa all'API. Il valore associato alla chiave è un oggetto con le proprietà seguenti:

* `name`: nome visualizzato della lingua.
* `language`: tag di lingua della lingua scritta associata. Vedere "Traduzione testuale", più avanti.
Di seguito è riportato un esempio:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Traduzione testuale (text)

Il valore associato alla proprietà `text` è anch'esso un dizionario in cui ogni chiave identifica una lingua supportata per la traduzione testuale. Il valore associato alla chiave descrive la lingua:

* `name`: nome visualizzato della lingua.
* `dir`: direzionalità, che è `rtl` per le lingue da destra a sinistra e `ltr` per le lingue da sinistra a destra.

Di seguito è riportato un esempio:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Sintesi vocale (tts)

Il valore associato alla proprietà di sintesi vocale, tts, è anch'esso un dizionario in cui ogni chiave identifica una voce supportata. Gli attributi di un oggetto voce sono i seguenti:

* `displayName`: nome visualizzato per la voce.
* `gender`: genere della voce (maschio o femmina).
* `locale`: tag di lingua della voce con sottotag per la lingua principale e per l'area.
* `language`: tag di lingua della lingua scritta associata.
* `languageName`: nome visualizzato della lingua.
* `regionName`: nome visualizzato dell'area per la lingua.

Di seguito è riportato un esempio:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Localizzazione
Il servizio restituisce tutti i nomi nella lingua dell'intestazione "Accept-Language", per tutte le lingue supportate nella traduzione testuale.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Oggetto che descrive il set di lingue supportate.

Valore di esempio del modello: 

Langagues { speech (oggetto, facoltativo), text (oggetto, facoltativo), tts (oggetto, facoltativo) }

### <a name="headers"></a>Headers

|Intestazione|DESCRIZIONE|type|
:--|:--|:--|
X-RequestId|Valore generato dal server per identificare la richiesta e usato per la risoluzione dei problemi.|stringa|

### <a name="parameters"></a>Parametri

|Parametro|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|
|api-version    |Versione dell'API richiesta dal client. I valori consentiti sono: `1.0`.|query|stringa|
|scope  |Set di lingue o voci supportate da restituire al client. Questo parametro viene specificato come elenco di parole chiave delimitato da virgole. Sono disponibili le parole chiave seguenti:<ul><li>`speech`: fornisce il set di lingue supportate per la trascrizione di contenuti vocali.</li><li>`tts`: fornisce il set di voci supportato per la sintesi vocale.</li><li>`text`: fornisce il set di lingue supportate per la traduzione di testo.</li></ul>Se non viene specificato un valore, il valore predefinito di `scope` è `text`.|query|stringa|
|X-ClientTraceId    |GUID generato dal client per tenere traccia di una richiesta. Per semplificare la risoluzione dei problemi, i client devono fornire un nuovo valore con ogni richiesta e registrarlo.|intestazione|stringa|
|Accept-Language    |Alcuni campi nella risposta sono nomi di lingue o di aree. Usare questo parametro per definire la lingua in cui vengono restituiti i nomi. La lingua viene specificata tramite un tag di lingua BCP 47 ben formato. Selezionare un tag nell'elenco di identificatori di lingua restituito con l'ambito `text`. Per le lingue non supportate, i nomi vengono forniti in lingua inglese.<br/>Usare ad esempio il valore `fr` per richiedere i nomi in francese oppure `zh-Hant` per richiedere i nomi in cinese tradizionale.|intestazione|stringa|
    
### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input per verificare che siano validi. L'oggetto della risposta include una descrizione più dettagliata dell'errore.|
|429|Numero eccessivo di richieste.|
|500|Si è verificato un errore. Se l'errore persiste, segnalarlo con l'identificatore di traccia client (X-ClientTraceId) o l'identificatore della richiesta (X-RequestId).|
|503|Il server è temporaneamente non disponibile. Si prega di ripetere la richiesta. Se l'errore persiste, segnalarlo con l'identificatore di traccia client (X-ClientTraceId) o l'identificatore della richiesta (X-RequestId).|
