---
title: API Traduzione testuale v2.0
titleSuffix: Azure Cognitive Services
description: Documentazione di riferimento per l'API traduzione testuale Microsoft Translator v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: a29e123c44ca198ce19db451ee4c624b6f993538
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705419"
---
# <a name="translator-text-api-v20"></a>API Traduzione testuale v2.0

> [!IMPORTANT]
> Questa versione dell'API Traduzione testuale è stata deprecata. [Visualizzare la documentazione della versione 3 dell'API traduzione testuale](v3-0-reference.md).

La versione 2 dell'API traduzione testuale può essere integrata perfettamente nelle tue App, siti Web, strumenti o altre soluzioni per fornire esperienze utente multilingue. È possibile usarlo su qualsiasi piattaforma di hardware e con qualsiasi sistema operativo per eseguire la traduzione e altre attività correlate alla lingua, come il rilevamento della lingua di testo e sintesi vocale, in base agli standard del settore. Per altre informazioni, vedere [API traduzione testuale](../translator-info-overview.md).

## <a name="getting-started"></a>Introduzione
Per accedere alle API traduzione testuale, devi [iscriversi a Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Authentication 
Tutte le chiamate all'API traduzione testuale richiedono una chiave di sottoscrizione per l'autenticazione. L'API supporta tre metodi di autenticazione:

- Un token di accesso. Usare la chiave di sottoscrizione cui viene fatto riferimento nel passaggio 9 per creare un token di accesso eseguendo una richiesta POST al servizio di autenticazione. Per informazioni dettagliate, vedere la documentazione del servizio token. Passare il token di accesso al servizio Microsoft Translator con i `Authorization` intestazione o il `access_token` parametro di query. Il token di accesso è valido per 10 minuti. Ottenere un nuovo token di accesso ogni 10 minuti e continuare a usare lo stesso accesso token per le richieste ripetute 10 minuti.
- Una chiave di sottoscrizione utilizzata direttamente. Passare la chiave di sottoscrizione come un valore nel `Ocp-Apim-Subscription-Key` intestazione inclusa con la richiesta per l'API traduzione testuale. Quando si usa direttamente la chiave di sottoscrizione, non è necessario chiamare il servizio di autenticazione basata su token per creare un token di accesso.
- Un' [sottoscrizione di servizi cognitivi di Azure multi-service](https://azure.microsoft.com/pricing/details/cognitive-services/). Questo metodo consente di usare una singola chiave segreta per autenticare le richieste per più servizi.
Quando si usa una chiave privata multiservizio, è necessario includere due intestazioni di autenticazione con la richiesta. La prima intestazione passa la chiave privata. La seconda intestazione specifica l'area associata alla sottoscrizione:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

L'area è obbligatoria per la sottoscrizione di API traduzione testuale multiservizio. L'area selezionata è l'unica area che è possibile usare per la traduzione del testo quando si usa la chiave di sottoscrizione multiservizio. Deve trovarsi nella stessa area selezionato al momento dell'iscrizione per la sottoscrizione nel portale di Azure multi-service.

Le aree disponibili sono `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, e `westus2`.

La chiave di sottoscrizione e l'accesso token costituiscono informazioni riservate che devono essere nascoste dalla visualizzazione.

## <a name="profanity-handling"></a>Gestione di contenuto volgare
In genere, il servizio Microsoft Translator conserverà il contenuto volgare presente nell'origine. Il livello di contenuto volgare e il contesto che rende le parole blasfeme differiscono in base alle impostazioni cultura. Pertanto, è stato possibile aumentare o ridurre il grado di contenuti profani nella lingua di destinazione.

Se si desidera evitare che il contenuto volgare nella traduzione anche quando è in testo di origine, è possibile usare l'opzione per i metodi che supportano questa tecnologia di filtro per le espressioni volgari. L'opzione consente di scegliere se si desidera visualizzare il contenuto volgare eliminate o contrassegnate con tag appropriati, o se si vuole consentire il contenuto volgare nel database di destinazione. I valori di accettazione `ProfanityAction` vengono `NoAction` (impostazione predefinita), `Marked`, e `Deleted`.


|ProfanityAction    |Azione |Origine di esempio (giapponese)  |Conversione di esempio (in inglese)  |
|:--|:--|:--|:--|
|NoAction   |Default. Equivale a non impostare l'opzione. Il contenuto volgare passerà dall'origine alla destinazione.        |彼はジャッカスです。     |È un cretino.   |
|Marked     |Parole blasfeme saranno essere racchiuso tra tag XML \<per le espressioni volgari > e \</profanity >.       |彼はジャッカスです。 |È un \<per le espressioni volgari > jackass\</profanity >.  |
|Deleted    |Le parole volgari verranno rimosse dall'output senza sostituzione.     |彼はジャッカスです。 |È un.   |

    
## <a name="excluding-content-from-translation"></a>Esclusione di contenuto dalla traduzione
Quando si traducono contenuti con tag, ad esempio HTML (`contentType=text/html`), è talvolta utile escludere determinati contenuti dalla traduzione. È possibile usare l'attributo `class=notranslate` per specificare il contenuto che deve rimanere nella lingua originale. Nell'esempio seguente, il contenuto nel primo `div` elemento non verrà convertito, ma il contenuto nel secondo `div` elemento verrà convertito.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Note sull'implementazione
Traduce una stringa di testo da una lingua a un'altra.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valore restituito:** Stringa che rappresenta il testo tradotto.

Se è stato usato in precedenza `AddTranslation` oppure `AddTranslationArray` per immettere una traduzione con una classificazione pari a 5 o superiore per la stessa frase di origine, `Translate` restituisce solo la scelta di principali che è disponibile nel sistema. "Stessa frase di origine" significa esattamente la stessa (100% corrispondente), fatta eccezione per l'uso delle maiuscole, gli spazi vuoti, i valori dei tag e la punteggiatura alla fine di una frase. Se non viene archiviato con un punteggio pari a 5 o versione successiva, il risultato restituito sarà la traduzione automatica da Microsoft Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione    |Tipo di parametro|tipo di dati|
|:--|:--|:--|:--|:--|
|appid  |(vuoto)    |Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da tradurre. Il testo non può contenere più di 10.000 caratteri.|query|string|
|from|(vuoto)   |facoltativo. Stringa che rappresenta il codice di lingua del testo da tradurre. ad esempio en per "Inglese".|query|string|
|to|(vuoto) |Richiesto. Stringa che rappresenta il codice del linguaggio da tradurre il testo in.|query|string|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono `text/plain` (impostazione predefinita) e `text/html`. Tutti gli elementi HTML devono essere elementi in formato corretto e completo.|query|string|
|category|(vuoto)   |facoltativo. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|Authorization|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|


### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le traduzioni di testi sorgente multipli.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Di seguito è riportato il formato del corpo della richiesta:

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

Questi elementi sono `TranslateArrayRequest`:


* `AppId`: Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `AppId` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: facoltativo. Stringa che rappresenta il codice di lingua del testo da tradurre. Se questo campo viene lasciato vuoto, la risposta includerà il risultato del rilevamento automatico della lingua.
* `Options`: facoltativo. Un `Options` oggetto che contiene i valori seguenti. Sono facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: Formato del testo da tradurre. I formati supportati sono `text/plain` (impostazione predefinita), `text/xml`, e `text/html`. Tutti gli elementi HTML devono essere elementi in formato corretto e completo.
    - `ProfanityAction`: Specifica come vengono gestiti profanities, come illustrato in precedenza. Valori accettati sono `NoAction` (impostazione predefinita), `Marked`, e `Deleted`.
    - `State`: Stato utente che consente di correlare la richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
    - `Uri`: filtrare i risultati in base a questo URI. Impostazione predefinita: `all`.
    - `User`: filtrare i risultati in base a questo utente. Impostazione predefinita: `all`.
* `Texts`: Richiesto. Matrice che contiene il testo per la conversione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da convertire non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 2.000.
* `To`: Richiesto. Stringa che rappresenta il codice del linguaggio da tradurre il testo in.

È possibile omettere gli elementi facoltativi. Gli elementi che sono elementi figlio diretti del `TranslateArrayRequest` devono essere elencati in ordine alfabetico.

Il `TranslateArray` metodo accetta `application/xml` oppure `text/xml` per `Content-Type`.

**Valore restituito:** una matrice `TranslateArrayResponse`. Ogni `TranslateArrayResponse` abbia questi elementi:

* `Error`: Indica un errore se si verifica uno. In caso contrario, è impostato su null.
* `OriginalSentenceLengths`: Matrice di interi che indica la lunghezza di ogni frase nel testo di origine. La lunghezza della matrice indica il numero di frasi.
* `TranslatedText`: testo tradotto.
* `TranslatedSentenceLengths`: Matrice di interi che indica la lunghezza di ogni frase in testo tradotto. La lunghezza della matrice indica il numero di frasi.
* `State`: Stato utente che consente di correlare la richiesta e risposta. Restituisce lo stesso contenuto della richiesta.

Di seguito è riportato il formato del corpo della risposta:

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

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Una risposta con esito positivo include un'ampia gamma di `TranslateArrayResponse` matrici nel formato descritto in precedenza.

string

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP   |`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata. Di seguito sono riportati gli errori più comuni. <ul><li>Elemento della matrice non può essere vuoto.</li><li>Categoria non è valido.</li><li>Da lingua non è valido.</li><li>Linguaggio non è valido.</li><li>La richiesta contiene troppi elementi.</li><li>Il linguaggio From non è supportato.</li><li>Il linguaggio To non è supportato.</li><li>Tradurre richiesta contiene troppi dati.</li><li>HTML non è presente in un formato corretto.</li><li>Un numero eccessivo di stringhe sono state passate nella richiesta di conversione.</li></ul>|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera i nomi descrittivi per le lingue passato come parametro `languageCodes`localizzato nell'oggetto passato `locale` language.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Il corpo della richiesta include una matrice di stringhe che rappresenta i codici ISO 639-1 lingua per cui recuperare i nomi descrittivi. Ad esempio:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valore restituito:** Matrice di stringhe che contiene nomi di linguaggio supportati dal servizio di Microsoft Translator, localizzato nella lingua richiesta.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe contenente i nomi dei linguaggi supportati dal servizio di Microsoft Translator, localizzato nella lingua richiesta.

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|locale|(vuoto) |Richiesto. Una stringa che rappresenta uno dei valori seguenti, utilizzati per localizzare i nomi di linguaggio: <ul><li>La combinazione di un codice di cultura in lettere minuscole due lettere ISO 639 associato a un linguaggio e un codice di secondarie maiuscole di due lettere ISO 3166. <li>Un codice di cultura in lettere minuscole ISO 639 per sé.|query|string|
|Authorization|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Note sull'implementazione
Ottiene un elenco dei codici di lingua che rappresentano le lingue supportate dal servizio di traduzione.  `Translate` e `TranslateArray` consentono la traduzione tra due di queste lingue.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valore restituito:** Matrice di stringhe che contiene i codici lingua supportati dal servizio Microsoft Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe che contiene i codici lingua supportati dal servizio Microsoft Translator.

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le lingue disponibili per la sintesi vocale.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valore restituito:** Matrice di stringhe che contiene i codici lingua supportati per la sintesi vocale dal servizio Microsoft Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe che contiene i codici lingua supportati per la sintesi vocale dal servizio Microsoft Translator.

string

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|
 
### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Note sull'implementazione
Restituisce un flusso in formato WAV o MP3 del testo passato, pronunciato nella lingua desiderata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valore restituito:** Flusso WAV o MP3 del testo passato, pronunciato nella lingua desiderata.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

binary

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)   |Richiesto. Stringa che contiene uno o più frasi da leggere per il flusso, nella lingua specificata. Il testo non deve superare i 2.000 caratteri.|query|string|
|language|(vuoto)   |Richiesto. Stringa che rappresenta il codice di lingua supportata della lingua in cui pronunciare il testo. Il codice deve essere uno dei codici restituiti dal metodo `GetLanguagesForSpeak`.|query|string|
|format|(vuoto)|facoltativo. Stringa che specifica l'ID del tipo di contenuto. Attualmente, sono disponibili `audio/wav` e `audio/mp3`. Il valore predefinito è `audio/wav`.|query|string|
|options|(vuoto)    |facoltativo. Stringa che specifica le proprietà di sintesi vocale:<ul><li>`MaxQuality` e `MinSize` specifica la qualità del segnale audio. `MaxQuality` fornisce la massima qualità. `MinSize` fornisce le dimensioni del file più piccolo. Il valore predefinito è `MinSize`.</li><li>`female` e `male` specifica il genere della voce desiderato. Il valore predefinito è `female`. Usare la barra verticale (<code>\|</code>) per includere più opzioni. Ad esempio, `MaxQuality|Male`.</li></li></ul>  |query|string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Note sull'implementazione
Identifica la lingua di una sezione di testo.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valore restituito:** Stringa che contiene un codice di lingua di due caratteri per il testo.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)|Richiesto. Stringa che contiene il testo la cui lingua è l'identificazione. Il testo non deve superare i 10.000 caratteri.|query|  string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key  |(vuoto)    |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Note sull'implementazione

Identifica le lingue in una matrice di stringhe. In modo indipendente, rileva la lingua di ogni elemento della matrice singoli e restituisce un risultato per ogni riga della matrice.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Di seguito è riportato il formato del corpo della richiesta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Il testo non può superare i 10.000 caratteri.

**Valore restituito:** Matrice di stringhe che contiene un codice di lingua di due caratteri per ogni riga nella matrice di input.

Di seguito è riportato il formato del corpo della risposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
`DetectArray` è stata completata. Restituisce una matrice di stringhe che contiene un codice di lingua di due caratteri per ogni riga della matrice di input.

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di elementi deprecati:** Dopo il 31 gennaio 2018, questo metodo non accetta nuovi invii di frase. Si riceverà un messaggio di errore. Vedere l'annuncio sulle modifiche apportate a Collaborative Translation Framework (CTF).

Aggiunge una traduzione alla memoria di traduzione.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto della risposta: applicazione: xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati   |
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(vuoto)|Richiesto. Stringa che contiene il testo da tradurre. La lunghezza massima della stringa è 1000 caratteri.|query|string|
|translatedText|(vuoto) |Richiesto. Stringa che contiene il testo tradotto in lingua di destinazione. La lunghezza massima della stringa è 2.000 caratteri.|query|string|
|from|(vuoto)   |Richiesto. Stringa che rappresenta il codice di lingua del linguaggio originale del testo. Ad esempio, en per inglese e de per il tedesco.|query|string|
|to|(vuoto)|Richiesto. Stringa che rappresenta il codice di lingua del linguaggio da tradurre il testo in.|query|string|
|rating|(vuoto) |facoltativo. Valore intero che rappresenta la classificazione di qualità per la stringa. Il valore è compreso tra -10 e 10. Il valore predefinito è 1.|query|integer|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono `text/plain` e `text/html`. Tutti gli elementi HTML devono essere elementi in formato corretto e completo.    |query|string|
|category|(vuoto)|facoltativo. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|user|(vuoto)|Richiesto. Stringa che viene utilizzata per rilevare il mittente dell'invio.|query|string|
|Uri|(vuoto)|facoltativo. Stringa che contiene il percorso del contenuto della traduzione.|query|string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.  |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410|`AddTranslation` non è più supportata.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di elementi deprecati:** Dopo il 31 gennaio 2018, questo metodo non accetta nuovi invii di frase. Si riceverà un messaggio di errore. Vedere l'annuncio sulle modifiche apportate a Collaborative Translation Framework (CTF).

Aggiunge una matrice di traduzioni per la memoria di traduzione. Questo metodo è una versione di matrice di `AddTranslation`.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Di seguito è riportato il formato del corpo della richiesta:

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

Questi elementi sono `AddtranslationsRequest`:

* `AppId`: Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `AppId` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: Richiesto. Stringa che contiene il codice di lingua di origine. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `To`: Richiesto. Stringa che contiene il codice di lingua di destinazione. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `Translations`: Richiesto. Matrice di traduzioni da aggiungere alla memoria di traduzione. Ogni traduzione deve contenere `OriginalText`, `TranslatedText`, e `Rating`. La dimensione massima della ognuno `OriginalText` e `TranslatedText` è 1000 caratteri. Il totale di tutti i `OriginalText` e `TranslatedText` elementi non possono superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 100.
* `Options`: Richiesto. Un set di opzioni, tra cui `Category`, `ContentType`, `Uri`, e `User`. `User` è obbligatorio. `Category`, `ContentType`, e `Uri` sono facoltativi. Gli elementi specificati devono essere elencati in ordine alfabetico.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
`AddTranslationArray` Metodo ha avuto esito positivo. 

Dopo il 31 gennaio 2018, non verranno accettati gli invii di frase. Il servizio risponderà con un codice di errore 410.

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410    |`AddTranslation` non è più supportata.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Note sull'implementazione
Suddivide una sezione di testo in frasi e restituisce una matrice che contiene la lunghezza di ogni frase.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valore restituito:** Matrice di interi che rappresenta la lunghezza delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di interi che rappresenta la lunghezza delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

integer

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query| string|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da suddividere in frasi. La dimensione massima del testo è 10.000 caratteri.|query|string|
|language   |(vuoto)    |Richiesto. Stringa che rappresenta il codice di lingua del testo di input.|query|string|
|Authorization|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.   |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500|Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera una matrice di traduzioni per una coppia di lingue specificata dall'archivio e dal motore MT. `GetTranslations` è diverso da `Translate` in quanto restituisce tutte le traduzioni disponibili.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Il corpo della richiesta include l'opzione facoltativa `TranslationOptions` object, che contiene questo formato:

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

Il `TranslateOptions` oggetto contiene i valori nell'elenco seguente. Sono facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.

* `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
* `ContentType`: L'unica opzione supportata e il valore predefinito, è `text/plain`.
* `IncludeMultipleMTAlternatives`: Flag booleano per specificare se più di una alternativa deve essere restituita dal motore del server di destinazione master. I valori validi sono `true` e `false` (maiuscole/minuscole). Il valore predefinito è `false`, che restituisce solo un'alternativa. Impostazione del flag su `true` consente la creazione di alternative artificiali, completamente integrato con Collaborative Translation Framework (CTF). La funzionalità consente di restituire alternative per trovare frasi che non dispone di alcuna traduzione in CTF aggiungendo artificiali alternative dal *n*-elenco migliore del decodificatore.
    - Classificazioni. Le classificazioni vengono applicate come segue: 
         - la miglior traduzione automatica ha una classificazione pari a 5.
       - Le alternative da CTF riflettono l'autorità del revisore. Sono compresi tra -10 a + 10.
       - Generato automaticamente (*n*-migliore) alternative di conversione hanno una classificazione compresa tra 0 e un livello di corrispondenza di 100.
    - Numero di alternative. Il numero di alternative restituite può essere più alto al valore specificato in `maxTranslations`, ma può essere inferiore.
    - Coppie di linguaggi. Questa funzionalità non è disponibile per le conversioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di linguaggi supportate da Microsoft Translator.
* `State`: Stato utente che consente di correlare la richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se è impostato alcun valore, il valore predefinito è `all`.
* `User`: filtrare i risultati in base a questo utente. Se è impostato alcun valore, il valore predefinito è `all`.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** Di seguito è riportato il formato della risposta:

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

La risposta include un `GetTranslationsResponse` elemento che contiene i valori seguenti:

* `Translations`: Trovata una matrice delle corrispondenze, archiviati in `TranslationMatch` oggetti (descritti nella sezione seguente). Le traduzioni possono includere lieve varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate: 100% in primo luogo, corrisponde quindi corrispondenze fuzzy.
* `From`: Se il metodo non è stato specificato un `From` linguaggio, questo valore proverrà dal rilevamento automatico della lingua. In caso contrario, sarà specificato `From` language.
* `State`: Stato utente che consente di correlare la richiesta e risposta. Contiene il valore specificato nel `TranslateOptions` parametro.

Il `TranslationMatch` oggetto è costituito da questi valori:

* `Error`: Il codice di errore, se si verifica un errore per una specifica stringa di input. In caso contrario, questo campo è vuoto.
* `MatchDegree`: indica in quale misura il testo di input corrisponde al testo originale è presente nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito di intervalli da 0 a 100, dove 0 non indica nessun somiglianza e 100 è una corrispondenza tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene utilizzato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. Risultati di traduzione automatica presentano una classificazione pari a 5. Traduzioni in modo anonimo fornite in genere hanno un punteggio compreso tra 1 e 4. Traduzioni in modo autorevole fornite in genere includerà una classificazione da 6 a 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Oggetto `GetTranslationsResponse` oggetto nel formato descritto in precedenza.

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se il `Authorization` oppure `Ocp-Apim-Subscription-Key` intestazione viene usata, lasciare il `appid` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.|query|string|
|text|(vuoto)|Richiesto. Stringa che rappresenta il testo da tradurre. La dimensione massima del testo è 10.000 caratteri.|query|string|
|from|(vuoto)|Richiesto. Stringa che rappresenta il codice di lingua del testo da tradurre.|query|string|
|to |(vuoto)    |Richiesto. Stringa che rappresenta il codice di lingua del linguaggio da tradurre il testo in.|query|string|
|maxTranslations|(vuoto)|Richiesto. Valore intero che rappresenta il numero massimo di traduzioni da restituire.|query|integer|
|Authorization| (vuoto)|Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|string|  intestazione|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Prega di inviarci con la data approssimativa e l'ora della richiesta e con l'ID richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera più candidati per la traduzione di testi sorgente multipli.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Di seguito è riportato il formato del corpo della richiesta:

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

`GetTranslationsArrayRequest` include i seguenti elementi:

* `AppId`: Richiesto. Se il `Authorization` intestazione viene usata, lasciare il `AppId` i campi vuoti. In caso contrario, includere una stringa che contiene `"Bearer" + " " + "access_token"`.
* `From`: Richiesto. Stringa che rappresenta il codice di lingua del testo da tradurre.
* `MaxTranslations`: Richiesto. Valore intero che rappresenta il numero massimo di traduzioni da restituire.
* `Options`: facoltativo. Un `Options` oggetto che contiene i valori seguenti. Sono facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: L'unica opzione supportata e il valore predefinito, è `text/plain`.
    - `IncludeMultipleMTAlternatives`: Flag booleano per specificare se più di una alternativa deve essere restituita dal motore del server di destinazione master. I valori validi sono `true` e `false` (maiuscole/minuscole). Il valore predefinito è `false`, che restituisce solo un'alternativa. Impostazione del flag su `true` abilita la generazione di artificiali alternative per la traduzione, completamente integrato con la collaborazione traduzioni Framework (CTF). La funzionalità consente di restituire alternative per trovare frasi che non dispongono di alcun alternative in CTF aggiungendo artificiali alternative dal *n*-elenco migliore del decodificatore.
        - Le classificazioni delle classificazioni vengono applicate come segue:
          - la miglior traduzione automatica ha una classificazione pari a 5.
          - Le alternative da CTF riflettono l'autorità del revisore. Sono compresi tra -10 a + 10.
          - Generato automaticamente (*n*-migliore) alternative di conversione hanno una classificazione compresa tra 0 e un livello di corrispondenza di 100.
        - Numero di alternative. Il numero di alternative restituite può essere più alto al valore specificato in `maxTranslations`, ma può essere inferiore.
        - Coppie di linguaggi. Questa funzionalità non è disponibile per le conversioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di linguaggi supportate da Microsoft Translator.
* `State`: Stato utente che consente di correlare la richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se è impostato alcun valore, il valore predefinito è `all`.
* `User`: filtrare i risultati in base a questo utente. Se è impostato alcun valore, il valore predefinito è `all`.
* `Texts`: Richiesto. Matrice che contiene il testo per la conversione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da convertire non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 10.
* `To`: Richiesto. Stringa che rappresenta il codice di lingua del linguaggio da tradurre il testo in.

È possibile omettere gli elementi facoltativi. Gli elementi che sono elementi figlio diretti del `GetTranslationsArrayRequest` devono essere elencati in ordine alfabetico.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** Di seguito è riportato il formato della risposta:

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

Ogni `GetTranslationsResponse` elemento contiene questi valori:

* `Translations`: Trovata una matrice delle corrispondenze, archiviati in `TranslationMatch` oggetti (descritti nella sezione seguente). Le traduzioni possono includere lieve varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate: 100% in primo luogo, corrisponde quindi corrispondenze fuzzy.
* `From`: Se il metodo non è stato specificato un `From` linguaggio, questo valore proverrà dal rilevamento automatico della lingua. In caso contrario, sarà specificato `From` language.
* `State`: Stato utente che consente di correlare la richiesta e risposta. Contiene il valore specificato nel `TranslateOptions` parametro.

Il `TranslationMatch` oggetto contiene i valori seguenti:
* `Error`: Il codice di errore, se si verifica un errore per una specifica stringa di input. In caso contrario, questo campo è vuoto.
* `MatchDegree`: indica in quale misura il testo di input corrisponde al testo originale è presente nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito di intervalli da 0 a 100, dove 0 non indica nessun somiglianza e 100 è una corrispondenza tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene utilizzato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. Risultati di traduzione automatica presentano una classificazione pari a 5. Traduzioni in modo anonimo fornite in genere hanno un punteggio compreso tra 1 e 4. Traduzioni in modo autorevole fornite in genere hanno una classificazione da 6 a 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.


### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Value|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization  |(vuoto)    |Obbligatorio se entrambi i `appid` campo e il `Ocp-Apim-Subscription-Key` intestazione viene lasciato vuoto.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se entrambi i `appid` campo e il `Authorization` intestazione viene lasciato vuoto.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|`Reason`|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione alla API traduzione testuale v3 ](../migrate-to-v3.md)


