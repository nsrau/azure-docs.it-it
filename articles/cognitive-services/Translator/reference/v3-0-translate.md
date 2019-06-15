---
title: Metodo Translate dell'API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Usare il metodo Translate dell'API Traduzione testuale.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: be61d8932288b9a6b2cc96e53d3630124ec0f610
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389759"
---
# <a name="translator-text-api-30-translate"></a>API Traduzione testuale 3.0: Translate

Traduce il testo.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

<table width="100%">
  <th width="20%">Query parameter (Parametro di query)</th>
  <th>Descrizione</th>
  <tr>
    <td>api-version</td>
    <td><em>Parametro obbligatorio</em>.<br/>Versione dell'API richiesta dal client. Il valore deve essere <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>from</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica la lingua del testo di input. Trovare quali lingue sono disponibili per la traduzione eseguendo una ricerca tra le <a href="./v3-0-languages.md">lingue supportate</a> con l'ambito <code>translation</code>. Se non si specifica il parametro <code>from</code>, viene applicato il rilevamento automatico della lingua per determinare la lingua di origine.</td>
  </tr>
  <tr>
    <td>to</td>
    <td><em>Parametro obbligatorio</em>.<br/>Specifica la lingua del testo di output. La lingua di destinazione deve essere una delle <a href="./v3-0-languages.md">lingue supportate</a> incluse nell'ambito <code>translation</code>. Ad esempio, usare <code>to=de</code> per la traduzione in tedesco.<br/>È possibile tradurre in più lingue contemporaneamente ripetendo il parametro nella stringa di query. Ad esempio, usare <code>to=de&to=it</code> per la traduzione in tedesco e in italiano.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Parametro facoltativo</em>.<br/>Definisce se il testo tradotto è testo normale o testo HTML. Qualsiasi codice HTML deve essere un elemento completo ben formato. I valori possibili sono: <code>plain</code> (impostazione predefinita) o <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Parametro facoltativo</em>.<br/>Stringa che specifica la categoria (dominio) della traduzione. Questo parametro viene usato per ottenere le traduzioni da un sistema personalizzato compilato con <a href="../customization.md">Custom Translator</a>. Aggiungere l'ID della categoria dal convertitore personalizzato <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">dettagli progetto</a> per questo parametro per l'utilizzo di distribuito personalizzate del sistema. Il valore predefinito è: <code>general</code>.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica come deve essere trattato il contenuto volgare nelle traduzioni. I valori possibili sono: <code>NoAction</code> (impostazione predefinita), <code>Marked</code> o <code>Deleted</code>. Per informazioni sui metodi di gestione del contenuto volgare, vedere <a href="#handle-profanity">Gestione del contenuto volgare</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica come deve essere contrassegnato il contenuto volgare nelle traduzioni. I valori possibili sono: <code>Asterisk</code> (impostazione predefinita) o <code>Tag</code>. Per informazioni sui metodi di gestione del contenuto volgare, vedere <a href="#handle-profanity">Gestione del contenuto volgare</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica se includere la proiezione dell'allineamento dal testo di origine al testo tradotto. I valori possibili sono: <code>true</code> o <code>false</code> (impostazione predefinita). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica se includere delimitatori di frase per il testo di input e il testo tradotto. I valori possibili sono: <code>true</code> o <code>false</code> (impostazione predefinita).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica una lingua di fallback se non è possibile identificare la lingua del testo di input. Viene applicato il rilevamento automatico della lingua quando il parametro <code>from</code> viene omesso. Se il rilevamento non riesce, la lingua <code>suggestedFrom</code> verrà presupposta.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica l'alfabeto del testo di input.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica l'alfabeto del testo tradotto.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Parametro facoltativo</em>.<br/>Specifica che il servizio può eseguire il fallback in un sistema generale quando non esiste un sistema personalizzato. I valori possibili sono: <code>true</code> (impostazione predefinita) o <code>false</code>.<br/><br/><code>allowFallback=false</code> specifica che la traduzione deve usare solo sistemi sottoposti a training per il valore di <code>category</code> definito dalla richiesta. Se una traduzione dalla lingua X alla lingua Y richiede un concatenamento tramite una lingua pivot E, tutti i sistemi nella catena (X->E ed E->Y) dovranno essere personalizzati e avere la stessa categoria. Se non viene trovato alcun sistema con la specifica categoria, la richiesta restituirà un codice di stato 400. <code>allowFallback=true</code> specifica che il servizio può eseguire il fallback in un sistema generale quando non esiste un sistema personalizzato.
</td>
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
    <td><em>Intestazione della richiesta obbligatoria</em>.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:<code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Intestazione della richiesta obbligatoria</em>.<br/>Lunghezza del corpo della richiesta.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Facoltativo</em>.<br/>GUID generato dal client che identifica in modo univoco la richiesta. È possibile omettere questa intestazione se nella stringa della query si include l'ID traccia usando un parametro di query denominato <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento di matrice è un oggetto JSON con una proprietà di stringa denominata `Text`, che rappresenta la stringa da tradurre.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 100 elementi.
* L'intero testo incluso nella richiesta non può superare 5.000 caratteri inclusi gli spazi.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni stringa nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `detectedLanguage`: oggetto che descrive la lingua rilevata tramite le proprietà seguenti:

      * `language`: stringa che rappresenta il codice della lingua rilevata.

      * `score`: valore float che indica il livello di attendibilità del risultato. Il punteggio è compreso tra zero e uno e un punteggio basso indica un'attendibilità bassa.

    La proprietà `detectedLanguage` è presente nell'oggetto risultato solo quando viene richiesto il rilevamento automatico della lingua.

  * `translations`: matrice dei risultati della traduzione. Le dimensioni della matrice corrispondono al numero di lingue di destinazione specificate tramite il parametro di query `to`. Ogni elemento nella matrice include:

    * `to`: stringa che rappresenta il codice lingua della lingua di destinazione.

    * `text`: stringa che fornisce il testo tradotto.

    * `transliteration`: oggetto che fornisce il testo tradotto nell'alfabeto specificato dal parametro `toScript`.

      * `script`: stringa che specifica l'alfabeto di destinazione.   

      * `text`: stringa che fornisce il testo tradotto nell'alfabeto di destinazione.

    L'oggetto `transliteration` non è incluso se non viene eseguita la traslitterazione.

    * `alignment`: oggetto con una proprietà di stringa singola denominata `proj`, che esegue il mapping del testo di input con quello tradotto. Le informazioni di allineamento sono disponibili solo quando il parametro della richiesta `includeAlignment` è `true`. L'allineamento viene restituito come un valore di stringa nel formato seguente: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  I due punti separano l'indice iniziale e finale, il trattino separa le lingue e uno spazio separa le parole. Una parola può essere allineata con nessuna, una o più parole nell'altra lingua e le parole allineate potrebbero non essere contigue. Quando non è disponibile alcuna informazione di allineamento, l'elemento di allineamento sarà vuoto. Vedere [Ottenere informazioni sull'allineamento](#obtain-alignment-information) per un esempio e le restrizioni.

    * `sentLen`: oggetto che restituisce delimitatori di frase nei testi di input e output.

      * `srcSentLen`: matrice di interi che rappresenta le lunghezze delle frasi nel testo di input. La lunghezza della matrice è il numero di frasi e i valori sono la lunghezza di ogni frase.

      * `transSentLen`:  matrice di interi che rappresenta le lunghezze delle frasi nel testo tradotto. La lunghezza della matrice è il numero di frasi e i valori sono la lunghezza di ogni frase.

    I delimitatori di frase vengono inclusi solo quando il parametro della richiesta `includeSentenceLength` è `true`.

  * `sourceText`: oggetto con una proprietà di stringa singola denominata `text`, che fornisce il testo di input nel carattere predefinito della lingua di origine. La proprietà `sourceText` è presente solo quando l'input è espresso in un carattere che non è quello consueto per la lingua. Ad esempio, se l'input è in arabo scritto in caratteri dell'alfabeto latino, `sourceText.text` è lo stesso testo in arabo convertito in caratteri dell'alfabeto arabo.

Nella sezione [Esempi](#examples) è disponibile un esempio di risposte JSON.

## <a name="response-headers"></a>Intestazioni della risposta

<table width="100%">
  <th width="20%">Headers</th>
  <th>Descrizione</th>
    <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Viene usato per la risoluzione dei problemi.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Specifica il tipo di sistema che è stato usato per la traduzione per ogni lingua di destinazione richiesta. Il valore è un elenco di stringhe delimitate da virgole. Ogni stringa indica un tipo:<br/><ul><li>Custom: la richiesta include un sistema personalizzato e durante la traduzione è stato usato almeno un sistema personalizzato.</li><li>Team: tutte le altre richieste.</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>Descrizione</th>
  <tr>
    <td>200</td>
    <td>Completamento della procedura.</td>
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
    <td>408</td>
    <td>Non è stato possibile soddisfare la richiesta perché manca una risorsa. Controllare il messaggio di errore per i dettagli. Quando si usa un valore di <code>category</code> personalizzato, il messaggio spesso indica che il sistema di traduzione personalizzato non è ancora disponibile per rispondere alle richieste. Il tentativo di richiesta deve essere ripetuto dopo un periodo di attesa, ad esempio 1 minuto.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta <code>X-RequestId</code> e identificatore del client dall'intestazione della richiesta <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Il server è temporaneamente non disponibile. ripetere la richiesta. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta <code>X-RequestId</code> e identificatore del client dall'intestazione della richiesta <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Se si verifica un errore, la richiesta restituirà anche una risposta di errore JSON. Il codice errore è un numero a 6 cifre che combina il codice di stato HTTP a 3 cifre seguito da un numero a 3 cifre per classificare ulteriormente l'errore. I codici di errore più comuni sono reperibili nella [pagina di riferimento API Traduzione testuale V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Esempi

### <a name="translate-a-single-input"></a>Tradurre un singolo input

Questo esempio mostra come tradurre una singola frase dall'inglese al cinese semplificato.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Il corpo della risposta è:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

La matrice `translations` include un elemento, che fornisce la traduzione della singola parte di testo nell'input.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tradurre un singolo input con il rilevamento automatico della lingua

Questo esempio mostra come tradurre una singola frase dall'inglese al cinese semplificato. La richiesta non specifica la lingua di input. Viene usato invece il rilevamento automatico della lingua di origine.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Il corpo della risposta è:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
La risposta è simile a quella dell'esempio precedente. Poiché è stato richiesto il rilevamento automatico della lingua, la risposta include anche informazioni sulla lingua rilevata per il testo di input. 

### <a name="translate-with-transliteration"></a>Tradurre con traslitterazione

È possibile estendere l'esempio precedente aggiungendo la traslitterazione. La richiesta seguente richiede una traduzione in cinese scritta in caratteri dell'alfabeto latino.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Il corpo della risposta è:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Il risultato della traduzione include ora una proprietà `transliteration`, che fornisce il testo tradotto usando caratteri dell'alfabeto latino.

### <a name="translate-multiple-pieces-of-text"></a>Tradurre più parti di testo

La traduzione di più stringhe contemporaneamente equivale semplicemente a specificare una matrice di stringhe nel corpo della richiesta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

Il corpo della risposta è:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Tradurre in più lingue

Questo esempio mostra come tradurre lo stesso input in diverse lingue in una sola richiesta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Il corpo della risposta è:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Gestire contenuto volgare

In genere, il servizio Translator manterrà il contenuto volgare presente nell'origine della traduzione. Il grado di volgarità e il contesto che rende volgare una determinata parola sono diversi da una cultura all'altra, pertanto il grado di volgarità nella lingua di destinazione può essere amplificato o ridotto.

Se si vuole evitare la presenza di contenuto volgare nella traduzione, indipendentemente dalla presenza di contenuto volgare nel testo di origine, è possibile usare l'opzione di filtro per il contenuto volgare. L'opzione consente di scegliere se si vuole eliminare il contenuto volgare o contrassegnarlo con tag appropriati (da aggiungere in fase di post-elaborazione) oppure se non si vuole eseguire alcuna operazione. I valori consentiti di `ProfanityAction` sono `Deleted`, `Marked` e `NoAction` (impostazione predefinita).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Azione</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Questo è il comportamento predefinito. Il contenuto volgare passerà dall'origine alla destinazione.<br/><br/>
    <strong>Origine di esempio (giapponese)</strong>: 彼はジャッカスです。<br/>
    <strong>Traduzione di esempio (italiano)</strong>: È un cretino.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Le parole volgari verranno rimosse dall'output senza sostituzione.<br/><br/>
    <strong>Origine di esempio (giapponese)</strong>: 彼はジャッカスです。<br/>
    <strong>Traduzione di esempio (italiano)</strong>: È un.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Le parole volgari vengono sostituite da un indicatore nell'output. L'indicatore dipende dal parametro <code>ProfanityMarker</code>.<br/><br/>
Per <code>ProfanityMarker=Asterisk</code>, le parole volgari vengono sostituite con <code>***</code>:<br/>
    <strong>Origine di esempio (giapponese)</strong>: 彼はジャッカスです。<br/>
    <strong>Traduzione di esempio (italiano)</strong>: È un \*\*\*.<br/><br/>
Per <code>ProfanityMarker=Tag</code>, le parole volgari sono racchiuse tra i tag XML &lt;profanity&gt; e &lt;/profanity&gt;:<br/>
    <strong>Origine di esempio (giapponese)</strong>: 彼はジャッカスです。<br/>
    <strong>Traduzione di esempio (italiano)</strong>: È un &lt;profanity&gt;cretino&lt;/profanity&gt;.
  </tr>
</table> 

Ad esempio:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

Viene restituito:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Eseguire il confronto con:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

L'ultima richiesta restituisce:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tradurre contenuto con markup e decidere cosa tradurre

È comune tradurre contenuto che include markup, ad esempio il contenuto di una pagina HTML o di un documento XML. Includere il parametro di query `textType=html` durante la traduzione di contenuto con tag. Inoltre, a volte è utile escludere contenuto specifico dalla traduzione. È possibile usare l'attributo `class=notranslate` per specificare il contenuto che deve rimanere nella lingua originale. Nell'esempio seguente il contenuto all'interno del primo elemento `div` non verrà tradotto, mentre il contenuto nel secondo elemento `div` verrà tradotto.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Di seguito è riportata una richiesta di esempio.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

La risposta è:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Ottenere informazioni sull'allineamento

Per ricevere informazioni sull'allineamento, specificare `includeAlignment=true` nella stringa di query.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

La risposta è:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Le informazioni sull'allineamento iniziano con `0:2-0:1`, vale a dire che i primi tre caratteri nel testo di origine (`The`) sono associati ai primi due caratteri nel testo tradotto (`La`).

Prendere nota delle restrizioni seguenti:

* L'allineamento viene restituito solo per un subset delle coppie di lingue:
  - dall'inglese verso qualsiasi altra lingua;
  - da qualsiasi altra lingua verso l'inglese, ad eccezione di cinese semplificato, cinese tradizionale e lettone verso l'inglese;
  - da giapponese e coreano o da coreano a giapponese.
* Non si riceveranno informazioni sull'allineamento se la frase è una traduzione predefinita. Un esempio di traduzione predefinita è "Questo è un test", "Ti amo" e altre frasi usate frequentemente.

### <a name="obtain-sentence-boundaries"></a>Ottenere delimitatori di frase

Per ricevere informazioni sulla lunghezza della frase nel testo di origine e nel testo tradotto, specificare `includeSentenceLength=true` nella stringa di query.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

La risposta è:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Tradurre con un dizionario dinamico

Se si conosce già la traduzione che si vuole applicare a una parola o una frase, è possibile specificarla come markup all'interno della richiesta. Il dizionario dinamico è sicuro solo per nomi composti come nomi propri e nomi di prodotto.

Il markup da specificare usa la sintassi seguente.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Si consideri ad esempio la frase inglese "The word wordomatic is a dictionary entry". Per mantenere la parola _wordomatic_ nella traduzione, inviare la richiesta:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Il risultato è:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Questa funzionalità funziona allo stesso modo con `textType=text` o con `textType=html`. È consigliabile usarla solo in casi limitati. Il modo più appropriato e di gran lunga migliore per personalizzare una traduzione è quello di usare Custom Translator. Custom Translator fa un ampio uso delle probabilità statistiche e di contesto. Se si ha la possibilità di creare dati di training che mostrano l'azienda o una frase nel contesto, si otterranno certamente risultati migliori. [Altre informazioni su Custom Translator](../customization.md).
 





