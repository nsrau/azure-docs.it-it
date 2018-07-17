---
title: Metodo BreakSentence dell'API Traduzione testuale Microsoft | Microsoft Docs
description: Usare il metodo BreakSentence dell'API Traduzione testuale Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 8ce6644d21b397ea0e7f2e71e3c3a5a96638eec5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377537"
---
# <a name="text-api-30-breaksentence"></a>API Traduzione testuale 3.0: BreakSentence

Identifica il posizionamento dei delimitatori di frase in una porzione di testo.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta passati nella stringa di query sono i seguenti:

<table width="100%">
  <th width="20%">Query parameter (Parametro di query)</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>api-version</td>
    <td>*Parametro di query obbligatorio*.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`.</td>
  </tr>
  <tr>
    <td>Linguaggio</td>
    <td>*Parametro di query facoltativo*.<br/>Tag di lingua che identifica la lingua del testo di input. Se non viene specificato un codice, verrà applicato il rilevamento automatico della lingua.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*Parametro di query facoltativo*.<br/>Tag dell'alfabeto che identifica i caratteri usati dal testo di input. Se non viene specificato un alfabeto, verranno usati i caratteri predefiniti della lingua.</td>
  </tr>
</table> 

Le intestazioni della richiesta includono:

<table width="100%">
  <th width="20%">Headers</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>_Intestazione_<br/>_di autorizzazione_</td>
    <td>*Intestazione della richiesta obbligatoria*.<br/>Vedere le [opzioni disponibili per l'autenticazione](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Intestazione della richiesta obbligatoria*.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:`application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Intestazione della richiesta obbligatoria*.<br/>Lunghezza del corpo della richiesta.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Facoltativo*.<br/>GUID generato dal client che identifica in modo univoco la richiesta. Si noti che è possibile omettere questa intestazione se nella stringa della query si include l'ID traccia usando un parametro di query denominato `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento della matrice è un oggetto JSON con una proprietà stringa denominata `Text`. I delimitatori di frase vengono calcolati per il valore della proprietà `Text`. Di seguito è riportato un esempio di corpo della richiesta con una porzione di testo:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 100 elementi.
* Il valore di testo di un elemento di matrice non può superare 10.000 caratteri inclusi gli spazi.
* L'intero testo incluso nella richiesta non può superare 50.000 caratteri inclusi gli spazi.
* Se viene specificato il parametro di query `language`, tutti gli elementi di matrice devono essere nella stessa lingua. In caso contrario, verrà applicato il rilevamento automatico della lingua a ogni elemento della matrice in modo indipendente.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni stringa nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `sentLen`: matrice di interi che rappresenta le lunghezze delle frasi nell'elemento di testo. La lunghezza della matrice è il numero di frasi e i valori sono la lunghezza di ogni frase. 

  * `detectedLanguage`: oggetto che descrive la lingua rilevata tramite le proprietà seguenti:

     * `language`: codice della lingua rilevata.

     * `score`: valore float che indica il livello di attendibilità del risultato. Il punteggio è compreso tra zero e uno e un punteggio basso indica un'attendibilità bassa.
     
    Si noti che la proprietà `detectedLanguage` è presente nell'oggetto risultato solo quando viene richiesto il rilevamento automatico della lingua.

Una risposta JSON di esempio è:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Intestazioni della risposta

<table width="100%">
  <th width="20%">Headers</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Viene usato per la risoluzione dei problemi.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>DESCRIZIONE</th>
  <tr>
    <td>200</td>
    <td>Completamento della procedura.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Uno dei parametri di query è mancante o non valido. Prima di riprovare, correggere i parametri della richiesta.</td>
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
    <td>Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della risposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Il server è temporaneamente non disponibile. ripetere la richiesta. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della risposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Esempi

L'esempio seguente mostra come ottenere i delimitatori di frase per una singola frase. La lingua della frase viene rilevata automaticamente dal servizio.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

