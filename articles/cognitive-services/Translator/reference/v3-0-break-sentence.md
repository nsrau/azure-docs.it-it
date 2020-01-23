---
title: Metodo BreakSentence dell'API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Il API Traduzione testuale metodo BreakSentence identifica il posizionamento dei limiti di frase in una porzione di testo.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548119"
---
# <a name="translator-text-api-30-breaksentence"></a>API Traduzione testuale 3.0: BreakSentence

Identifica il posizionamento dei delimitatori di frase in una porzione di testo.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

| Parametro della query | Description |
| -------| ----------- |
| api-version <img width=200/>   | **Parametro di query obbligatorio**.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`. |
| Linguaggio | **Parametro di query facoltativo**.<br/>Tag di lingua che identifica la lingua del testo di input. Se non viene specificato un codice, verrà applicato il rilevamento automatico della lingua. |
| script    | **Parametro di query facoltativo**.<br/>Tag dell'alfabeto che identifica i caratteri usati dal testo di input. Se non viene specificato un alfabeto, verranno usati i caratteri predefiniti della lingua.  | 

Le intestazioni della richiesta includono:

| Headers | Description |
| ------- | ----------- |
| Intestazione/e di autenticazione <img width=200/>  | **Intestazione della richiesta obbligatoria**.<br/>Vedere le <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">opzioni disponibili per l'autenticazione</a>. |
| Content-Type | **Intestazione della richiesta obbligatoria**.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:`application/json`. |
| Content-Length    | **Intestazione della richiesta obbligatoria**.<br/>Lunghezza del corpo della richiesta.  | 
| X-ClientTraceId   | **Facoltativo**.<br/>GUID generato dal client che identifica in modo univoco la richiesta. Si noti che è possibile omettere questa intestazione se nella stringa della query si include l'ID traccia con un parametro di query denominato `ClientTraceId`.  | 

## <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento della matrice è un oggetto JSON con una proprietà stringa denominata `Text`. I delimitatori di frase vengono calcolati per il valore della proprietà `Text`. Di seguito è riportato un esempio di corpo della richiesta con una porzione di testo:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 100 elementi.
* Il valore di testo di un elemento della matrice non può superare 10.000 caratteri spazi inclusi.
* L'intero testo incluso nella richiesta non può superare 50.000 caratteri spazi inclusi.
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
    "sentLen": [ 13, 11, 22 ]
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
  <th>Description</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Viene usato per la risoluzione dei problemi.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Esito positivo.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Uno dei parametri di query manca o non è valido. Prima di riprovare, correggere i parametri della richiesta.</td>
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

L'esempio seguente mostra come ottenere i delimitatori di frase per una singola frase. La lingua della frase viene rilevata automaticamente dal servizio.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

