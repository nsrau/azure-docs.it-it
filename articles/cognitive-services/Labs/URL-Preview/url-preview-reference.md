---
title: Informazioni di riferimento su Project URL Preview
titlesuffix: Azure Cognitive Services
description: Informazioni di riferimento per l'endpoint di Project URL Preview.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 2e4c5e6337c7fb222d18efada6ea9167e58aa024
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471408"
---
# <a name="project-url-preview-v7-reference"></a>Informazioni di riferimento su Project URL Preview v7

URL Preview supporta brevi descrizioni di risorse Web per post di blog, discussioni di forum, pagine di anteprima e così via.  L'URL può essere di qualsiasi tipo di risorsa Internet: pagina Web, notizie, immagini o video. La query deve essere un URL assoluto con schema http o https; non sono supportati URL relativi o altri schemi, ad esempio ftp://.

Le applicazioni che usano URL Preview inviano richieste Web all'endpoint con un URL da visualizzare in anteprima in un parametro di query.  La richiesta deve includere l'intestazione *Ocp-Apim-Subscription-Key*.   

La risposta JSON può essere analizzata per ottenere le informazioni di anteprima: nome, descrizione della risorsa, flag *isFamilyFriendly* e collegamenti che forniscono l'accesso a un'immagine rappresentativa e alla risorsa online completa.

Usare solo i dati di URL Preview per visualizzare frammenti e immagini in anteprima collegati ai siti di origine, nella condivisione URL avviata dall'utente finale su social media, chat bot o offerte simili. Non copiare, archiviare o memorizzare nella cache i dati ricevuti da Project URL Preview. Onorare eventuali richieste di disabilitazione delle anteprime che si possono ricevere dai proprietari di siti Web o contenuti.

## <a name="endpoint"></a>Endpoint
Per richiedere i risultati di URL Preview, inviare una richiesta all'endpoint seguente. Usare le intestazioni e i parametri URL per definire altre specifiche.

Endpoint GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

La richiesta deve usare il protocollo HTTPS e includere il parametro di query seguente:

 q: la query che identifica l'URL da visualizzare in anteprima 

Le sezioni seguenti contengono i dettagli tecnici sugli oggetti risposta, i parametri di query e le intestazioni che interessano i risultati della ricerca. 
  
Per informazioni sulle intestazioni che devono essere incluse nelle richieste, vedere [Intestazioni](#headers).  
  
Per informazioni sui parametri di query che devono essere inclusi nelle richieste, vedere [Parametri di query](#query-parameters).  
  
Per informazioni sugli oggetti JSON inclusi nella risposta, vedere [Oggetti risposta](#response-objects).

La lunghezza massima consentita dell'URL di query è 2.048 caratteri. Per garantire che la lunghezza dell'URL non superi il limite, la lunghezza massima dei parametri di query deve essere inferiore a 1.500 caratteri. Se l'URL supera 2.048 caratteri, il server restituisce l'errore 404 Non trovato.  

Per informazioni sull'utilizzo consentito e la visualizzazione dei risultati, vedere [Requisiti per l'uso e la visualizzazione](use-display-requirements.md). 

> [!NOTE]
> Alcune intestazioni della richiesta che sono significative per le altre API di ricerca non incidono su Anteprima URL
> - Pragma: il chiamante non ha il controllo sull'eventualità che Anteprima URL usi la cache
> - Agente utente: al momento, l'API URL Preview non fornisce risposte diverse per le chiamate provenienti da PC, computer portatili o dispositivi mobili.

> Inoltre, alcuni parametri non sono attualmente significativi per l'API Anteprima URL, ma potranno essere usati in futuro per un processo migliore di globalizzazione. 
 
## <a name="headers"></a>Headers  
Di seguito sono riportate le intestazioni che una richiesta e una risposta possono includere.  
  
|Intestazione|DESCRIZIONE|  
|------------|-----------------|   
|<a name="market" />BingAPIs-Market|Intestazione della risposta.<br /><br /> Il mercato usato dalla richiesta. Il formato è \<languageCode\>-\<countryCode\>, ad esempio en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Intestazione della risposta.<br /><br /> L'ID della voce di registro che contiene i dettagli della richiesta. In caso di errore acquisire questo ID. Se non si riesce a individuare e risolvere il problema, includere questo ID con le altre informazioni inviate al team di supporto.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria.<br /><br /> La chiave di sottoscrizione ricevuta quando è stata effettuata l'iscrizione per questo servizio in [Servizi cognitivi](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Intestazione di richiesta e risposta facoltativa.<br /><br /> Bing usa questa intestazione per fornire agli utenti un comportamento coerente tra le chiamate API Bing. Bing spesso invia in versione di anteprima i miglioramenti e le nuove funzionalità e usa l'ID client come chiave per l'assegnazione del traffico per le diverse versioni di anteprima. Se non si usa lo stesso ID client per un utente in più richieste, Bing può assegnare l'utente a più versioni di anteprima in conflitto. L'assegnazione a più versioni di anteprima in conflitto può generare un'esperienza utente incoerente. Ad esempio, se alla seconda richiesta viene assegnata una versione di anteprima diversa rispetto alla prima, è possibile che l'esperienza non sia quella prevista. Inoltre, Bing può usare l'ID client per adattare i risultati Web alla cronologia di ricerca dell'ID client, fornendo un'esperienza più completa per l'utente.<br /><br /> Bing usa questa intestazione anche per migliorare le classifiche dei risultati analizzando l'attività generata da un ID client. I miglioramenti di pertinenza migliorano la qualità dei risultati forniti dalle API Bing consentendo di conseguenza percentuali di click-through più elevate per il consumer API.<br /><br />Di seguito sono riportate le regole di utilizzo di base che si applicano a questa intestazione.<br /><ul><li>Ogni utente che usa l'applicazione nel dispositivo deve avere un ID client univoco generato da Bing.<br /><br/>Se non si include questa intestazione nella richiesta, Bing genera un ID e lo restituisce nell'intestazione della risposta X-MSEdge-ClientID. L'unica volta in cui NON è necessario includere questa intestazione in una richiesta è la prima volta l'utente usa l'app nel dispositivo.<br /><br/></li><li>Usare l'ID client per ogni richiesta dell'API Bing che l'app effettua per questo utente nel dispositivo.<br /><br/></li><li>**ATTENZIONE:** è necessario assicurarsi che questo ID client non sia collegabile a informazioni sull'account utente autenticabili.</li><br/><li>Salvare l'ID client in modo permanente. Per salvare in modo permanente l'ID in un'app browser, usare un cookie HTTP permanente per far sì che l'ID venga usato in tutte le sessioni. Non usare un cookie di sessione. Per altre app, ad esempio le app per dispositivi mobili, usare l'archiviazione permanente del dispositivo per salvare in modo permanente l'ID.<br /><br/>La volta successiva che l'utente usa l'app in questo dispositivo, ottiene l'ID client salvato in modo permanente.</li></ul><br /> **NOTA:** le risposte Bing possono includere o meno questa intestazione. Se la risposta include questa intestazione, acquisire l'ID client e usarlo per tutte le richieste Bing successive per l'utente in tale dispositivo.<br /><br /> **NOTA:** se si include X-MSEdge-ClientID, non includere i cookie nella richiesta.|  
|<a name="clientip" />X-MSEdge-ClientIP|Intestazione di richiesta facoltativa.<br /><br /> Indirizzo IPv4 o IPv6 del dispositivo client. L'indirizzo IP viene usato per individuare la posizione dell'utente. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura.<br /><br />  Non offuscare l'indirizzo, ad esempio modificando l'ultimo ottetto su 0. Con l'offuscamento dell'indirizzo, la posizione non si troverà in un punto vicino alla posizione effettiva del dispositivo e Bing potrebbe restituire risultati errati.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametri della query  
La richiesta può includere i parametri di query seguenti. Vedere i parametri obbligatori nella colonna corrispondente. È necessario eseguire la codifica URL dei parametri della query. La query deve essere un URL assoluto con schema http o https; non sono supportati URL relativi o altri schemi, ad esempio ftp://
  
  
|NOME|Valore|type|Obbligatoria|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Mercato dal quale provengono i risultati. <br /><br />Per un elenco di possibili valori di mercato, vedere [Codici di mercato](#market-codes).<br /><br /> **Nota:** l'API URL Preview supporta attualmente solo aree geografiche degli Stati Uniti e la lingua inglese.<br /><br />|string|Yes|  
|<a name="query" />q|URL per l'anteprima|string|Yes|  
|<a name="responseformat" />responseFormat|Tipo di contenuto multimediale da usare per la risposta. Di seguito sono riportati i valori possibili senza distinzione tra maiuscole e minuscole.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Il valore predefinito è JSON. Per informazioni sugli oggetti JSON contenuti nella risposta, vedere [Oggetti risposta](#response-objects).<br /><br />  Se si specifica JsonLd, il corpo della risposta include gli oggetti JSON-LD contenenti i risultati della ricerca. Per informazioni su JSON-LD, vedere [JSON-LD](http://json-ld.org/).|string|No |
|<a name="safesearch"/>safeSearch|Il contenuto per adulti illegale o il contenuto pirata viene bloccato con il codice di errore 400 e il flag *isFamilyFriendly* non viene restituito. <p>Per il contenuto per adulti legale, il comportamento è riportato di seguito. Il codice di stato restituisce 200 e il flag *isFamilyFriendly* è impostato su false.<ul><li>safeSearch=strict: titolo, descrizione, URL e immagine non verranno restituiti.</li><li>safeSearch=moderate: si ottengono il titolo, l'URL e la descrizione ma non l'immagine descrittiva.</li><li>safeSearch=off: si ottengono tutti gli oggetti/elementi della risposta, ovvero titolo, URL, descrizione e immagine.</li></ul> |string|Non obbligatorio. </br> L'impostazione predefinita è safeSearch=strict.| 

## <a name="response-objects"></a>Oggetti della risposta  
Lo schema di risposta è [WebPage] o ErrorResponse, come nell'API Ricerca Web. Se la richiesta ha esito negativo, l'oggetto di livello superiore è [ErrorResponse](#errorresponse).


|Oggetto|DESCRIZIONE|  
|------------|-----------------|  
|[WebPage](#webpage)|Oggetto JSON di livello superiore che contiene gli attributi dell'anteprima.|  

  
### <a name="error"></a>Tipi di errore  
Definisce l'errore che si è verificato.  
  
|Elemento|DESCRIZIONE|type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|Codice di errore che identifica la categoria di errore. Per un elenco di codici possibili, vedere [Codici di errore ](#error-codes).|string|  
|<a name="error-message" />message|Descrizione dell'errore.|string|  
|<a name="error-moredetails" />moreDetails|Descrizione che fornisce altre informazioni sull'errore.|string|  
|<a name="error-parameter" />parameter|Parametro di query nella richiesta che ha causato l'errore.|string|  
|<a name="error-subcode" />subCode|Codice di errore che identifica l'errore. Se ad esempio `code` è InvalidRequest, `subCode` potrebbe essere ParameterInvalid o ParameterInvalidValue. |string|  
|<a name="error-value" />value|Valore del parametro di query che non è valido.|string|  
  

### <a name="errorresponse"></a>ErrorResponse  
Oggetto di livello superiore incluso nella risposta in caso di richiesta con esito negativo.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo.|string|  
|<a name="errors" />errors|Un elenco di errori che descrivono i motivi per cui la richiesta non ha avuto esito positivo.|[Error](#error)[]|   
  

### <a name="webpage"></a>WebPage  
Definisce le informazioni su una pagina Web in anteprima.  
  
|NOME|Valore|type|  
|----------|-----------|----------|
|name|Titolo della pagina, non necessariamente il titolo HTML|string|
|URL|L'URL per cui è stata effettivamente eseguita la ricerca per indicizzazione; la richiesta potrebbe avere seguito reindirizzamenti|string|  
|description|Breve descrizione della pagina e del contenuto|string|  
|isFamilyFriendly|Più preciso per gli elementi nell'indice Web; i recuperi in tempo reale eseguono questo rilevamento esclusivamente in base all'URL e non al contenuto della pagina|boolean|
|primaryImageOfPage/contentUrl|URL di un'immagine rappresentativa da includere nell'anteprima|string| 


### <a name="identifiable"></a>Identifiable
|NOME|Valore|type|  
|-------------|-----------------|----------|
|id|Identificatore di risorsa|string|
 

## <a name="error-codes"></a>Codici di errore

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.  
  
|Codice di stato|DESCRIZIONE|  
|-----------------|-----------------|  
|200|Completamento della procedura.|  
|400|Uno dei parametri di query manca o non è valido.| 
|400|ServerError, subCode ResourceError: l'URL richiesto non è raggiungibile|
|400|ServerError, subCode ResourceError: l'URL richiesto non ha restituito un codice di riuscita (anche se ha restituito HTTP 404)|
|400|InvalidRequest, subCode Blocked: l'URL richiesto potrebbe avere contenuti per adulti ed è stato bloccato| 
|401|La chiave di sottoscrizione manca o non è valida.|  
|403|L'utente viene autenticato, ad esempio è stata usata una chiave di sottoscrizione valida, ma non dispone dell'autorizzazione per la risorsa richiesta.<br /><br /> Bing può restituire questo stato anche se il chiamante ha superato la quota di query al mese.|  
|410|La richiesta ha usato il protocollo HTTP anziché HTTPS. L'unico protocollo supportato è HTTPS.|  
|429|Il chiamante ha superato la quota di query al secondo.|  
|500|Errore server imprevisto.|

Se la richiesta non ha esito positivo, la risposta contiene un oggetto [ErrorResponse](#errorresponse) con un elenco di oggetti [Error](#error) che descrivono la causa dell'errore. Se l'errore è correlato a un parametro, il campo `parameter` identifica il parametro che genera il problema. Se l'errore è correlato a un valore di parametro, il campo `value` identifica il valore non valido.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Di seguito sono riportati i valori dei codici di errore e dei codici di errore secondari.

|Codice|Sottocodice|DESCRIZIONE
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Il codice di stato HTTP è 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloccato|Bing restituisce InvalidRequest ogni volta che una parte della richiesta non è valida, ad esempio quando non è specificato un parametro obbligatorio o un valore di parametro non è valido.<br/><br/>Se l'errore è ParameterMissing o ParameterInvalidValue, il codice di stato HTTP è 400.<br/><br/>Se si usa il protocollo HTTP anziché HTTPS, Bing restituisce HttpNotAllowed e il codice di stato HTTP è 410.
|RateLimitExceeded|Nessun codice secondario|Bing restituisce RateLimitExceeded ogni volta che si supera la quota di query al secondo (QPS) o di query al mese (QPM).<br/><br/>Se è stata superata la quota di query al secondo, Bing restituisce il codice di stato HTTP 429; se è stata superata la quota di query al mese, Bing restituisce il codice di stato 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing restituisce InvalidAuthorization quando non riesce ad autenticare il chiamante, ad esempio quando l'intestazione `Ocp-Apim-Subscription-Key` non è specificata o la chiave di sottoscrizione non è valida.<br/><br/>La ridondanza si verifica se si specificano più metodi di autenticazione.<br/><br/>Se l'errore è InvalidAuthorization, il codice di stato HTTP è 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing restituisce InsufficientAuthorization quando il chiamante non ha le autorizzazioni per accedere alla risorsa. Ciò può verificarsi se la chiave di sottoscrizione è stata disabilitata o è scaduta. <br/><br/>Se l'errore è InsufficientAuthorization, il codice di stato HTTP è 403.

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)

