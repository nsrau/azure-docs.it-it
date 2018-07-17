---
title: Informazioni di riferimento sull'API Traduzione testuale Microsoft V2.0 | Microsoft Docs
titleSuffix: Cognitive Services
description: Documentazione di riferimento per l'API Traduzione testuale Microsoft V2.0.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377761"
---
# <a name="translator-text-api-v20"></a>API Traduzione testuale v2.0

> [!IMPORTANT]
> Questa versione dell'API Traduzione testuale è stata deprecata. [Visualizzare la documentazione per la versione 3 dell'API Traduzione testuale](v3-0-reference.md).

L'API Traduzione testuale Microsoft V2 può essere facilmente integrata in applicazioni, siti Web, strumenti o altre soluzioni per assicurare esperienze utente multilingue. Sfruttando gli standard del settore, può essere usata in qualsiasi piattaforma hardware e con qualunque sistema operativo per eseguire la traduzione e altre operazioni relative alla lingua, ad esempio il rilevamento della lingua del testo o la sintesi vocale. Fare clic qui per altre informazioni sull'API di Microsoft Translator.

## <a name="getting-started"></a>Introduzione
Per accedere all'API Traduzione testuale Microsoft, è necessario [registrarsi per Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Authorization
Tutte le chiamate all'API Traduzione testuale Microsoft richiedono una chiave di sottoscrizione per l'autenticazione. L'API supporta due modalità di autenticazione:

* Tramite un token di accesso. Usare la chiave di sottoscrizione a cui si fa riferimento nel **passaggio** 9 per generare un token di accesso tramite una richiesta POST al servizio di autorizzazione. Per informazioni dettagliate, vedere la documentazione del servizio token. Passare il token di accesso al servizio Translator usando l'intestazione Authorization o il parametro di query access_token. Il token di accesso è valido per 10 minuti. Ottenere un nuovo token di accesso ogni 10 minuti e continuare a usare lo stesso token di accesso per le richieste ripetute entro 10 minuti.

* Direttamente tramite una chiave di sottoscrizione. Passare la chiave di sottoscrizione come valore nell'intestazione `Ocp-Apim-Subscription-Key` inclusa nella richiesta all'API del servizio Translator. In questa modalità non è necessario chiamare il servizio token di autenticazione per generare un token di accesso.

Considerare la chiave di sottoscrizione e il token di accesso informazioni riservate da tenere nascoste e non visualizzare.

## <a name="profanity-handling"></a>Gestione di contenuto volgare
In genere, il servizio Translator manterrà il contenuto volgare presente nell'origine della traduzione. Il grado di volgarità e il contesto che rende volgare una determinata parola sono diversi da una cultura all'altra, pertanto il grado di volgarità nella lingua di destinazione può essere amplificato o ridotto.

Se si vuole evitare la presenza di contenuto volgare nella traduzione, indipendentemente dalla presenza di contenuto volgare nel testo di origine, è possibile usare l'opzione di filtro per il contenuto volgare per i metodi che la supportano. L'opzione consente di scegliere se si vuole cancellare il contenuto volgare o contrassegnarlo con tag appropriati oppure non eseguire alcuna operazione. I valori consentiti di `ProfanityAction` sono `NoAction` (impostazione predefinita), Marked e `Deleted`.


|ProfanityAction    |Azione |Origine di esempio (giapponese)  |Traduzione di esempio (italiano)  |
|:--|:--|:--|:--|
|NoAction   |Default. Equivale a non impostare l'opzione. Il contenuto volgare passerà dall'origine alla destinazione.        |彼はジャッカスです。     |È un cretino.   |
|Marked     |Le parole volgari verranno racchiuse tra i tag XML <profanity> e </profanity>.     |彼はジャッカスです。 |È un <profanity>cretino</profanity>.    |
|Deleted    |Le parole volgari verranno rimosse dall'output senza sostituzione.     |彼はジャッカスです。 |È un.   |

    
## <a name="excluding-content-from-translation"></a>Esclusione di contenuto dalla traduzione
Durante la traduzione di contenuto con tag, ad esempio HTML (`contentType=text/html`), a volte è utile escludere contenuti specifici dalla traduzione. È possibile usare l'attributo `class=notranslate` per specificare il contenuto che deve rimanere nella lingua originale. Nell'esempio seguente il contenuto all'interno del primo elemento `div` non verrà tradotto, mentre il contenuto nel secondo elemento `div` verrà tradotto.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Note sull'implementazione
Traduce una stringa di testo da una lingua a un'altra.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valore restituito:** stringa che rappresenta il testo tradotto.

Se in precedenza è stato usato `AddTranslation` o `AddTranslationArray` per immettere una traduzione con una classificazione uguale o maggiore di 5 per la stessa frase di origine, `Translate` restituisce solo la scelta superiore disponibile nel sistema. Con "stessa frase di origine" si intende esattamente la stessa (corrispondenza al 100%), ad eccezione di maiuscole/minuscole, spazi, valori dei tag e punteggiatura alla fine di una frase. Se non è presente alcuna classificazione uguale o maggiore di 5, il risultato restituito sarà la traduzione automatica da parte di Microsoft Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

stringa

Tipo di contenuto della risposta: application/xml 

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE    |Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid  |(vuoto)    |Richiesto. Se si usa l'intestazione Authorization o Ocp-Apim-Subscription-Key, lasciare vuoto il campo appid, altrimenti includere una stringa contenente "Bearer" + " " + "access_token".|query|stringa|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da tradurre. Le dimensioni del testo non devono superare 10.000 caratteri.|query|stringa|
|from|(vuoto)   |facoltativo. Stringa che rappresenta il codice lingua del testo della traduzione, ad esempio en per "Inglese".|query|stringa|
|to|(vuoto) |Richiesto. Stringa che rappresenta il codice della lingua in cui tradurre il testo.|query|stringa|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono text/plain (impostazione predefinita) e text/html. Qualsiasi codice HTML deve essere un elemento completo ben formato.|query|stringa|
|category|(vuoto)   |facoltativo. Stringa contenente la categoria (dominio) della traduzione. L'impostazione predefinita è "general".|query|stringa|
|Authorization|(vuoto)  |Obbligatorio se non è specificato il campo appid o l'intestazione Ocp-Apim-Subscription-Key. Token di autorizzazione: "Bearer" + " " + "access_token".|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se non è specificato il campo appid o l'intestazione Authorization.|intestazione|stringa|


### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Note sull'implementazione
Usare il metodo `TranslateArray` per recuperare le traduzioni per più testi di origine.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Il formato del corpo della richiesta deve essere come segue:

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

Gli elementi all'interno di `TranslateArrayRequest` sono:


* `appid`: elemento obbligatorio. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.
* `from`: facoltativo. Stringa che rappresenta il codice della lingua da cui tradurre il testo. Se lasciato vuoto, la risposta includerà il risultato del rilevamento automatico della lingua.
* `options`: facoltativo. Oggetto `Options` contenente i valori elencati di seguito. Sono tutti facoltativi e con le impostazioni più comuni per impostazione predefinita. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - `Category`: stringa contenente la categoria (dominio) della traduzione. L'impostazione predefinita è `general`.
    - `ContentType`: formato del testo da tradurre. I formati supportati sono `text/plain` (impostazione predefinita), `text/xml` e `text/html`. Qualsiasi codice HTML deve essere un elemento completo ben formato.
    - `ProfanityAction`: specifica come viene gestito il contenuto volgare come descritto in precedenza. I valori consentiti di `ProfanityAction` sono `NoAction` (impostazione predefinita), `Marked` e `Deleted`.
    - `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
    - `Uri`: filtrare i risultati in base a questo URI. Impostazione predefinita: `all`.
    - `User`: filtrare i risultati in base a questo utente. Impostazione predefinita: `all`.
* `texts`: elemento obbligatorio. Matrice contenente i testi per la traduzione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutti i testi da tradurre non deve superare 10.000 caratteri. Il numero massimo di elementi della matrice è 2000.
* `to`: elemento obbligatorio. Stringa che rappresenta il codice della lingua in cui tradurre il testo.

Gli elementi facoltativi possono essere omessi. Gli elementi che sono elementi figlio diretti di TranslateArrayRequest devono essere elencati in ordine alfabetico.

Il metodo TranslateArray accetta `application/xml` o `text/xml` per `Content-Type`.

**Valore restituito:** matrice `TranslateArrayResponse`. Ogni `TranslateArrayResponse` presenta gli elementi seguenti:

* `Error`: indica un errore, nel caso in cui se ne sia verificato uno. In caso contrario, è impostato su null.
* `OriginalSentenceLengths`: matrice di interi che indica la lunghezza di ogni frase nel testo di origine originale. La lunghezza della matrice indica il numero di frasi.
* `TranslatedText`: testo tradotto.
* `TranslatedSentenceLengths`: matrice di interi che indica la lunghezza di ogni frase nel testo tradotto. La lunghezza della matrice indica il numero di frasi.
* `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Restituisce lo stesso contenuto della richiesta.

Il formato del corpo della risposta è quello indicato di seguito.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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
Una risposta corretta include una matrice di `TranslateArrayResponse` nel formato descritto in precedenza.

stringa

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto) |Obbligatorio se non è specificato il campo appid o l'intestazione Ocp-Apim-Subscription-Key. Token di autorizzazione: "Bearer" + " " + "access_token".|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione Authorization.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP   |Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata. Di seguito sono riportati gli errori più comuni. <ul><li>L'elemento della matrice non può essere vuoto</li><li>Categoria non valida</li><li>La lingua di origine non è valida</li><li>La lingua di destinazione non è valida</li><li>La richiesta contiene troppi elementi</li><li>La lingua di origine non è supportata</li><li>La lingua di destinazione non è supportata</li><li>La richiesta di traduzione contiene troppi dati</li><li>Il formato HTML non è corretto</li><li>È stato passato un numero eccessivo di stringhe nella richiesta di traduzione</li></ul>|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera i nomi descrittivi per le lingue passate come parametro `languageCodes` e localizzate usando la lingua delle impostazioni locali passata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Il corpo della richiesta include una matrice di stringhe che rappresenta i codici delle lingue ISO 639-1 per cui recuperare i nomi descrittivi. Ad esempio: 

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valore restituito:** matrice di stringhe contenente i nomi delle lingue supportate dal servizio Translator, localizzati nella lingua richiesta.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe contenente i nomi delle lingue supportate dal servizio Translator, localizzati nella lingua richiesta.

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|locale|(vuoto) |Richiesto. Stringa che rappresenta una combinazione di un codice ISO 639 di impostazioni cultura a due lettere minuscole associato a una lingua e un codice ISO 3166 di impostazioni cultura secondarie a due lettere maiuscole per localizzare i nomi delle lingue o semplicemente un codice ISO 639 di impostazioni cultura a lettere minuscole.|query|stringa|
|Authorization|(vuoto)  |Obbligatorio se non è specificato il campo appid o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se non è specificato il campo appid o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Note sull'implementazione
Ottenere un elenco di codici di lingua che rappresenta le lingue supportate dal servizio Translator.  `Translate` e `TranslateArray` consentono la traduzione tra due di queste lingue.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valore restituito:** matrice di stringhe contenente i codici delle lingue supportate dai servizi Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe contenente i codici delle lingue supportate dai servizi Translator.

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|Authorization|(vuoto)  |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera le lingue disponibili per la sintesi vocale.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valore restituito:** matrice di stringhe contenente i codici delle lingue supportate per la sintesi vocale dal servizio Translator.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di stringhe contenente i codici delle lingue supportate per la sintesi vocale dal servizio Translator.

stringa

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|
 
### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Note sull'implementazione
Restituisce un audio o un flusso mp3 del testo passato pronunciato nella lingua desiderata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valore restituito**: audio o flusso mp3 del testo passato pronunciato nella lingua desiderata.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

binary

Tipo di contenuto della risposta: application/xml 

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|text|(vuoto)   |Richiesto. Stringa contenente una o più frase della lingua specificata da pronunciare per il flusso audio. Le dimensioni del testo da pronunciare non devono superare 2000 caratteri.|query|stringa|
|Linguaggio|(vuoto)   |Richiesto. Stringa che rappresenta il codice della lingua supportata in cui pronunciare il testo. Il codice deve essere presente nell'elenco dei codici restituiti dal metodo `GetLanguagesForSpeak`.|query|stringa|
|format|(vuoto)|facoltativo. Stringa che specifica l'ID del tipo di contenuto. Attualmente, sono disponibili `audio/wav` e `audio/mp3`. Il valore predefinito è `audio/wav`.|query|stringa|
|options|(vuoto)    |<ul><li>facoltativo. Stringa che specifica le proprietà della sintesi vocale:<li>`MaxQuality` e `MinSize` sono disponibili per specificare la qualità dei segnali audio. Con `MaxQuality`, è possibile ottenere voci con la massima qualità, mentre con `MinSize` è possibile ottenere voci con dimensioni minime. Il valore predefinito è `MinSize`.</li><li>`female` e `male` sono disponibili per specificare il genere della voce desiderato. Il valore predefinito è `female`. Usare la barra verticale `|` to include multiple options. For example  `MaxQuality|Male`.</li></li></ul> |query|stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Note sull'implementazione
Usare il metodo `Detect` per identificare la lingua di una parte di testo selezionata.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valore restituito:** stringa contenente un codice di lingua di due caratteri per il testo specificato. .

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

stringa

Tipo di contenuto della risposta: application/xml

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|text|(vuoto)|Richiesto. Stringa contenente il testo di cui identificare la lingua. Le dimensioni del testo non devono superare 10.000 caratteri.|query| stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key  |(vuoto)    |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Note sull'implementazione
Usare il metodo `DetectArray` per identificare la lingua della matrice di stringhe in una sola volta. Esegue il rilevamento indipendente di ogni singolo elemento della matrice e restituisce un risultato per ogni riga della matrice.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Il formato del corpo della richiesta deve essere come segue.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Le dimensioni del testo non devono superare 10.000 caratteri.

**Valore restituito:** matrice di stringhe contenente codici di lingua di due caratteri per ogni riga della matrice di input.

Il formato del corpo della risposta è quello indicato di seguito.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
DetectArray ha avuto esito positivo. Restituisce una matrice di stringhe contenente codici di lingua di due caratteri per ogni riga della matrice di input.

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione Authorization.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **NOTA SULLA DEPRECAZIONE:** dopo il 31 gennaio 2018, questo metodo non accetterà nuovi invii di frasi e verrà visualizzato un messaggio di errore. Fare riferimento a questo annuncio sulle modifiche delle funzioni di Collaborative Translation Framework.

Aggiunge una traduzione alla memoria di traduzione.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

stringa

Tipo di contenuto della risposta: application: xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati   |
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|originalText|(vuoto)|Richiesto. Stringa contenente il testo da cui tradurre. La stringa può avere una lunghezza massima di 1000 caratteri.|query|stringa|
|translatedText|(vuoto) |Richiesto. Stringa contenente il testo tradotto nella lingua di destinazione. La stringa può avere una lunghezza massima di 2000 caratteri.|query|stringa|
|from|(vuoto)   |Richiesto. Stringa che rappresenta il codice lingua del testo della traduzione, ad esempio en = inglese, de = tedesco e così via.|query|stringa|
|to|(vuoto)|Richiesto. Stringa che rappresenta il codice della lingua in cui tradurre il testo.|query|stringa|
|rating|(vuoto) |facoltativo. Intero che rappresenta la classificazione di qualità per questa stringa. Il valore è compreso tra -10 e 10. Assume il valore predefinito 1.|query|numero intero|
|contentType|(vuoto)    |facoltativo. Formato del testo da tradurre. I formati supportati sono "text/plain" e "text/html". Qualsiasi codice HTML deve essere un elemento completo ben formato.   |query|stringa|
|category|(vuoto)|facoltativo. Stringa contenente la categoria (dominio) della traduzione. L'impostazione predefinita è "general".|query|stringa|
|user|(vuoto)|Richiesto. Stringa usata per rilevare l'iniziatore dell'invio.|query|stringa|
|Uri|(vuoto)|facoltativo. Stringa contenente il percorso del contenuto di questa traduzione.|query|stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.    |intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410|AddTranslation non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Note sull'implementazione

> [!IMPORTANT]
> **NOTA SULLA DEPRECAZIONE:** dopo il 31 gennaio 2018, questo metodo non accetterà nuovi invii di frasi e verrà visualizzato un messaggio di errore. Fare riferimento a questo annuncio sulle modifiche delle funzioni di Collaborative Translation Framework.

Aggiunge una matrice di traduzioni alla memoria di traduzione. Si tratta di una versione di matrice di `AddTranslation`.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Il formato del corpo della richiesta è quello indicato di seguito.

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

Gli elementi all'interno dell'elemento AddtranslationsRequest sono:

* `AppId`: elemento obbligatorio. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.
* `From`: elemento obbligatorio. Stringa contenente il codice lingua della lingua di origine. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `To`: elemento obbligatorio. Stringa contenente il codice lingua della lingua di destinazione. Deve essere una delle lingue restituite dal metodo `GetLanguagesForTranslate`.
* `Translations`: elemento obbligatorio. Matrice di traduzioni da aggiungere alla memoria di traduzione. Ogni traduzione deve contenere: originalText, translatedText e classificazione. La dimensione di ogni elemento originalText e translatedText è limitata a 1000 caratteri. Il totale di tutti gli elementi originalText e translatedText non deve superare 10.000 caratteri. Il numero massimo di elementi della matrice è 100.
* `Options`: elemento obbligatorio. Set di opzioni, tra cui Category, ContentType, Uri e User. User è obbligatorio. Category, ContentType e Uri sono facoltativi. Gli elementi specificati devono essere elencati in ordine alfabetico.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Il metodo AddTranslationArray ha avuto esito positivo. Dopo il 31 gennaio 2018, gli invii di frase non verranno accettati. Il servizio risponderà con un codice di errore 410.

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione Ocp-Apim-Subscription-Key. Token di autorizzazione: "Bearer" + " " + "access_token".|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione Authorization.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|410    |AddTranslation non è più supportato.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Note sull'implementazione
Suddivide una parte di testo in frasi e restituisce una matrice contenente le lunghezze in ogni frase.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valore restituito**: matrice di interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice è il numero di frasi e i valori sono la lunghezza di ogni frase.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Matrice di interi che rappresenta le lunghezze delle frasi. La lunghezza della matrice è il numero di frasi e i valori sono la lunghezza di ogni frase.

numero intero

Tipo di contenuto della risposta: application/xml 

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)  |Richiesto. Se si usa l'intestazione Authorization o Ocp-Apim-Subscription-Key, lasciare vuoto il campo appid, altrimenti includere una stringa contenente "Bearer" + " " + "access_token".|query| stringa|
|text|(vuoto)   |Richiesto. Stringa che rappresenta il testo da dividere in frasi. Le dimensioni del testo non devono superare 10.000 caratteri.|query|stringa|
|Linguaggio   |(vuoto)    |Richiesto. Stringa che rappresenta il codice lingua del testo di input.|query|stringa|
|Authorization|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione Ocp-Apim-Subscription-Key. Token di autorizzazione: "Bearer" + " " + "access_token".    |intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se non è specificato il campo appid o l'intestazione Authorization.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400|Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401|Credenziali non valide.|
|500|Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta X-MS-Trans-Info.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Note sull'implementazione
Recupera una matrice di traduzioni per una coppia di lingue specificata dall'archivio e dal motore MT. GetTranslations è diverso da Translate in quanto restituisce tutte le traduzioni disponibili.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Il corpo della richiesta include l'oggetto TranslationOptions facoltativo, che ha il formato seguente.

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

L'oggetto `TranslateOptions` contiene i valori elencati di seguito. Sono tutti facoltativi e con le impostazioni più comuni per impostazione predefinita. Gli elementi specificati devono essere elencati in ordine alfabetico.

* `Category`: stringa contenente la categoria (dominio) della traduzione. L'impostazione predefinita è "general".
* `ContentType`: l'unica opzione supportata e l'impostazione predefinita è "text/plain".
* `IncludeMultipleMTAlternatives`: flag booleano per determinare se devono essere restituite più alternative dal motore MT. I valori validi sono true e false (con distinzione tra maiuscole/minuscole). Il valore predefinito è false e include una sola alternativa. L'impostazione del flag su true consente di generare alternative artificiali nella traduzione, completamente integrate con Collaborative Translation Framework (CTF). La funzionalità consente la restituzione di alternative per le frasi che non hanno alternative in CTF, aggiungendo alternative artificiali dall'elenco delle N migliori del decodificatore.
    - Classificazioni Le classificazioni vengono applicate come segue: 1) la traduzione automatica migliore ha una classificazione pari a 5. 2) Le alternative di CTF riflettono l'autorità del revisore, da -10 a + 10. 3) Le alternative di traduzione (N migliori) generate automaticamente hanno una classificazione pari a 0 e un livello di corrispondenza di 100.
    - Numero di alternative Il numero di alternative restituite è fino a maxTranslations, ma può essere inferiore.
    - Coppie di lingue Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non si imposta alcun valore, il valore predefinito è all.
* `User`: filtrare i risultati in base a questo utente. Se non si imposta alcun valore, il valore predefinito è all.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** il formato della risposta è quello indicato di seguito.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Include un elemento `GetTranslationsResponse` contenente i valori seguenti:

* `Translations`: matrice di corrispondenze trovate, archiviate in oggetti TranslationMatch (vedere di seguito). Le traduzioni possono includere leggere varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate con le corrispondenze al 100% per prime, seguite dalle corrispondenze fuzzy.
* `From`: se il metodo non ha specificato una lingua di origine, questo valore sarà il risultato del rilevamento automatico della lingua. In caso contrario, sarà la lingua di origine specificata.
* `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Contiene lo stesso valore specificato nel parametro TranslateOptions.

L'oggetto TranslationMatch è costituito dai seguenti elementi:

* `Error`: se si è verificato un errore per una stringa di input specifica, il codice di errore viene archiviato. In caso contrario, il campo è vuoto.
* `MatchDegree`: il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte.  MatchDegree indica in quale misura il testo di input corrisponde al testo originale trovato nell'archivio. Il valore restituito è compreso tra 0 e 100, dove 0 indica nessuna somiglianza e 100 è una corrispondenza esatta tra maiuscole e minuscole.
MatchedOriginalText: testo originale corrispondente per questo risultato. Restituito solo se il testo originale corrispondente è diverso dal testo di input. Usato per restituire il testo di origine di una corrispondenza fuzzy. Non restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica avranno una classificazione pari a 5. Le traduzioni fornite in modo anonimo avranno in genere una classificazione da 1 a 4, mentre le traduzioni fornite autorevolmente avranno in genere una classificazione compresa tra 6 e 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore sarà 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.

### <a name="response-class-status-200"></a>Classe di risposta (stato 200)
Oggetto `GetTranslationsResponse` nel formato descritto in precedenza.

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|appid|(vuoto)|Richiesto. Se si usa l'intestazione `Authorization` o `Ocp-Apim-Subscription-Key`, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.|query|stringa|
|text|(vuoto)|Richiesto. Stringa che rappresenta il testo da tradurre. Le dimensioni del testo non devono superare 10.000 caratteri.|query|stringa|
|from|(vuoto)|Richiesto. Stringa che rappresenta il codice lingua del testo della traduzione.|query|stringa|
|to |(vuoto)    |Richiesto. Stringa che rappresenta il codice della lingua in cui tradurre il testo.|query|stringa|
|maxTranslations|(vuoto)|Richiesto. Intero che rappresenta il numero massimo di traduzioni da restituire.|query|numero intero|
|Authorization| (vuoto)|Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|stringa| intestazione|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503|Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Note sull'implementazione
Usare il metodo `GetTranslationsArray` per recuperare più candidati per la traduzione per più testi di origine.

L'URI della richiesta è `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Il formato del corpo della richiesta è quello indicato di seguito.

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

* `AppId`: elemento obbligatorio. Se si usa l'intestazione Authorization, lasciare vuoto il campo appid, altrimenti includere una stringa contenente `"Bearer" + " " + "access_token"`.
* `From`: elemento obbligatorio. Stringa che rappresenta il codice lingua del testo della traduzione.
* `MaxTranslations`: elemento obbligatorio. Intero che rappresenta il numero massimo di traduzioni da restituire.
* `Options`: facoltativo. Oggetto Options contenente i valori elencati di seguito. Sono tutti facoltativi e con le impostazioni più comuni per impostazione predefinita. Gli elementi specificati devono essere elencati in ordine alfabetico.
    - Category`: stringa contenente la categoria (dominio) della traduzione. L'impostazione predefinita è general.
    - `ContentType`: l'unica opzione supportata e l'impostazione predefinita è text/plain.
    - `IncludeMultipleMTAlternatives`: flag booleano per determinare se devono essere restituite più alternative dal motore MT. I valori validi sono true e false (con distinzione tra maiuscole/minuscole). Il valore predefinito è false e include una sola alternativa. L'impostazione del flag su true consente di generare alternative artificiali nella traduzione, completamente integrate con Collaborative Translation Framework (CTF). La funzionalità consente la restituzione di alternative per le frasi che non hanno alternative in CTF, aggiungendo alternative artificiali dall'elenco delle N migliori del decodificatore.
        - Classificazioni Le classificazioni vengono applicate come segue: 1) la traduzione automatica migliore ha una classificazione pari a 5. 2) Le alternative di CTF riflettono l'autorità del revisore, da -10 a + 10. 3) Le alternative di traduzione (N migliori) generate automaticamente hanno una classificazione pari a 0 e un livello di corrispondenza di 100.
        - Numero di alternative Il numero di alternative restituite è fino a maxTranslations, ma può essere inferiore.
        - Coppie di lingue Questa funzionalità non è disponibile per le traduzioni tra cinese semplificato e cinese tradizionale in entrambe le direzioni. È disponibile per tutte le altre coppie di lingue supportate da Microsoft Translator.
* `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Verrà restituito lo stesso contenuto nella risposta.
* `Uri`: filtrare i risultati in base a questo URI. Se non si imposta alcun valore, il valore predefinito è all.
* `User`: filtrare i risultati in base a questo utente. Se non si imposta alcun valore, il valore predefinito è all.
* `Texts`: elemento obbligatorio. Matrice contenente i testi per la traduzione. Tutte le stringhe devono essere nella stessa lingua. Il totale di tutti i testi da tradurre non deve superare 10.000 caratteri. Il numero massimo di elementi della matrice è 10.
* `To`: elemento obbligatorio. Stringa che rappresenta il codice della lingua in cui tradurre il testo.

Gli elementi facoltativi possono essere omessi. Gli elementi che sono elementi figlio diretti di `GetTranslationsArrayRequest` devono essere elencati in ordine alfabetico.

Il `Content-Type` della richiesta deve essere `text/xml`.

**Valore restituito:** il formato della risposta è quello indicato di seguito.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

* `Translations`: matrice di corrispondenze trovate, archiviate in oggetti `TranslationMatch` (vedere di seguito). Le traduzioni possono includere leggere varianti del testo originale (corrispondenza fuzzy). Le traduzioni verranno ordinate con le corrispondenze al 100% per prime, seguite dalle corrispondenze fuzzy.
* `From`: se il metodo non ha specificato una lingua `From`, questo valore sarà il risultato del rilevamento automatico della lingua. In caso contrario, sarà la lingua di origine specificata.
* `State`: stato dell'utente per mettere in correlazione richiesta e risposta. Contiene lo stesso valore specificato nel parametro `TranslateOptions`.

L'oggetto `TranslationMatch` è costituito dagli elementi seguenti:
* `Error`: se si è verificato un errore per una stringa di input specifica, il codice di errore viene archiviato. In caso contrario, il campo è vuoto.
* `MatchDegree`: il sistema trova una corrispondenza tra le frasi di input e l'archivio, incluse le corrispondenze inesatte.  `MatchDegree` indica in quale misura il testo di input corrisponde al testo originale trovato nell'archivio. Il valore restituito è compreso tra 0 e 100, dove 0 indica nessuna somiglianza e 100 è una corrispondenza esatta tra maiuscole e minuscole.
* `MatchedOriginalText`: testo originale corrispondente per questo risultato. Restituito solo se il testo originale corrispondente è diverso dal testo di input. Usato per restituire il testo di origine di una corrispondenza fuzzy. Non restituito per i risultati di Microsoft Translator.
* `Rating`: indica l'autorità della persona che prende la decisione sulla qualità. I risultati della traduzione automatica avranno una classificazione pari a 5. Le traduzioni fornite in modo anonimo avranno in genere una classificazione da 1 a 4, mentre le traduzioni fornite autorevolmente avranno in genere una classificazione compresa tra 6 e 10.
* `Count`: numero di volte in cui è stata selezionata questa traduzione con questa classificazione. Il valore sarà 0 per la risposta tradotta automaticamente.
* `TranslatedText`: testo tradotto.


### <a name="response-class-status-200"></a>Classe di risposta (stato 200)

stringa

Tipo di contenuto della risposta: application/xml
 
### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:--|:--|:--|:--|:--|
|Authorization  |(vuoto)    |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Ocp-Apim-Subscription-Key`. Token di autorizzazione: `"Bearer" + " " + "access_token"`.|intestazione|stringa|
|Ocp-Apim-Subscription-Key|(vuoto)  |Obbligatorio se non è specificato il campo `appid` o l'intestazione `Authorization`.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|
|:--|:--|
|400    |Richiesta non valida. Controllare i parametri di input e la risposta di errore dettagliata.|
|401    |Credenziali non valide.|
|500    |Errore del server. Se l'errore persiste, inviare un messaggio, specificando la data e l'ora approssimative della richiesta e l'ID della richiesta incluso nell'intestazione della risposta `X-MS-Trans-Info`.|
|503    |Servizio temporaneamente non disponibile. Riprovare e inviare un messaggio se l'errore persiste.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione all'API Traduzione testuale v3](../migrate-to-v3.md)










