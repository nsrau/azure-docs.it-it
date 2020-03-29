---
title: API Traduzione testuale v2.0
titleSuffix: Azure Cognitive Services
description: Documentazione di riferimento per l'API Translator Text v2.0.Reference documentation for the Translator Text API v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242490"
---
# <a name="translator-text-api-v20"></a>API Traduzione testuale v2.0

> [!IMPORTANT]
> Questa versione dell'API Traduzione testuale è stata deprecata. [Visualizza la documentazione per la versione 3 dell'API Translator Text](v3-0-reference.md).

La versione 2 dell'API Translator Text può essere integrata senza problemi nelle app, nei siti Web, negli strumenti o in altre soluzioni per offrire esperienze utente in più lingue. È possibile utilizzarlo su qualsiasi piattaforma hardware e con qualsiasi sistema operativo per eseguire la traduzione della lingua e altre attività relative alla lingua, come il rilevamento della lingua del testo e il text to speech, secondo gli standard del settore. Per ulteriori informazioni, consultate [API Testo traduttore](../translator-info-overview.md).

## <a name="getting-started"></a>Introduzione
Per accedere all'API Translator Text, è necessario iscriversi a [Microsoft Azure.](../translator-text-how-to-signup.md)

## <a name="authentication"></a>Authentication 
Tutte le chiamate all'API Translator Text richiedono una chiave di sottoscrizione per l'autenticazione. L'API supporta tre metodi di autenticazione:

- Un token di accesso. Usare la chiave di sottoscrizione per creare un token di accesso effettuando una richiesta POST al servizio di autenticazione. Per informazioni dettagliate, vedere la documentazione del servizio token. Passare il token di accesso al `Authorization` servizio `access_token` Translator usando l'intestazione o il parametro di query. Il token di accesso è valido per 10 minuti. Ottenere un nuovo token di accesso ogni 10 minuti e continuare a usare lo stesso token di accesso per le richieste ripetute durante i 10 minuti.
- Una chiave di sottoscrizione utilizzata direttamente. Passare la chiave di sottoscrizione `Ocp-Apim-Subscription-Key` come valore nell'intestazione inclusa nella richiesta all'API Translator Text. Quando si usa direttamente la chiave di sottoscrizione, non è necessario chiamare il servizio di autenticazione token per creare un token di accesso.
- Una [sottoscrizione multiservizio di Servizi cognitivi di Azure.](https://azure.microsoft.com/pricing/details/cognitive-services/) Questo metodo consente di utilizzare una singola chiave segreta per autenticare le richieste per più servizi.
Quando si usa una chiave segreta per più servizi, è necessario includere due intestazioni di autenticazione con la richiesta. La prima intestazione passa la chiave segreta. La seconda intestazione specifica l'area associata alla sottoscrizione:The second header specifies the region associated with your subscription:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

L'area è necessaria per la sottoscrizione all'API Text multiservizio. L'area selezionata è l'unica area che è possibile utilizzare per la traduzione di testo quando si utilizza la chiave di sottoscrizione multiservizio. Deve essere la stessa area selezionata al momento dell'iscrizione alla sottoscrizione multiservizio nel portale di Azure.It needs to be the same region you selected when you sign up for your multi-service subscription on the Azure portal.

Le aree `australiaeast` `brazilsouth`disponibili `canadacentral` `centralindia`sono `centraluseuap` `eastasia`, `eastus` `eastus2`, `japaneast` `northeurope`, `southcentralus` `southeastasia`, `uksouth` `westcentralus`, `westeurope` `westus`, `westus2`, , , , , , , e .

La chiave di sottoscrizione e il token di accesso sono segreti che devono essere nascosti alla visualizzazione.

## <a name="profanity-handling"></a>Gestione di contenuto volgare
Normalmente, il servizio Translator manterrà la parola volgarità presente nell'origine. Il grado di volgarità e il contesto che rende le parole profane differiscono a seconda della cultura. Quindi il grado di volgarità nella lingua di destinazione potrebbe essere aumentato o ridotto.

Se si desidera impedire volgarità nella traduzione anche quando è nel testo di origine, è possibile utilizzare l'opzione di filtro volgarità per i metodi che lo supportano. L'opzione consente di scegliere se si desidera visualizzare la volgarità eliminata o contrassegnata con i tag appropriati o se si desidera consentire la volgarità nella destinazione. I valori `ProfanityAction` accettati di sono `NoAction` (impostazione predefinita), `Marked`, e `Deleted`.


|ProfanityAction    |Azione |Origine di esempio (giapponese)  |Esempio di traduzione (inglese)  |
|:--|:--|:--|:--|
|NoAction   |Valore predefinito. Equivale a non impostare l'opzione. Il contenuto volgare passerà dall'origine alla destinazione.        |彼はジャッカスです。     |È un cretino.   |
|Marked     |Le parole profane saranno \<racchiuse tra \<tag XML volgarità> e /profanity>.       |彼はジャッカスです。 |È un \<> volgari\<>asino.  |
|Deleted    |Le parole volgari verranno rimosse dall'output senza sostituzione.     |彼はジャッカスです。 |È un.   |

    
## <a name="excluding-content-from-translation"></a>Esclusione di contenuto dalla traduzione
Quando traduci il contenuto`contentType=text/html`con tag, come HTML ( ), a volte è utile escludere contenuto specifico dalla traduzione. È possibile usare l'attributo `class=notranslate` per specificare il contenuto che deve rimanere nella lingua originale. Nell'esempio seguente, il contenuto `div` nel primo elemento non verrà tradotto, ma il contenuto nel secondo `div` elemento verrà tradotto.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Note sull'implementazione
Traduce una stringa di testo da una lingua a un'altra.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valore restituito:** Stringa che rappresenta il testo tradotto.

Se in `AddTranslation` precedenza hai utilizzato o `AddTranslationArray` per inserire una traduzione `Translate` con una valutazione pari o superiore a 5 per la stessa frase di origine, restituisce solo la scelta migliore disponibile per il tuo sistema. "Stessa frase di origine" significa esattamente lo stesso (corrispondenza del 100%), ad eccezione di maiuscole e minuscole, spazi vuoti, valori di tag e punteggiatura alla fine di una frase. Se non viene memorizzata alcuna classificazione con una classificazione pari o superiore a 5, il risultato restituito sarà la traduzione automatica da microsoft Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione    |Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid  |(vuoto)    |Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|text|(vuoto)   |Obbligatorio. Stringa che rappresenta il testo da tradurre. Il testo non può contenere più di 10.000 caratteri.|query|string|
|da|(vuoto)   |Facoltativa. Stringa che rappresenta il codice lingua del testo da tradurre. ad esempio en per "Inglese".|query|string|
|to|(vuoto) |Obbligatorio. Stringa che rappresenta il codice della lingua in cui tradurre il testo.|query|string|
|contentType|(vuoto)    |Facoltativa. Formato del testo da tradurre. I formati `text/plain` supportati sono `text/html`(impostazione predefinita) e . Tutti gli elementi HTML devono essere elementi completi e ben formati.|query|string|
|category|(vuoto)   |Facoltativa. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|Autorizzazione|(vuoto)  |Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|


### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le traduzioni per più testi sorgente.

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

Questi elementi `TranslateArrayRequest`sono in:


* `AppId`: richiesto. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `AppId` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .
* `From`: facoltativo. Stringa che rappresenta il codice lingua del testo da tradurre. Se questo campo viene lasciato vuoto, la risposta includerà il risultato del rilevamento automatico della lingua.
* `Options`: facoltativo. Oggetto `Options` che contiene i valori seguenti. Sono tutti facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: formato del testo da tradurre. I formati `text/plain` supportati sono `text/xml`(impostazione predefinita), e `text/html`. Tutti gli elementi HTML devono essere elementi completi e ben formati.
    - `ProfanityAction`: specifica la modalità di gestione delle volgarità, come spiegato in precedenza. I valori `NoAction` accettati `Marked`sono `Deleted`(impostazione predefinita), , e .
    - `State`: stato utente per correlare la richiesta e la risposta. Lo stesso contenuto verrà restituito nella risposta.
    - `Uri`: filtrare i risultati in base a questo URI. Impostazione predefinita: `all`.
    - `User`: filtrare i risultati in base a questo utente. Impostazione predefinita: `all`.
* `Texts`: richiesto. Matrice che contiene il testo per la traduzione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da tradurre non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 2.000.The maximum number of array elements is 2,000.
* `To`: richiesto. Stringa che rappresenta il codice della lingua in cui tradurre il testo.

È possibile omettere elementi facoltativi. Gli elementi di `TranslateArrayRequest` cui sono figli diretti di devono essere elencati in ordine alfabetico.

Il `TranslateArray` metodo `application/xml` accetta `text/xml` `Content-Type`o per .

**Valore restituito:** matrice `TranslateArrayResponse`. Ognuno `TranslateArrayResponse` ha questi elementi:

* `Error`: indica un errore se si verifica uno. In caso contrario, è impostato su null.
* `OriginalSentenceLengths`: matrice di interi che indica la lunghezza di ogni frase nel testo di origine. La lunghezza della matrice indica il numero di frasi.
* `TranslatedText`: testo tradotto.
* `TranslatedSentenceLengths`: matrice di interi che indica la lunghezza di ogni frase nel testo tradotto. La lunghezza della matrice indica il numero di frasi.
* `State`: stato utente per correlare la richiesta e la risposta. Restituisce lo stesso contenuto della richiesta.

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

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Una risposta corretta include `TranslateArrayResponse` una matrice di matrici nel formato descritto in precedenza.

string

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Autorizzazione|(vuoto)  |Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP   |Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata. Di seguito sono riportati gli errori più comuni. <ul><li>L'elemento della matrice non può essere vuoto.</li><li>Categoria non valida.</li><li>La lingua non è valida.</li><li>La lingua non è valida.</li><li>La richiesta contiene troppi elementi.</li><li>La lingua Da non è supportata.</li><li>La lingua A non è supportata.</li><li>Translate Request ha troppi dati.</li><li>HTML non è in un formato corretto.</li><li>Troppe stringhe sono state passate nella richiesta Translate.</li></ul>|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera i nomi descrittivi per le `languageCodes`lingue passate `locale` come parametro , localizzato nella lingua passata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Il corpo della richiesta include una matrice di stringhe che rappresenta i codici di lingua ISO 639-1 per cui recuperare i nomi descrittivi. Ad esempio:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valore restituito:** Matrice di stringhe contenente i nomi delle lingue supportati dal servizio Translator, localizzati nella lingua richiesta.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe contenente i nomi delle lingue supportati dal servizio Translator, localizzati nella lingua richiesta.

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|locale|(vuoto) |Obbligatorio. Stringa che rappresenta uno dei seguenti elementi, utilizzata per localizzare i nomi delle lingue: <ul><li>Combinazione di un codice di impostazioni cultura a due lettere minuscole ISO 639 associato a una lingua e di un codice di sottolingua a due lettere maiuscole ISO 3166. <li>Un codice di impostazioni cultura ISO 639 minuscolo da solo.|query|string|
|Autorizzazione|(vuoto)  |Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Note sull'implementazione
Ottiene un elenco di codici lingua che rappresentano le lingue supportate dal servizio di traduzione.  `Translate` e `TranslateArray` consentono la traduzione tra due di queste lingue.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valore restituito:** Matrice di stringhe che contiene i codici lingua supportati dal servizio Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe che contiene i codici lingua supportati dal servizio Translator.

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|Autorizzazione|(vuoto)  |Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le lingue disponibili per la sintesi vocale.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valore restituito:** Matrice di stringhe che contiene i codici lingua supportati per la sintesi vocale dal servizio Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe che contiene i codici lingua supportati per la sintesi vocale dal servizio Translator.

string

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|
 
### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Note sull'implementazione
Restituisce un flusso WAV o MP3 del testo passato, parlato nella lingua desiderata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valore restituito:** Flusso WAV o MP3 del testo passato, parlato nella lingua desiderata.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

BINARY

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|text|(vuoto)   |Obbligatorio. Stringa che contiene una o più frasi da pronunciare per il flusso, nella lingua specificata. Il testo non deve superare i 2.000 caratteri.|query|string|
|Linguaggio|(vuoto)   |Obbligatorio. Stringa che rappresenta il codice lingua supportato della lingua in cui pronunciare il testo. Il codice deve essere uno dei codici `GetLanguagesForSpeak`restituiti dal metodo .|query|string|
|format|(vuoto)|Facoltativa. Stringa che specifica l'ID del tipo di contenuto. Attualmente, sono disponibili `audio/wav` e `audio/mp3`. Il valore predefinito è `audio/wav`.|query|string|
|opzioni|(vuoto)    |Facoltativa. Una stringa che specifica le proprietà del riconoscimento vocale sintetizzato:<ul><li>`MaxQuality`e `MinSize` specificare la qualità del segnale audio. `MaxQuality`fornisce la massima qualità. `MinSize`fornisce la dimensione minima del file. Il valore `MinSize`predefinito è .</li><li>`female`e `male` specificare il sesso desiderato della voce. Il valore predefinito è `female`. Utilizzare la barra<code>\|</code>verticale ( ) per includere più opzioni. Ad esempio, `MaxQuality|Male`.</li></li></ul>  |query|string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Note sull'implementazione
Identifica la lingua di una sezione di testo.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valore restituito:** Stringa che contiene un codice lingua a due caratteri per il testo.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|text|(vuoto)|Obbligatorio. Stringa contenente testo la cui lingua deve essere identificata. Il testo non deve superare i 10.000 caratteri.|query|  string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key  |(vuoto)    |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Note sull'implementazione

Identifica le lingue in una matrice di stringhe. Rileva in modo indipendente il linguaggio di ogni singolo elemento della matrice e restituisce un risultato per ogni riga della matrice.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Di seguito è riportato il formato del corpo della richiesta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Il testo non può superare i 10.000 caratteri.

**Valore restituito:** Matrice di stringhe che contiene un codice di linguaggio a due caratteri per ogni riga della matrice di input.

Ecco il formato del corpo della risposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
`DetectArray`ha avuto successo. Restituisce una matrice di stringhe che contiene un codice di linguaggio a due caratteri per ogni riga della matrice di input.

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di deprecazione:** Dopo il 31 gennaio 2018, questo metodo non accetterà nuovi invii di frasi. Verrà visualizzato un messaggio di errore. Vedere l'annuncio sulle modifiche al Quadro di traduzione collaborativa (CTF).

Aggiunge una traduzione alla memoria di traduzione.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto risposta: application: xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati   |
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|originalText|(vuoto)|Obbligatorio. Stringa contenente il testo da tradurre. La lunghezza massima della stringa è 1.000 caratteri.|query|string|
|translatedText|(vuoto) |Obbligatorio. Stringa contenente testo tradotto nella lingua di destinazione. La lunghezza massima della stringa è 2.000 caratteri.|query|string|
|da|(vuoto)   |Obbligatorio. Stringa che rappresenta il codice lingua della lingua originale del testo. Ad esempio, en per l'inglese e de per il tedesco.|query|string|
|to|(vuoto)|Obbligatorio. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.|query|string|
|rating|(vuoto) |Facoltativa. Intero che rappresenta la classificazione di qualità per la stringa. Il valore è compreso tra -10 e 10. Il valore predefinito è 1.|query|integer|
|contentType|(vuoto)    |Facoltativa. Formato del testo da tradurre. I formati `text/plain` supportati `text/html`sono e . Tutti gli elementi HTML devono essere elementi completi e ben formati.    |query|string|
|category|(vuoto)|Facoltativa. Stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.|query|string|
|utente|(vuoto)|Obbligatorio. Stringa utilizzata per tenere traccia dell'iniziatore dell'invio.|query|string|
|Uri|(vuoto)|Facoltativa. Stringa contenente il percorso del contenuto della traduzione.|query|string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.  |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410|`AddTranslation` non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **Nota di deprecazione:** Dopo il 31 gennaio 2018, questo metodo non accetterà nuovi invii di frasi. Verrà visualizzato un messaggio di errore. Vedere l'annuncio sulle modifiche al Quadro di traduzione collaborativa (CTF).

Aggiunge una matrice di traduzioni alla memoria di traduzione. Questo metodo è una `AddTranslation`versione in forma di matrice di .

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

Questi elementi `AddtranslationsRequest`sono in:

* `AppId`: richiesto. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `AppId` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .
* `From`: richiesto. Stringa contenente il codice lingua del linguaggio di origine. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `To`: richiesto. Stringa contenente il codice lingua della lingua di destinazione. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `Translations`: richiesto. Matrice di traduzioni da aggiungere alla memoria di traduzione. Ogni traduzione `OriginalText` `TranslatedText`deve `Rating`contenere , , e . La dimensione massima `OriginalText` `TranslatedText` di ciascuno e 1.000 caratteri. Il totale `OriginalText` di `TranslatedText` tutti gli elementi non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 100.
* `Options`: richiesto. Un insieme di `Category`opzioni, `Uri`tra `User`cui , `ContentType`, e . `User` è obbligatorio. `Category`, `ContentType`, `Uri` e sono facoltativi. Gli elementi specificati devono essere elencati in ordine alfabetico.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
`AddTranslationArray`metodo riuscito. 

Dopo il 31 gennaio 2018, le condanne non saranno accettate. Il servizio risponderà con un codice di errore 410.

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410    |`AddTranslation` non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Note sull'implementazione
Suddivide una sezione di testo in frasi e restituisce una matrice che contiene le lunghezze di ogni frase.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valore restituito:** Matrice di interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice rappresenta il numero di frasi. I valori rappresentano la lunghezza di ogni frase.

integer

Tipo di contenuto risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query| string|
|text|(vuoto)   |Obbligatorio. Stringa che rappresenta il testo da dividere in frasi. La dimensione massima del testo è di 10.000 caratteri.|query|string|
|Linguaggio   |(vuoto)    |Obbligatorio. Stringa che rappresenta il codice della lingua del testo di input.|query|string|
|Autorizzazione|(vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.   |intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500|Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera una matrice di traduzioni per una coppia di lingue specificata dall'archivio e dal motore MT. `GetTranslations`differisce `Translate` da in quanto restituisce tutte le traduzioni disponibili.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Il corpo della richiesta `TranslationOptions` include l'oggetto facoltativo, che ha il seguente formato:

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

* `Category`: stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
* `ContentType`: l'unica opzione supportata `text/plain`e l'impostazione predefinita sono .
* `IncludeMultipleMTAlternatives`: flag booleano per specificare se devono essere restituite più alternative dal motore MT. I valori `true` `false` validi sono e (maiuscole/minuscole). Il valore `false`predefinito è , che restituisce una sola alternativa. L'impostazione `true` del flag consente la creazione di alternative artificiali, completamente integrate con il framework di traduzione collaborativa (CTF). La funzione consente di restituire alternative per frasi che non hanno traduzioni in CTF aggiungendo alternative artificiali dall'elenco *n-miglior*del decodificatore.
    - Voti. Le valutazioni sono applicate in questo modo: 
         - la miglior traduzione automatica ha una classificazione pari a 5.
       - Le alternative del CTF riflettono l'autorità del revisore. Essi vanno da -10 a 10 dollari.
       - Le alternative di traduzione generate automaticamente (*n*-best) hanno una valutazione di 0 e un grado di corrispondenza di 100.
    - Numero di alternative. Il numero di alternative restituite può essere alto `maxTranslations`quanto il valore specificato in , ma può essere inferiore.
    - Coppie linguistiche. Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: stato utente per correlare la richiesta e la risposta. Lo stesso contenuto verrà restituito nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non è impostato alcun `all`valore, il valore predefinito è .
* `User`: filtrare i risultati in base a questo utente. Se non è impostato alcun `all`valore, il valore predefinito è .

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

Questa risposta `GetTranslationsResponse` include un elemento che contiene i valori seguenti:This response includes a element that contains the following values:

* `Translations`: matrice delle corrispondenze trovate, memorizzate negli `TranslationMatch` oggetti (descritte nella sezione seguente). Le traduzioni potrebbero includere lievi varianti del testo originale (corrispondenza fuzzy). Le traduzioni saranno ordinate: 100% corrisponde prima, partite fuzzy prossime.
* `From`: se il metodo non `From` specifica una lingua, questo valore proviene dal rilevamento automatico della lingua. In caso contrario, `From` sarà la lingua specificata.
* `State`: stato utente per correlare la richiesta e la risposta. Contiene il valore fornito `TranslateOptions` nel parametro.

L'oggetto `TranslationMatch` è costituito dai seguenti valori:

* `Error`: il codice di errore, se si verifica un errore per una stringa di input specifica. In caso contrario, questo campo è vuoto.
* `MatchDegree`: indica quanto il testo di input corrisponde al testo originale trovato nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito è compreso tra 0 e 100, dove 0 non è una somiglianza e 100 è una corrispondenza esatta con distinzione tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene utilizzato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica hanno una valutazione di 5. Le traduzioni fornite in forma anonima hanno generalmente una valutazione da 1 a 4. Le traduzioni fornite in modo autorevole avranno generalmente una valutazione da 6 a 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Oggetto `GetTranslationsResponse` nel formato descritto in precedenza.

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Obbligatorio. Se `Authorization` viene `Ocp-Apim-Subscription-Key` utilizzata l'intestazione o , lasciare vuoto il `appid` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .|query|string|
|text|(vuoto)|Obbligatorio. Stringa che rappresenta il testo da tradurre. La dimensione massima del testo è di 10.000 caratteri.|query|string|
|da|(vuoto)|Obbligatorio. Stringa che rappresenta il codice lingua del testo da tradurre.|query|string|
|to |(vuoto)    |Obbligatorio. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.|query|string|
|maxTranslations|(vuoto)|Obbligatorio. Intero che rappresenta il numero massimo di traduzioni da restituire.|query|integer|
|Autorizzazione| (vuoto)|Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|string|  intestazione|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, Ti preghiamo di fornirci la data approssimativa & ora della `X-MS-Trans-Info`richiesta e l'ID della richiesta incluso nell'intestazione della risposta.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera più candidati di traduzione per più testi di origine.

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

`GetTranslationsArrayRequest`include questi elementi:

* `AppId`: richiesto. Se `Authorization` viene utilizzata l'intestazione, lasciare vuoto il `AppId` campo. In caso contrario, `"Bearer" + " " + "access_token"`includere una stringa che contiene .
* `From`: richiesto. Stringa che rappresenta il codice lingua del testo da tradurre.
* `MaxTranslations`: richiesto. Intero che rappresenta il numero massimo di traduzioni da restituire.
* `Options`: facoltativo. Oggetto `Options` che contiene i valori seguenti. Sono tutti facoltativi e predefiniti per le impostazioni più comuni. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: stringa che contiene la categoria (dominio) della traduzione. Il valore predefinito è `general`.
    - `ContentType`: l'unica opzione supportata `text/plain`e l'impostazione predefinita sono .
    - `IncludeMultipleMTAlternatives`: flag booleano per specificare se devono essere restituite più alternative dal motore MT. I valori `true` `false` validi sono e (maiuscole/minuscole). Il valore `false`predefinito è , che restituisce una sola alternativa. L'impostazione `true` del flag per la generazione di alternative artificiali nella traduzione, completamente integrata con il Collaborative Translations Framework (CTF). La funzione consente di restituire alternative per frasi che non hanno alternative in CTF aggiungendo alternative artificiali dall'elenco *n-miglior*del decodificatore.
        - Valutazioni Le valutazioni vengono applicate in questo modo:
          - la miglior traduzione automatica ha una classificazione pari a 5.
          - Le alternative del CTF riflettono l'autorità del revisore. Essi vanno da -10 a 10 dollari.
          - Le alternative di traduzione generate automaticamente (*n*-best) hanno una valutazione di 0 e un grado di corrispondenza di 100.
        - Numero di alternative. Il numero di alternative restituite può essere alto `maxTranslations`quanto il valore specificato in , ma può essere inferiore.
        - Coppie linguistiche. Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale, in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: stato utente per correlare la richiesta e la risposta. Lo stesso contenuto verrà restituito nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non è impostato alcun `all`valore, il valore predefinito è .
* `User`: filtrare i risultati in base a questo utente. Se non è impostato alcun `all`valore, il valore predefinito è .
* `Texts`: richiesto. Matrice che contiene il testo per la traduzione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutto il testo da tradurre non può superare i 10.000 caratteri. Il numero massimo di elementi della matrice è 10.
* `To`: richiesto. Stringa che rappresenta il codice lingua della lingua in cui tradurre il testo.

È possibile omettere elementi facoltativi. Gli elementi di `GetTranslationsArrayRequest` cui sono figli diretti di devono essere elencati in ordine alfabetico.

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

Ogni `GetTranslationsResponse` elemento contiene i seguenti valori:

* `Translations`: matrice delle corrispondenze trovate, memorizzate negli `TranslationMatch` oggetti (descritte nella sezione seguente). Le traduzioni potrebbero includere lievi varianti del testo originale (corrispondenza fuzzy). Le traduzioni saranno ordinate: 100% corrisponde prima, partite fuzzy prossime.
* `From`: se il metodo non `From` specifica una lingua, questo valore proviene dal rilevamento automatico della lingua. In caso contrario, `From` sarà la lingua specificata.
* `State`: stato utente per correlare la richiesta e la risposta. Contiene il valore fornito `TranslateOptions` nel parametro.

L'oggetto `TranslationMatch` contiene i seguenti valori:
* `Error`: il codice di errore, se si verifica un errore per una stringa di input specifica. In caso contrario, questo campo è vuoto.
* `MatchDegree`: indica quanto il testo di input corrisponde al testo originale trovato nell'archivio. il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte. Il valore restituito è compreso tra 0 e 100, dove 0 non è una somiglianza e 100 è una corrispondenza esatta con distinzione tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Questo valore viene restituito solo se il testo originale corrispondente è diverso dal testo di input. Viene utilizzato per restituire il testo di origine di una corrispondenza fuzzy. Questo valore non viene restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica hanno una valutazione di 5. Le traduzioni fornite in forma anonima hanno generalmente una valutazione da 1 a 4. Le traduzioni fornite in modo autorevole hanno generalmente una valutazione da 6 a 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore è 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.


### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

string

Tipo di contenuto risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|valore|Descrizione|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Autorizzazione  |(vuoto)    |Obbligatorio se `appid` sia il `Ocp-Apim-Subscription-Key` campo che l'intestazione vengono lasciati vuoti.  Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|string|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se `appid` sia il `Authorization` campo che l'intestazione vengono lasciati vuoti.|intestazione|string|

### <a name="response-messages"></a>Messaggi di risposta

|Stato codice HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione all'API Translator Text v3Migrate to Translator Text API v3](../migrate-to-v3.md)


