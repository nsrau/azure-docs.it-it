---
title: Metodo Languages dell'API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Usare il metodo Languages dell'API Traduzione testuale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 3dd875fbf9a8da464abe1b6312cec84c030dc624
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934006"
---
# <a name="translator-text-api-30-languages"></a>API Traduzione testuale 3.0: Lingue

Ottiene il set di lingue attualmente supportate da altre operazioni dell'API Traduzione testuale. 

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

<table width="100%">
  <th width="20%">Parametro di query</th>
  <th>Descrizione</th>
  <tr>
    <td>api-version</td>
    <td><em>Parametro obbligatorio</em>.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parametro facoltativo*.<br/>Elenco di nomi delimitato da virgole che definisce il gruppo di lingue da restituire. I nomi di gruppi consentiti sono: `translation`, `transliteration` e `dictionary`. Se non viene specificato un ambito, vengono restituiti tutti i gruppi come se venisse passato `scope=translation,transliteration,dictionary`. Per determinare il set di lingue supportate appropriato per uno scenario specifico, vedere la descrizione dell'[oggetto risposta](#response-body).</td>
  </tr>
</table> 

Le intestazioni della richiesta sono le seguenti:

<table width="100%">
  <th width="20%">Intestazioni</th>
  <th>Descrizione</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Intestazione di richiesta facoltativa*.<br/>Lingua da usare per le stringhe dell'interfaccia utente. Alcuni campi nella risposta sono nomi di lingue o di aree. Usare questo parametro per definire la lingua in cui verranno restituiti tali nomi. La lingua viene specificata fornendo un tag di lingua BCP 47 in formato corretto. Ad esempio, usare il valore `fr` per richiedere i nomi in francese oppure `zh-Hant` per richiedere i nomi in cinese tradizionale.<br/>Quando non è specificata una lingua di destinazione o non è disponibile la localizzazione, i nomi vengono forniti in lingua inglese.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Intestazione di richiesta facoltativa*.<br/>GUID generato dal client che identifica in modo univoco la richiesta.</td>
  </tr>
</table> 

Per recuperare le risorse di lingua non è necessaria l'autenticazione.

## <a name="response-body"></a>Corpo della risposta

Un client usa il parametro di query `scope` per definire i gruppi di lingue a cui è interessato.

* `scope=translation` fornisce le lingue supportate per la traduzione del testo da una lingua a un'altra.

* `scope=transliteration` fornisce le funzionalità per la conversione del testo in una lingua da un alfabeto a un altro.

* `scope=dictionary` fornisce le coppie di lingue per cui le operazioni `Dictionary` restituiscono dati.

Un client può recuperare diversi gruppi contemporaneamente specificando un elenco di nomi delimitato da virgole. `scope=translation,transliteration,dictionary`, ad esempio, restituirà le lingue supportate per tutti i gruppi.

Una risposta corretta è un oggetto JSON con una proprietà per ogni gruppo richiesto:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Di seguito è riportato il valore di ogni proprietà.

* Proprietà `translation`

  Il valore di `translation` è un dizionario di coppie (chiave, valore). Ogni chiave è un tag di lingua BCP 47. Una chiave identifica una lingua da o verso la quale può essere tradotto il testo. Il valore associato alla chiave è un oggetto JSON con le proprietà che descrivono la lingua.

  * `name`: nome visualizzato della lingua nelle impostazioni locali richieste tramite l'intestazione `Accept-Language`.

  * `nativeName`: nome visualizzato della lingua nelle relative impostazioni locali native.

  * `dir`: direzionalità, ovvero `rtl` per le lingue da destra a sinistra e `ltr` per le lingue da sinistra a destra.

  Di seguito è riportato un esempio:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* Proprietà `transliteration`

  Il valore di `transliteration` è un dizionario di coppie (chiave, valore). Ogni chiave è un tag di lingua BCP 47. Una chiave identifica un lingua per cui il testo può essere convertito da un alfabeto a un altro. Il valore associato alla chiave è un oggetto JSON con le proprietà che descrivono la lingua e gli alfabeti supportati.

  * `name`: nome visualizzato della lingua nelle impostazioni locali richieste tramite l'intestazione `Accept-Language`.

  * `nativeName`: nome visualizzato della lingua nelle relative impostazioni locali native.

  * `scripts`: elenco degli alfabeti da cui eseguire la conversione. Ogni elemento dell'elenco di `scripts` contiene le proprietà seguenti.

    * `code`: codice che identifica l'alfabeto.

    * `name`: nome visualizzato dell'alfabeto nelle impostazioni locali richieste tramite l'intestazione `Accept-Language`.

    * `nativeName`: nome visualizzato della lingua nelle relative impostazioni locali native.

    * `dir`: direzionalità, ovvero `rtl` per le lingue da destra a sinistra e `ltr` per le lingue da sinistra a destra.

    * `toScripts`: elenco degli alfabeti in cui è possibile convertire il testo. Ogni elemento dell'elenco di `toScripts` contiene le proprietà `code`, `name`, `nativeName` e `dir` descritte in precedenza.

  Di seguito è riportato un esempio:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* Proprietà `dictionary`

  Il valore di `dictionary` è un dizionario di coppie (chiave, valore). Ogni chiave è un tag di lingua BCP 47. La chiave identifica la lingua per cui sono disponibili traduzioni inverse e traduzioni alternative. Il valore è un oggetto JSON che descrive la lingua di origine e le lingue di destinazione con le traduzioni disponibili.

  * `name`: nome visualizzato della lingua di origine nelle impostazioni locali richieste tramite l'intestazione `Accept-Language`.

  * `nativeName`: nome visualizzato della lingua nelle relative impostazioni locali native.

  * `dir`: direzionalità, ovvero `rtl` per le lingue da destra a sinistra e `ltr` per le lingue da sinistra a destra.

  * `translations`: elenco delle lingue con traduzioni alternative ed esempi della query espressa nella lingua di origine. Ogni elemento dell'elenco di `translations` contiene le proprietà seguenti.

    * `name`: nome visualizzato della lingua di destinazione nelle impostazioni locali richieste tramite l'intestazione `Accept-Language`.

    * `nativeName`: nome visualizzato della lingua di destinazione nelle relative impostazioni locali native.

    * `dir`: direzionalità, ovvero `rtl` per le lingue da destra a sinistra e `ltr` per le lingue da sinistra a destra.
    
    * `code`: codice di lingua che identifica la lingua di destinazione.

  Di seguito è riportato un esempio:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

La struttura dell'oggetto risposta cambierà solo in caso di cambiamento della versione dell'API. Per la stessa versione dell'API, l'elenco delle lingue disponibili può variare nel tempo perché l'elenco delle lingue supportate dai servizi di Microsoft Translator viene continuamente esteso.

L'elenco delle lingue supportate non subirà spesso modifiche. Per risparmiare larghezza di banda e migliorare la velocità di risposta, per un'applicazione client può essere opportuno memorizzare nella cache le risorse di lingua e il tag di entità corrispondente (`ETag`). L'applicazione client potrà quindi eseguire periodicamente query sul servizio, ad esempio ogni 24 ore, per recuperare il set di lingue supportate più recente. Passando il valore corrente di `ETag` in un campo dell'intestazione `If-None-Match` si consentirà al servizio di ottimizzare la risposta. Se la risorsa non è stata modificata, il servizio restituirà il codice di stato 304 e un corpo della risposta vuoto.

## <a name="response-headers"></a>Intestazioni della risposta

<table width="100%">
  <th width="20%">Intestazioni</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>ETag</td>
    <td>Valore corrente del tag di entità per i gruppi di lingue supportate richiesti. Per rendere più efficienti le richieste successive, il client può inviare il valore di `ETag` in un campo dell'intestazione `If-None-Match`.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Viene usato per la risoluzione dei problemi.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>Descrizione</th>
  <tr>
    <td>200</td>
    <td>Riuscite.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>La risorsa non è stata modificata dopo la versione specificata dalle intestazioni `If-None-Match` della richiesta.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Uno dei parametri di query manca o non è valido. Prima di riprovare, correggere i parametri della richiesta.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Il server è temporaneamente non disponibile. ripetere la richiesta. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
</table> 

Se si verifica un errore, la richiesta restituirà anche una risposta di errore JSON. Il codice errore è un numero a 6 cifre che combina il codice di stato HTTP a 3 cifre seguito da un numero a 3 cifre per classificare ulteriormente l'errore. I codici di errore più comuni sono reperibili nella [pagina di riferimento API Traduzione testuale V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Esempi

L'esempio seguente mostra come recuperare le lingue supportate per la traduzione del testo.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
