---
title: Metodo Dictionary Lookup dell'API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Usare il metodo Dictionary Lookup dell'API Traduzione testuale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 8cc37c9edd5679158f8454e00bd27a53e4491004
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357704"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>API Traduzione testuale 3.0: Ricerca nel dizionario

Specifica le traduzioni alternative per una parola e un numero ridotto di frasi idiomatiche. Ogni traduzione specifica la parte del discorso e include un elenco di traduzioni inverse. Le traduzioni inverse consentono all'utente di capire la traduzione nel contesto. L'operazione [Esempio di dizionario](./v3-0-dictionary-examples.md) permette di fare un'analisi più approfondita per vedere esempi di usi di ogni traduzione.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

<table width="100%">
  <th width="20%">Query parameter (Parametro di query)</th>
  <th>Descrizione</th>
  <tr>
    <td>api-version</td>
    <td>*Parametro obbligatorio*.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Parametro obbligatorio*.<br/>Specifica la lingua del testo di input. La lingua di origine deve essere una delle [lingue supportate](./v3-0-languages.md) incluse nell'ambito `dictionary`.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*Parametro obbligatorio*.<br/>Specifica la lingua del testo di output. La lingua di destinazione deve essere una delle [lingue supportate](./v3-0-languages.md) incluse nell'ambito `dictionary`.</td>
  </tr>
</table>

Le intestazioni della richiesta includono:

<table width="100%">
  <th width="20%">Headers</th>
  <th>Descrizione</th>
  <tr>
    <td>Intestazioni di autenticazione</td>
    <td><em>Intestazione della richiesta obbligatoria</em>.<br/>Vedere le <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">opzioni disponibili per l'autenticazione</a>.</td>
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
    <td>*Facoltativo*.<br/>GUID generato dal client che identifica in modo univoco la richiesta. È possibile omettere questa intestazione se nella stringa della query si include l'ID traccia usando un parametro di query denominato `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento della matrice è un oggetto JSON con una proprietà di stringa denominata `Text`, che rappresenta il termine da cercare.

```json
[
    {"Text":"fly"}
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 10 elementi.
* Il valore di testo di un elemento della matrice non può superare 100 caratteri spazi inclusi.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni stringa nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `normalizedSource`: stringa che fornisce la forma normalizzata del termine di origine. Ad esempio, se la richiesta è "JOHN", la forma normalizzata sarà "john". Il contenuto di questo campo diventa l'input per gli [esempi di ricerca](./v3-0-dictionary-examples.md).
    
  * `displaySource`: stringa che fornisce il termine di origine in un formato adatto per la visualizzazione da parte dell'utente finale. Se, ad esempio, l'input è "JOHN", il formato di visualizzazione rifletterà la consueta ortografia del nome: "John". 

  * `translations`: elenco di traduzioni per il termine di origine. Ogni elemento dell'elenco è un oggetto con le proprietà seguenti:

    * `normalizedTarget`: stringa che fornisce la forma normalizzata del termine nella lingua di destinazione. Questo valore deve essere usato come input per gli [esempi di ricerca](./v3-0-dictionary-examples.md).

    * `displayTarget`: stringa che fornisce il termine nella lingua di destinazione e in un formato adatto per la visualizzazione da parte dell'utente finale. In genere, differisce da `normalizedTarget` solo in termini di uso di maiuscole/minuscole. Ad esempio, un nome proprio come "Juan" avrà `normalizedTarget = "juan"` e `displayTarget = "Juan"`.

    * `posTag`: stringa che associa il termine a un tag che identifica la parte del discorso.

        | Nome del tag | Descrizione  |
        |----------|--------------|
        | ADJ      | Aggettivi   |
        | ADV      | Avverbi      |
        | CONJ     | Congiunzioni |
        | DET      | Determinanti  |
        | MODAL    | Verbi        |
        | NOUN     | Nomi        |
        | PREP     | Preposizioni |
        | PRON     | Pronomi     |
        | VERB     | Verbi        |
        | OTHER    | Altri        |

        Come nota per l'implementazione, questi tag sono stati determinati in base alla parte del discorso che definisce la categoria in inglese e poi prendendo il tag più frequente per ogni coppia di origine/destinazione. Pertanto, se gli utenti spesso traducono una parola spagnola nel tag di una parte del discorso diversa in inglese, si potrebbe rilevare che i tag contengono errori rispetto al termine spagnolo.

    * `confidence`: valore compreso tra 0,0 e 1,0 che rappresenta l'"attendibilità" (o, più precisamente, la "probabilità nei dati di training") della coppia di traduzione. La somma dei punteggi di attendibilità per un termine di origine potrebbero sommarsi a 1,0 oppure no. 

    * `prefixWord`: stringa che fornisce il termine da visualizzare come prefisso della traduzione. Attualmente, è il determinante che include il genere dei nomi, nelle lingue in cui esistono tali valori. Ad esempio, il prefisso della parola spagnola "mosca" è "la", poiché in spagnolo "mosca" è un sostantivo femminile. Dipende solo dalla traduzione, non dal termine di origine. Se non esiste un prefisso, la stringa è vuota.
    
    * `backTranslations`: elenco di "traduzioni inverse" del termine di destinazione. Ad esempio, i termini di origine con cui è possibile tradurre quelli di destinazione. È garantito che l'elenco contenga il termine di origine che è stato richiesto (ad esempio, se il termine di origine che viene cercato è "fly", allora è garantito che "fly" si troverà nell'elenco `backTranslations`). Tuttavia, non è garantito che si trovi nella prima posizione, infatti spesso non sarà così. Ogni elemento dell'elenco `backTranslations` è un oggetto descritto dalle proprietà seguenti:

        * `normalizedText`: stringa che fornisce la forma normalizzata del termine di origine costituita da una traduzione inversa del termine di destinazione. Questo valore deve essere usato come input per gli [esempi di ricerca](./v3-0-dictionary-examples.md).        

        * `displayText`: stringa che fornisce il termine di origine sotto forma di traduzione inversa del termine di destinazione in un formato adatto per la visualizzazione da parte dell'utente finale.

        * `numExamples`: numero intero che rappresenta la quantità di esempi disponibili per questa coppia di traduzione. Gli esempi effettivi devono essere recuperati con una chiamata separata agli [esempi di ricerca](./v3-0-dictionary-examples.md). Il numero è principalmente destinato a facilitare la visualizzazione nell'esperienza utente. Ad esempio, un'interfaccia utente può aggiungere un collegamento ipertestuale per la traduzione inversa se la quantità di esempi è maggiore di zero e mostrare la traduzione inversa come testo normale se non sono disponibili esempi. Si noti che la quantità effettiva di esempi restituita da una chiamata agli [esempi di ricerca](./v3-0-dictionary-examples.md) può essere inferiore a `numExamples`, poiché altre opzioni di filtro possono essere applicate in tempo reale per rimuovere gli esempi "errati".
        
        * `frequencyCount`: numero intero che rappresenta la frequenza di questa coppia di traduzione nei dati. Lo scopo principale di questo campo è fornire un'interfaccia utente con un mezzo per ordinare le traduzioni inverse in modo che vengano visualizzati per primi i termini più frequenti.

    > [!NOTE]
    > Se il termine cercato non esiste nel dizionario, la risposta è 200 (OK) ma l'elenco `translations` è vuoto.

## <a name="examples"></a>Esempi

Questo esempio illustra come eseguire la ricerca di traduzioni spagnole alternative per il termine inglese `fly`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Il corpo della risposta (abbreviato per maggiore chiarezza) è:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Questo esempio illustra cosa succede quando il termine cercato non esiste per la coppia di dizionario valida.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Poiché il termine non viene trovato nel dizionario, il corpo della risposta include un elenco `translations` vuoto.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
