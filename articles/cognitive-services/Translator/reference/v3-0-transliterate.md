---
title: Metodo di translitterazione dell'API Traduzione testuale Microsoft | Microsoft Docs
description: Usare il metodo di translitterazione dell'API Traduzione testuale Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377521"
---
# <a name="text-api-30-transliterate"></a>API per testo 3.0: traslitterare

Converte il testo in una lingua da uno script in un altro script.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri di richiesta passati alla stringa di query sono:

<table width="100%">
  <th width="20%">Query parameter (Parametro di query)</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>api-version</td>
    <td>*Parametro obbligatorio*.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`.</td>
  </tr>
  <tr>
    <td>Linguaggio</td>
    <td>*Parametro obbligatorio*.<br/>Specifica la lingua del testo da convertire da uno script a un altro. Le lingue possibili sono elencate nell'ambito `transliteration` ottenuto a seguito di una query al servizio per le [lingue supportate](.\v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parametro obbligatorio*.<br/>Specifica lo script usato dal testo di input. Ricercare le [lingue supportate](.\v3-0-languages.md) usando l'ambito `transliteration` per trovare script di input disponibili per la lingua selezionata.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parametro obbligatorio*.<br/>Specifica lo script di output. Ricercare le [lingue supportate](.\v3-0-languages.md) usando l'ambito `transliteration` per trovare script di output disponibili per la combinazione selezionata di lingua e di script di input.</td>
  </tr>
</table> 

Le intestazioni della richiesta includono:

<table width="100%">
  <th width="20%">Headers</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>_Un'intestazione_<br/>_di autorizzazione_</td>
    <td>*Intestazione di richiesta necessaria*.<br/>Vedere le [opzioni disponibili per l'autenticazione](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Intestazione di richiesta necessaria*.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:`application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Intestazione di richiesta necessaria*.<br/>Lunghezza del corpo della richiesta.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Facoltativo*.<br/>GUID generato dal client che identifica in modo univoco la richiesta. Si noti che è possibile omettere questa intestazione se nella stringa della query si include l'ID traccia usando un parametro di query denominato `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento di matrice è un oggetto JSON con una proprietà di stringa denominata `Text`, che rappresenta la stringa da convertire.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 10 elementi.
* Il valore di testo di un elemento di matrice non può superare 1.000 caratteri inclusi gli spazi.
* L'intero testo incluso nella richiesta non può superare 5.000 caratteri inclusi gli spazi.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni elemento nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `text`: una stringa che è il risultato della conversione della stringa di input nello script di output.
  
  * `script`: una stringa che specifica lo script usato nell'output.

Una risposta di esempio JSON è:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Intestazioni della risposta

<table width="100%">
  <th width="20%">Headers</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Usato per la risoluzione dei problemi.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

I seguenti sono i possibili codici di stato HTTP che possono essere restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>200</td>
    <td>Completamento della procedura.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Uno dei parametri della query è mancante o non valido. Correggere i parametri di richiesta prima di riprovare.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Impossibile autenticare la richiesta. Verificare che le credenziali siano state specificate e che siano valide.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>La richiesta non è autorizzata. Controllare il messaggio di errore per i dettagli. Ciò spesso indica che tutte le traduzioni gratuite fornite con una sottoscrizione di prova sono state usate.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Il chiamante sta inviando un numero eccessivo di richieste.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Si è verificato un errore imprevisto. Se l'errore permane, segnalarlo con: data e ora dell'errore, identificativo della richiesta dall'intestazione di risposta `X-RequestId` e l'identificativo del client dall'intestazione di risposta `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server temporaneamente non disponibile. ripetere la richiesta. Se l'errore permane, segnalarlo con: data e ora dell'errore, identificativo della richiesta dall'intestazione di risposta `X-RequestId` e l'identificativo del client dall'intestazione di risposta `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Esempi

L'esempio seguente illustra come convertire due stringhe in lingua giapponese in giapponese romanizzato.

# <a name="curltabcurl"></a>[curl](#tab/curl)

Il payload JSON per la richiesta in questo esempio:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se si sta usando cUrl in una finestra della riga di comando che non supporta i caratteri Unicode, salvare il payload JSON seguente in un file denominato `request.txt`. Assicurarsi di salvare il file con la codifica `UTF-8`.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
