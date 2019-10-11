---
title: API Traduzione testuale v2.0
titleSuffix: Azure Cognitive Services
description: Documentazione di riferimento per il API Traduzione testuale v 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242490"
---
# <a name="translator-text-api-v20"></a>API Traduzione testuale v2.0

> [!IMPORTANT]
> Questa versione dell'API Traduzione testuale è stata deprecata. [Visualizzare la documentazione per la versione 3 del API traduzione testuale](v3-0-reference.md).

La versione 2 del API Traduzione testuale può essere integrata facilmente in app, siti Web, strumenti o altre soluzioni per fornire esperienze utente multilingue. È possibile usarlo in qualsiasi piattaforma hardware e con qualsiasi sistema operativo per eseguire la traduzione della lingua e altre attività correlate al linguaggio, ad esempio il rilevamento della lingua del testo e la sintesi vocale, in base agli standard del settore. Per ulteriori informazioni, vedere [API traduzione testuale](../translator-info-overview.md).

## <a name="getting-started"></a>Introduzione
Per accedere alla API Traduzione testuale, è necessario [iscriversi per Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Authentication 
Tutte le chiamate al API Traduzione testuale richiedono una chiave di sottoscrizione per l'autenticazione. L'API supporta tre metodi di autenticazione:

- Un token di accesso. Usare la chiave di sottoscrizione per creare un token di accesso effettuando una richiesta POST al servizio di autenticazione. Per informazioni dettagliate, vedere la documentazione del servizio token. Passare il token di accesso al servizio di conversione utilizzando l'intestazione `Authorization` o il parametro di query `access_token`. Il token di accesso è valido per 10 minuti. Ottieni un nuovo token di accesso ogni 10 minuti e continua a usare lo stesso token di accesso per le richieste ripetute durante i 10 minuti.
- Chiave di sottoscrizione utilizzata direttamente. Passare la chiave di sottoscrizione come valore nell'intestazione `Ocp-Apim-Subscription-Key` inclusa nella richiesta al API Traduzione testuale. Quando si usa direttamente la chiave di sottoscrizione, non è necessario chiamare il servizio di autenticazione token per creare un token di accesso.
- [Sottoscrizione multiservizio di servizi cognitivi di Azure](https://azure.microsoft.com/pricing/details/cognitive-services/). Questo metodo consente di usare una singola chiave privata per autenticare le richieste di più servizi.
Quando si usa una chiave privata a più servizi, è necessario includere due intestazioni di autenticazione con la richiesta. La prima intestazione passa la chiave privata. La seconda intestazione specifica l'area associata alla sottoscrizione:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

L'area è obbligatoria per la sottoscrizione dell'API del testo multiservizio. L'area selezionata è l'unica area che è possibile usare per la traduzione del testo quando si usa la chiave di sottoscrizione multiservizio. Deve essere la stessa area selezionata quando è stata effettuata l'iscrizione per la sottoscrizione multiservizio nel portale di Azure.

Le aree disponibili sono `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, 0, 1, 2, 3, 4, 5 e 6.

La chiave e il token di accesso della sottoscrizione sono segreti che devono essere nascosti dalla visualizzazione.

## <a name="profanity-handling"></a>Gestione di contenuto volgare
In genere, il servizio di conversione manterrà la volgarità presente nell'origine. Il livello di volgarità e il contesto che rende le parole profane differiscono in base alle impostazioni cultura. Il grado di volgarità nella lingua di destinazione potrebbe quindi essere aumentato o ridotto.

Se si desidera impedire la volgarità nella traduzione anche quando si trova nel testo di origine, è possibile utilizzare l'opzione di filtro volgare per i metodi che lo supportano. L'opzione consente di scegliere se si desidera visualizzare la profanità eliminata o contrassegnata con i tag appropriati o se si desidera consentire la volgarità nella destinazione. I valori accettati di `ProfanityAction` sono `NoAction` (impostazione predefinita), `Marked` e `Deleted`.


|ProfanityAction    |Azione |Esempio di origine (giapponese)  |Esempio di traduzione (Inglese)  |
|:--|:--|:--|:--|
|NoAction   |Valore predefinito. Equivale a non impostare l'opzione. Il contenuto volgare passerà dall'origine alla destinazione.        |彼はジャッカスです。     |È un cretino.   |
|Marked     |Le parole profane saranno racchiuse tra tag XML \<profanity > e \</Profanity >.       |彼はジャッカスです。 |È un \<profanity > Jackass @ no__t-1/Profanity >.  |
|Deleted    |Le parole volgari verranno rimosse dall'output senza sostituzione.     |彼はジャッカスです。 |È un.   |

    
## <a name="excluding-content-from-translation"></a>Esclusione di contenuto dalla traduzione
Quando si converte il contenuto con tag, ad esempio HTML (`contentType=text/html`), a volte è utile escludere contenuto specifico dalla traduzione. È possibile usare l'attributo `class=notranslate` per specificare il contenuto che deve rimanere nella lingua originale. Nell'esempio seguente il contenuto del primo elemento `div` non verrà convertito, ma il contenuto nel secondo elemento `div` verrà convertito.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Note sull'implementazione
Traduce una stringa di testo da una lingua a un'altra.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valore restituito:** Stringa che rappresenta il testo tradotto.

Se in precedenza è stato usato `AddTranslation` o `AddTranslationArray` per immettere una traduzione con una classificazione di 5 o superiore per la stessa frase di origine, `Translate` restituisce solo la scelta migliore disponibile per il sistema. "Same source frase" significa esattamente lo stesso (100% corrispondente), ad eccezione delle maiuscole, degli spazi vuoti, dei valori dei tag e della punteggiatura alla fine di una frase. Se nessuna classificazione viene archiviata con una classificazione pari a 5 o successiva, il risultato restituito sarà la traduzione automatica da parte di Microsoft Translator.

### <a name="response-class-status-200"></a>Classe Response (stato 200)

string

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione    |Tipo di parametro|tipo di dati|
|:--|:--|:--|:--|:--|
|appid  |(vuoto)    |Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da tradurre. Il testo non può contenere più di 10.000 caratteri.|query|string|
|from|(vuoto)   |facoltativo. Stringa che rappresenta il codice della lingua del testo da tradurre. ad esempio en per "Inglese".|query|string|
|to|(vuoto) |Richiesto. Stringa che rappresenta il codice del linguaggio in cui tradurre il testo.|query|string|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono `text/plain` (impostazione predefinita) e `text/html`. È necessario che gli elementi HTML siano in formato corretto e completi.|query|string|
|category|(vuoto)   |facoltativo. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|Authorization|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|


### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le traduzioni per più testi di origine.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Ecco il formato del corpo della richiesta:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Questi elementi si trovano in `TranslateArrayRequest`:


* `AppId`: Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `AppId`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: facoltativo. Stringa che rappresenta il codice della lingua del testo da tradurre. Se questo campo viene lasciato vuoto, la risposta includerà il risultato del rilevamento automatico della lingua.
* `Options`: facoltativo. Oggetto `Options` che contiene i valori seguenti. Sono tutti facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: Formato del testo da tradurre. I formati supportati sono `text/plain` (impostazione predefinita), `text/xml` e `text/html`. È necessario che gli elementi HTML siano in formato corretto e completi.
    - `ProfanityAction`: Specifica il modo in cui vengono gestite le parolacce, come illustrato in precedenza. I valori accettati sono `NoAction` (impostazione predefinita), `Marked` e `Deleted`.
    - `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Lo stesso contenuto verrà restituito nella risposta.
    - `Uri`: filtrare i risultati in base a questo URI. Impostazione predefinita: `all`.
    - `User`: filtrare i risultati in base a questo utente. Impostazione predefinita: `all`.
* `Texts`: Richiesto. Matrice che contiene il testo per la conversione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da tradurre non può superare i 10.000 caratteri. Il numero massimo di elementi di matrice è 2.000.
* `To`: Richiesto. Stringa che rappresenta il codice del linguaggio in cui tradurre il testo.

È possibile omettere gli elementi facoltativi. Gli elementi che sono elementi figlio diretti di `TranslateArrayRequest` devono essere elencati in ordine alfabetico.

Il metodo `TranslateArray` accetta `application/xml` o `text/xml` per `Content-Type`.

**Valore restituito:** una matrice `TranslateArrayResponse`. Ogni `TranslateArrayResponse` ha questi elementi:

* `Error`: Indica un errore se si verifica un errore. In caso contrario, è impostato su null.
* `OriginalSentenceLengths`: Matrice di Integer che indica la lunghezza di ogni frase nel testo di origine. La lunghezza della matrice indica il numero di frasi.
* `TranslatedText`: testo tradotto.
* `TranslatedSentenceLengths`: Matrice di Integer che indica la lunghezza di ogni frase nel testo tradotto. La lunghezza della matrice indica il numero di frasi.
* `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Restituisce lo stesso contenuto della richiesta.

Ecco il formato del corpo della risposta:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Una risposta con esito positivo include una matrice di matrici `TranslateArrayResponse` nel formato descritto in precedenza.

string

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP   |`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata. Di seguito sono riportati gli errori più comuni. <ul><li>L'elemento della matrice non può essere vuoto.</li><li>Categoria non valida.</li><li>Il linguaggio from non è valido.</li><li>Il linguaggio non è valido.</li><li>La richiesta contiene troppi elementi.</li><li>Il linguaggio from non è supportato.</li><li>Il linguaggio to non è supportato.</li><li>La richiesta translate contiene troppi dati.</li><li>Il formato del codice HTML non è corretto.</li><li>Troppe stringhe passate nella richiesta translate.</li></ul>|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera i nomi descrittivi per le lingue passate come parametro `languageCodes`, localizzato nella lingua `locale` passata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Il corpo della richiesta include una matrice di stringhe che rappresenta i codici della lingua ISO 639-1 per i quali recuperare i nomi descrittivi. Di seguito è riportato un esempio:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valore restituito:** Matrice di stringhe che contiene i nomi di lingua supportati dal servizio di conversione, localizzati nella lingua richiesta.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Matrice di stringhe che contiene i nomi di lingue supportati dal servizio di conversione, localizzati nella lingua richiesta.

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|locale|(vuoto) |Richiesto. Stringa che rappresenta uno dei seguenti elementi utilizzati per localizzare i nomi di lingua: <ul><li>La combinazione di un codice di impostazioni cultura minuscole ISO 639 2-lettera associato a una lingua e a un codice di sottocultura in lettere maiuscole ISO 3166 2. <li>Codice delle impostazioni cultura minuscole ISO 639.|query|string|
|Authorization|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Note sull'implementazione
Ottiene un elenco di codici della lingua che rappresentano le lingue supportate dal servizio di traduzione.  `Translate` e `TranslateArray` consentono la traduzione tra due di queste lingue.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valore restituito:** Matrice di stringhe che contiene i codici lingua supportati dal servizio di conversione.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Matrice di stringhe che contiene i codici lingua supportati dal servizio di conversione.

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le lingue disponibili per la sintesi vocale.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valore restituito:** Matrice di stringhe che contiene i codici di lingua supportati per la sintesi vocale da parte del servizio di conversione.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Matrice di stringhe che contiene i codici di lingua supportati per la sintesi vocale da parte del servizio di conversione.

string

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|
 
### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Note sull'implementazione
Restituisce un flusso WAV o MP3 del testo passato, parlato nella lingua desiderata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valore restituito:** Un flusso WAV o MP3 del testo passato, parlato nella lingua desiderata.

### <a name="response-class-status-200"></a>Classe Response (stato 200)

binary

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)   |Richiesto. Stringa che contiene una o più frasi da pronunciare per il flusso, nella lingua specificata. Il testo non deve superare i 2.000 caratteri.|query|string|
|language|(vuoto)   |Richiesto. Stringa che rappresenta il codice di lingua supportato della lingua in cui pronunciare il testo. Il codice deve essere uno dei codici restituiti dal metodo `GetLanguagesForSpeak`.|query|string|
|format|(vuoto)|facoltativo. Stringa che specifica l'ID del tipo di contenuto. Attualmente, sono disponibili `audio/wav` e `audio/mp3`. Il valore predefinito è `audio/wav`.|query|string|
|options|(vuoto)    |facoltativo. Stringa che specifica le proprietà della voce sintetizzata:<ul><li>`MaxQuality` e `MinSize` specificano la qualità del segnale audio. `MaxQuality` fornisce la qualità più elevata. `MinSize` fornisce le dimensioni più piccole del file. Il valore predefinito è `MinSize`.</li><li>`female` e `male` specificano il sesso desiderato per la voce. Il valore predefinito è `female`. Utilizzare la barra verticale (<code>\|</code>) per includere più opzioni. Ad esempio, `MaxQuality|Male`.</li></li></ul>  |query|string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Note sull'implementazione
Identifica la lingua di una sezione di testo.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valore restituito:** Stringa che contiene un codice di lingua di due caratteri per il testo.

### <a name="response-class-status-200"></a>Classe Response (stato 200)

string

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)|Richiesto. Stringa che contiene il testo di cui è necessario identificare la lingua. Il testo non deve superare i 10.000 caratteri.|query|  string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key  |(vuoto)    |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Note sull'implementazione

Identifica le lingue in una matrice di stringhe. Rileva in modo indipendente la lingua di ogni singolo elemento della matrice e restituisce un risultato per ogni riga della matrice.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Ecco il formato del corpo della richiesta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Il testo non può superare i 10.000 caratteri.

**Valore restituito:** Matrice di stringhe che contiene un codice di lingua di due caratteri per ogni riga della matrice di input.

Ecco il formato del corpo della risposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe Response (stato 200)
`DetectArray` ha avuto esito positivo. Restituisce una matrice di stringhe che contiene un codice di lingua di due caratteri per ogni riga della matrice di input.

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di deprecazione:** Dopo il 31 gennaio 2018, questo metodo non accetta nuovi invii di frasi. Verrà ricevuto un messaggio di errore. Vedere l'annuncio relativo alle modifiche apportate a Collaborative Translation Framework (CTF).

Aggiunge una traduzione alla memoria di traduzione.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe Response (stato 200)

string

Tipo di contenuto della risposta: applicazione: XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati   |
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(vuoto)|Richiesto. Stringa che contiene il testo da tradurre. La lunghezza massima della stringa è 1.000 caratteri.|query|string|
|translatedText|(vuoto) |Richiesto. Stringa che contiene il testo tradotto nella lingua di destinazione. La lunghezza massima della stringa è 2.000 caratteri.|query|string|
|from|(vuoto)   |Richiesto. Stringa che rappresenta il codice della lingua originale del testo. Ad esempio, en per la lingua inglese e de per il tedesco.|query|string|
|to|(vuoto)|Richiesto. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.|query|string|
|rating|(vuoto) |facoltativo. Intero che rappresenta la classificazione di qualità per la stringa. Il valore è compreso tra-10 e 10. Il valore predefinito è 1.|query|integer|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono `text/plain` e `text/html`. È necessario che gli elementi HTML siano in formato corretto e completi.    |query|string|
|category|(vuoto)|facoltativo. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|user|(vuoto)|Richiesto. Stringa utilizzata per tenere traccia del creatore dell'invio.|query|string|
|Uri|(vuoto)|facoltativo. Stringa che contiene il percorso del contenuto della traduzione.|query|string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.  |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410|`AddTranslation` non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di deprecazione:** Dopo il 31 gennaio 2018, questo metodo non accetta nuovi invii di frasi. Verrà ricevuto un messaggio di errore. Vedere l'annuncio relativo alle modifiche apportate a Collaborative Translation Framework (CTF).

Aggiunge una matrice di traduzioni alla memoria di conversione. Questo metodo è una versione di matrice di `AddTranslation`.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Ecco il formato del corpo della richiesta:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Questi elementi si trovano in `AddtranslationsRequest`:

* `AppId`: Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `AppId`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: Richiesto. Stringa che contiene il codice lingua della lingua di origine. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `To`: Richiesto. Stringa che contiene il codice lingua della lingua di destinazione. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `Translations`: Richiesto. Matrice di traduzioni da aggiungere alla memoria di traduzione. Ogni traduzione deve contenere `OriginalText`, `TranslatedText` e `Rating`. La dimensione massima di ogni `OriginalText` e `TranslatedText` è 1.000 caratteri. Il totale di tutti gli elementi `OriginalText` e `TranslatedText` non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 100.
* `Options`: Richiesto. Set di opzioni, tra cui `Category`, `ContentType`, `Uri` e `User`. `User` è obbligatorio. `Category`, `ContentType` e `Uri` sono facoltativi. Gli elementi specificati devono essere elencati in ordine alfabetico.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Metodo `AddTranslationArray` riuscito. 

Dopo il 31 gennaio 2018, gli invii di frasi non verranno accettati. Il servizio risponderà con un codice di errore 410.

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410    |`AddTranslation` non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Note sull'implementazione
Suddivide una sezione di testo in frasi e restituisce una matrice che contiene le lunghezze di ogni frase.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valore restituito:** Matrice di numeri interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Matrice di numeri interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

integer

Tipo di contenuto della risposta: Application/XML

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query| string|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da suddividere in frasi. La dimensione massima del testo è di 10.000 caratteri.|query|string|
|language   |(vuoto)    |Richiesto. Stringa che rappresenta il codice della lingua del testo di input.|query|string|
|Authorization|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.   |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500|Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera una matrice di traduzioni per una coppia di lingue specificata dall'archivio e dal motore MT. `GetTranslations` differisce da `Translate` in quanto restituisce tutte le traduzioni disponibili.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Il corpo della richiesta include l'oggetto facoltativo `TranslationOptions`, il cui formato è il seguente:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

L'oggetto `TranslateOptions` contiene i valori nell'elenco seguente. Sono tutti facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.

* `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
* `ContentType`: L'unica opzione supportata e l'impostazione predefinita è `text/plain`.
* `IncludeMultipleMTAlternatives`: Flag booleano per specificare se deve essere restituita più di un'alternativa dal motore MT. I valori validi sono `true` e `false` (con distinzione tra maiuscole e minuscole). Il valore predefinito è `false`, che restituisce solo un'alternativa. Impostando il flag su `true` è possibile creare alternative artificiali, completamente integrate con il Framework CTF (Collaborative Translation Framework). La funzionalità consente di restituire alternative per le frasi senza traduzione in CTF aggiungendo alternative artificiali dall'elenco *n*-Best del decodificatore.
    - Classificazioni. Le classificazioni vengono applicate come segue: 
         - la miglior traduzione automatica ha una classificazione pari a 5.
       - Le alternative di CTF riflettono l'autorità del revisore. L'intervallo è compreso tra-10 e + 10.
       - Le alternative di traduzione generate automaticamente (*n*-Best) hanno una classificazione pari a 0 e un grado di corrispondenza di 100.
    - Numero di alternative. Il numero di alternative restituite può essere il più elevato del valore specificato in `maxTranslations`, ma può essere inferiore.
    - Coppie di lingue. Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Lo stesso contenuto verrà restituito nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non è impostato alcun valore, il valore predefinito è `all`.
* `User`: filtrare i risultati in base a questo utente. Se non è impostato alcun valore, il valore predefinito è `all`.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** Ecco il formato della risposta:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Questa risposta include un elemento `GetTranslationsResponse` che contiene i valori seguenti:

* `Translations`: Matrice delle corrispondenze trovate, archiviate in oggetti `TranslationMatch` (descritti nella sezione seguente). Le traduzioni possono includere lievi varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate: 100% corrisponde al primo, corrispondenze fuzzy successiva.
* `From`: Se il metodo non specifica una lingua `From`, questo valore proverrà dal rilevamento automatico della lingua. In caso contrario, sarà la lingua `From` specificata.
* `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Contiene il valore fornito nel parametro `TranslateOptions`.

L'oggetto `TranslationMatch` è costituito da questi valori:

* `Error`: Codice di errore, se si verifica un errore per una stringa di input specifica. In caso contrario, questo campo è vuoto.
* `MatchDegree`: Indica la precisione del testo di input corrispondente al testo originale trovato nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito è compreso tra 0 e 100, dove 0 non è simile e 100 è una corrispondenza esatta con distinzione tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene usato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica hanno una classificazione di 5. Le traduzioni fornite anonimamente hanno in genere una classificazione compresa tra 1 e 4. Le traduzioni fornite con autorevoli in genere avranno una classificazione compresa tra 6 e 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.

### <a name="response-class-status-200"></a>Classe Response (stato 200)
Oggetto `GetTranslationsResponse` nel formato descritto in precedenza.

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se viene utilizzata l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo `appid`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)|Richiesto. Stringa che rappresenta il testo da tradurre. La dimensione massima del testo è di 10.000 caratteri.|query|string|
|from|(vuoto)|Richiesto. Stringa che rappresenta il codice della lingua del testo da tradurre.|query|string|
|to |(vuoto)    |Richiesto. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.|query|string|
|maxTranslations|(vuoto)|Richiesto. Intero che rappresenta il numero massimo di conversioni da restituire.|query|integer|
|Authorization| (vuoto)|Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|string|  intestazione|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Specificare la data approssimativa & ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera più candidati alla conversione per più testi di origine.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Ecco il formato del corpo della richiesta:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` include gli elementi seguenti:

* `AppId`: Richiesto. Se viene utilizzata l'intestazione `Authorization`, lasciare vuoto il campo `AppId`. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: Richiesto. Stringa che rappresenta il codice della lingua del testo da tradurre.
* `MaxTranslations`: Richiesto. Intero che rappresenta il numero massimo di conversioni da restituire.
* `Options`: facoltativo. Oggetto `Options` che contiene i valori seguenti. Sono tutti facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: L'unica opzione supportata e l'impostazione predefinita è `text/plain`.
    - `IncludeMultipleMTAlternatives`: Flag booleano per specificare se deve essere restituita più di un'alternativa dal motore MT. I valori validi sono `true` e `false` (con distinzione tra maiuscole e minuscole). Il valore predefinito è `false`, che restituisce solo un'alternativa. L'impostazione del flag su `true` consente la generazione di alternative artificiali nella traduzione, completamente integrata con collaborative translations Framework (CTF). La funzionalità consente di restituire alternative per le frasi che non hanno alternative in CTF aggiungendo alternative artificiali dall'elenco *n*-Best del decodificatore.
        - Classificazioni a cui vengono applicate le classificazioni seguenti:
          - la miglior traduzione automatica ha una classificazione pari a 5.
          - Le alternative di CTF riflettono l'autorità del revisore. L'intervallo è compreso tra-10 e + 10.
          - Le alternative di traduzione generate automaticamente (*n*-Best) hanno una classificazione pari a 0 e un grado di corrispondenza di 100.
        - Numero di alternative. Il numero di alternative restituite può essere il più elevato del valore specificato in `maxTranslations`, ma può essere inferiore.
        - Coppie di lingue. Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Lo stesso contenuto verrà restituito nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non è impostato alcun valore, il valore predefinito è `all`.
* `User`: filtrare i risultati in base a questo utente. Se non è impostato alcun valore, il valore predefinito è `all`.
* `Texts`: Richiesto. Matrice che contiene il testo per la conversione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da tradurre non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 10.
* `To`: Richiesto. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.

È possibile omettere gli elementi facoltativi. Gli elementi che sono elementi figlio diretti di `GetTranslationsArrayRequest` devono essere elencati in ordine alfabetico.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** Ecco il formato della risposta:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Ogni elemento `GetTranslationsResponse` contiene i valori seguenti:

* `Translations`: Matrice delle corrispondenze trovate, archiviate in oggetti `TranslationMatch` (descritti nella sezione seguente). Le traduzioni possono includere lievi varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate: 100% corrisponde al primo, corrispondenze fuzzy successiva.
* `From`: Se il metodo non specifica una lingua `From`, questo valore proverrà dal rilevamento automatico della lingua. In caso contrario, sarà la lingua `From` specificata.
* `State`: Stato utente per consentire la correlazione della richiesta e della risposta. Contiene il valore fornito nel parametro `TranslateOptions`.

L'oggetto `TranslationMatch` contiene i valori seguenti:
* `Error`: Codice di errore, se si verifica un errore per una stringa di input specifica. In caso contrario, questo campo è vuoto.
* `MatchDegree`: Indica la precisione del testo di input corrispondente al testo originale trovato nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito è compreso tra 0 e 100, dove 0 non è simile e 100 è una corrispondenza esatta con distinzione tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene usato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica hanno una classificazione di 5. Le traduzioni fornite anonimamente hanno in genere una classificazione compresa tra 1 e 4. Le traduzioni fornite con autorevoli in genere hanno una classificazione compresa tra 6 e 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.


### <a name="response-class-status-200"></a>Classe Response (stato 200)

string

Tipo di contenuto della risposta: Application/XML
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization  |(vuoto)    |Obbligatorio se il campo `appid` e l'intestazione `Ocp-Apim-Subscription-Key` vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se il campo `appid` e l'intestazione `Authorization` vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione a API Traduzione testuale V3](../migrate-to-v3.md)


