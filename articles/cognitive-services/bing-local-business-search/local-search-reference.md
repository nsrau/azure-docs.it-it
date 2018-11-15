---
title: Informazioni di riferimento per l'API Bing Local Business Search v7 | Microsoft Docs
description: Descrive gli elementi di programmazione dell'API Bing Local Business Search.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 100d2a6163c6b2b7a7bf8a557e69e1082b48eaee
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979217"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Informazioni di riferimento per l'API Bing Local Business Search v7

L'API Local Business Search invia una query di ricerca a Bing per ottenere risultati che includono ristoranti, hotel o altre attività commerciali locali. Per le località, la query può specificare il nome dell'attività commerciale locale o una categoria, ad esempio ristoranti nelle vicinanze. I risultati relativi all'entità includono persone, località o cose. In questo contesto, per località si intendono entità commerciali, stati, paesi e così via.  

Questa sezione contiene i dettagli tecnici sugli oggetti di risposta, i parametri di query e le intestazioni che interessano i risultati della ricerca. Per esempi che illustrano come eseguire richieste, vedere [Guida introduttiva a Local Business Search in C#](quickstarts/local-quickstart.md) oppure [Guida introduttiva a Local Business Search in Java](quickstarts/local-search-java-quickstart.md). 
  
Per informazioni sulle intestazioni che devono essere incluse nelle richieste, vedere [Intestazioni](#headers).  
  
Per informazioni sui parametri di query che devono essere inclusi nelle richieste, vedere [Parametri di query](#query-parameters).  
  
Per informazioni sugli oggetti JSON inclusi nella risposta, vedere [Oggetti risposta](#response-objects).

Per informazioni sull'utilizzo consentito e la visualizzazione dei risultati, vedere [Requisiti per l'uso e la visualizzazione](use-display-requirements.md).


  
## <a name="endpoint"></a>Endpoint  
Per richiedere i risultati relativi ad attività commerciali locali, inviare una richiesta GET a: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
La richiesta deve usare il protocollo HTTPS.  
  
> [!NOTE]
> La lunghezza massima dell'URL è di 2048 caratteri. Per garantire che la lunghezza dell'URL non superi il limite, la lunghezza massima dei parametri di query deve essere inferiore a 1.500 caratteri. Se l'URL supera 2.048 caratteri, il server restituisce l'errore 404 Non trovato.  
  
  
## <a name="headers"></a>Headers  
Di seguito sono riportate le intestazioni che una richiesta e una risposta possono includere.  
  
|Intestazione|Descrizione|  
|------------|-----------------|  
|Accept|Intestazione di richiesta facoltativa.<br /><br /> Il tipo di contenuto multimediale predefinito è application/json. Per specificare che la risposta usi [JSON-LD](http://json-ld.org/), impostare l'intestazione Accept su application/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da virgole di lingue da usare per le stringhe dell'interfaccia utente. L'elenco è in ordine decrescente di preferenza. Per altre informazioni, incluso il formato previsto, vedere [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Questa intestazione e il parametro di query [setLang](#setlang) si escludono a vicenda&mdash;non specificare entrambi.<br /><br /> Se si imposta questa intestazione, è necessario anche specificare il parametro di query [cc](#cc). Per determinare il mercato per cui restituire i risultati, Bing usa la prima lingua supportata individuata nell'elenco e la combina con il valore del parametro `cc`. Se l'elenco non include una lingua supportata, Bing trova la corrispondenza più vicina della lingua e il mercato che supporta la richiesta oppure usa un mercato aggregato o predefinito per i risultati. Per determinare il mercato usato da Bing, vedere l'intestazione BingAPIs-Market.<br /><br /> Usare questa intestazione e il parametro di query `cc` solo se si specificano più lingue. In caso contrario, usare i parametri di query [mkt](#mkt) e [setLang](#setlang).<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Eventuali collegamenti alle proprietà Bing.com negli oggetti di risposta si applicano alla lingua specificata.|  
|<a name="market" />BingAPIs-Market|Intestazione della risposta.<br /><br /> Il mercato usato dalla richiesta. Il formato è \<languageCode\>-\<countryCode\>, ad esempio en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Intestazione della risposta.<br /><br /> L'ID della voce di registro che contiene i dettagli della richiesta. In caso di errore acquisire questo ID. Se non si riesce a individuare e risolvere il problema, includere questo ID con le altre informazioni inviate al team di supporto.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria.<br /><br /> La chiave di sottoscrizione ricevuta quando è stata eseguita la registrazione per questo servizio in [Servizi cognitivi](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Intestazione di richiesta facoltativa<br /><br /> Per impostazione predefinita, Bing restituisce il contenuto memorizzato nella cache, se disponibile. Per impedire a Bing di restituire il contenuto memorizzato nella cache, impostare l'intestazione Pragma su no-cache, ad esempio, Pragma: no-cache.
|<a name="useragent" />User-Agent|Intestazione di richiesta facoltativa.<br /><br /> L'agente utente di origine della richiesta. Bing usa l'agente utente per fornire agli utenti di dispositivi mobili un'esperienza ottimizzata. Sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione.<br /><br /> La stringa user-agent deve essere la stessa stringa inviata da qualsiasi browser di uso comune. Per informazioni sugli agenti utente, vedere [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Di seguito sono riportati esempi di stringhe user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Intestazione di richiesta e risposta facoltativa.<br /><br /> Bing usa questa intestazione per fornire agli utenti un comportamento coerente tra le chiamate API Bing. Bing spesso invia in versione di anteprima i miglioramenti e le nuove funzionalità e usa l'ID client come chiave per l'assegnazione del traffico per le diverse versioni di anteprima. Se non si usa lo stesso ID client per un utente in più richieste, Bing può assegnare l'utente a più versioni di anteprima in conflitto. L'assegnazione a più versioni di anteprima in conflitto può generare un'esperienza utente incoerente. Ad esempio, se alla seconda richiesta viene assegnata una versione di anteprima diversa rispetto alla prima, è possibile che l'esperienza non sia quella prevista. Inoltre, Bing può usare l'ID client per adattare i risultati Web alla cronologia di ricerca dell'ID client, fornendo un'esperienza più completa per l'utente.<br /><br /> Bing usa questa intestazione anche per migliorare le classifiche dei risultati analizzando l'attività generata da un ID client. I miglioramenti di pertinenza migliorano la qualità dei risultati forniti dalle API Bing consentendo, di conseguenza, percentuali di click-through più elevate per il consumer API.<br /><br /> **IMPORTANTE:** sebbene sia facoltativa, è opportuno considerare obbligatoria questa intestazione. Salvare in modo permanente l'ID client in più richieste per la stessa combinazione di dispositivo e utente finale consente 1) al consumer API di ottenere un'esperienza utente coerente e 2) percentuali di click-through più elevate migliorando la qualità dei risultati dalle API Bing.<br /><br /> Di seguito sono indicate le regole d'uso di base che si applicano a questa intestazione.<br /><ul><li>Ogni utente che usa l'applicazione nel dispositivo deve avere un ID client univoco generato da Bing.<br /><br/>Se non si include questa intestazione nella richiesta, Bing genera un ID e lo restituisce nell'intestazione della risposta X-MSEdge-ClientID. L'unica volta in cui NON è necessario includere questa intestazione in una richiesta è la prima volta l'utente usa l'app nel dispositivo.<br /><br/></li><li>Usare l'ID client per ogni richiesta dell'API Bing che l'app effettua per questo utente nel dispositivo.<br /><br/></li><li>**ATTENZIONE:** è necessario assicurarsi che questo ID client non sia collegabile a informazioni sull'account utente autenticabili.</li><br/><li>Salvare l'ID client in modo permanente. Per salvare in modo permanente l'ID in un'app browser, usare un cookie HTTP permanente per far sì che l'ID venga usato in tutte le sessioni. Non usare un cookie di sessione. Per altre app, ad esempio le app per dispositivi mobili, usare l'archiviazione permanente del dispositivo per salvare in modo permanente l'ID.<br /><br/>La volta successiva che l'utente usa l'app in questo dispositivo, ottiene l'ID client salvato in modo permanente.</li></ul><br /> **NOTA:** le risposte Bing possono includere o meno questa intestazione. Se la risposta include questa intestazione, acquisire l'ID client e usarlo per tutte le richieste Bing successive per l'utente in tale dispositivo.<br /><br /> **NOTA:** se si include X-MSEdge-ClientID, non includere i cookie nella richiesta.|  
|<a name="clientip" />X-MSEdge-ClientIP|Intestazione di richiesta facoltativa.<br /><br /> Indirizzo IPv4 o IPv6 del dispositivo client. L'indirizzo IP viene usato per individuare la posizione dell'utente. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura.<br /><br /> **NOTA:** sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione e l'intestazione X-Search-Location.<br /><br /> Non offuscare l'indirizzo, ad esempio, modificando l'ultimo ottetto su 0. L'offuscamento dei risultati degli indirizzi nella posizione in un punto non vicino alla posizione effettiva del dispositivo può generare risultati errati in Bing.|  
|<a name="location" />X-Search-Location|Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da punto e virgola di coppie chiave/valore che descrivono la posizione geografica del client. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura e per restituire il contenuto locale pertinente. Specificare la coppia chiave/valore come \<chiave\>:\<valore\>. Di seguito sono riportate le chiavi usate per specificare la posizione dell'utente.<br /><br /><ul><li>lat: la latitudine della posizione del client, in gradi. La latitudine deve essere maggiore o uguale a -90.0 e minore o uguale a + 90.0. I valori negativi indicano le latitudini meridionali e i valori positivi indicano le latitudini settentrionali.<br /><br /></li><li>long: la longitudine della posizione del client, in gradi. La longitudine deve essere maggiore o uguale a -180.0 e minore o uguale a +180.0. I valori negativi indicano le latitudini occidentali e i valori positivi indicano le latitudini orientali.<br /><br /></li><li>re: il raggio, in metri, che specifica la precisione orizzontale delle coordinate. Passare il valore restituito dal servizio che rileva la posizione del dispositivo. I valori tipici potrebbero essere 22 m per GPS/Wi-Fi, 380 m per la triangolazione dei ripetitori di rete e 18.000 m per la ricerca inversa degli indirizzi IP.<br /><br /></li><li>ts: il timestamp UNIX UTC del momento in cui il client si trovava nella posizione. Il timestamp UNIX è il numero di secondi trascorsi dal 1° gennaio 1970.<br /><br /></li><li>head&mdash;Facoltativa. Intestazione o direzione di viaggio relative al client. Specificare la direzione di viaggio come gradi da 0 a 360, partendo in senso orario rispetto al vero nord. Specificare questa chiave solo se la chiave `sp` è diversa da zero.<br /><br /></li><li>sp: la velocità orizzontale, in metri al secondo, con cui viaggia il dispositivo client.<br /><br /></li><li>alt: l'altitudine del dispositivo client, in metri.<br /><br /></li><li>are: facoltativo. Il raggio, in metri, che specifica la precisione verticale delle coordinate. Il raggio predefinito è 50 km. Specificare questa chiave solo se si specifica la chiave `alt`.<br /><br /></li></ul> **NOTA:** sebbene queste chiavi siano facoltative, più informazioni vengono fornite, più accurati sono i risultati relativi alle posizioni.<br /><br /> **NOTA:** è consigliabile specificare sempre la posizione geografica dell'utente. Specificare la posizione è particolarmente importante se l'indirizzo IP del client non riflette accuratamente la posizione fisica dell'utente, ad esempio, se il client usa una VPN. Per ottenere risultati ottimali, è necessario includere questa intestazione e l'intestazione X-MSEdge-ClientIP ma, è necessario almeno includere questa intestazione.|

> [!NOTE] 
> Si tenga presente che le condizioni per l'utilizzo richiedono la conformità con tutte le leggi applicabili, tra cui l'uso di queste intestazioni. Per esempio, in alcune giurisdizioni, come l'Europa, sono previsti requisiti per ottenere il consenso dell'utente prima di inserire dispositivi di tracciamento nei dispositivi dell'utente.
  

## <a name="query-parameters"></a>Parametri di query  
La richiesta può includere i parametri di query seguenti. Vedere i parametri obbligatori nella colonna corrispondente. È necessario eseguire la codifica URL dei parametri della query.  
  
  
|Nome|Valore|Tipo|Obbligatorio|  
|----------|-----------|----------|--------------|
|<a name="count" />count|Numero di risultati da restituire, a partire dall'indice specificato dal parametro `offset`.|String|No |   
|<a name="localCategories" />localCategories|Elenco di opzioni che definiscono una ricerca in base alla categoria di attività commerciale.  Vedere [Ricerca in base a categorie di attività commerciali](local-categories.md).|String|No |  
|<a name="mkt" />mkt|Mercato dal quale provengono i risultati. <br /><br />Per un elenco di possibili valori di mercato, vedere [Codici di mercato](#market-codes).<br /><br /> **NOTA:** attualmente l'API Local Business Search supporta solo la lingua e il mercato en-us.<br /><br />|Stringa|Sì|
|<a name="offset"/>offset|Indice per definire l'inizio dei risultati specificati dal parametro `count`.|Integer|No |  
|<a name="query" />q|Termine di ricerca dell'utente.|String|No |  
|<a name="responseformat" />responseFormat|Tipo di contenuto multimediale da usare per la risposta. Di seguito sono riportati i valori possibili senza distinzione tra maiuscole e minuscole.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Il valore predefinito è JSON. Per informazioni sugli oggetti JSON contenuti nella risposta, vedere [Oggetti risposta](#response-objects).<br /><br />  Se si specifica JsonLd, il corpo della risposta include gli oggetti JSON-LD contenenti i risultati della ricerca. Per informazioni su JSON-LD, vedere [JSON-LD](http://json-ld.org/).|String|No |  
|<a name="safesearch" />safeSearch|Filtro usato per filtrare il contenuto per adulti. Di seguito sono riportati i possibili valori di filtro con distinzione tra maiuscole e minuscole.<br /><ul><li>Disattivata: vengono restituite le pagine Web con testo, immagini o video per adulti.<br /><br/></li><li>Moderata: vengono restituite le pagine Web con testo per adulti, ma non le immagini o i video per adulti.<br /><br/></li><li>Completa: non vengono restituite le pagine Web con testo, immagini o video per adulti.</li></ul><br /> Il valore predefinito è Moderata.<br /><br /> **NOTA:** se la richiesta proviene da un mercato in cui il criterio per adulti di Bing richiede che `safeSearch` sia impostata su Completa, Bing ignora il valore di `safeSearch` e usa Completa.<br/><br/>**NOTA:** se si usa l'operatore di query `site:`, esiste la possibilità che la risposta possa includere contenuti per adulti indipendentemente dall'impostazione del parametro di query `safeSearch`. Usare `site:` solo se si è a conoscenza del contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti. |String|No |  
|<a name="setlang" />setLang|Lingua da usare per le stringhe dell'interfaccia utente. Specificare la lingua tramite il codice lingua a due lettere ISO 639-1. Ad esempio, il codice lingua per l'inglese è EN. L'impostazione predefinita è EN (inglese).<br /><br /> Sebbene sia facoltativo, è opportuno specificare sempre la lingua. In genere, si imposta `setLang` sulla stessa lingua specificata da `mkt`, a meno che non si intenda visualizzare le stringhe dell'interfaccia utente in un'altra lingua.<br /><br /> Questo parametro e l'intestazione [Accept-Language](#acceptlanguage) si escludono a vicenda&mdash;non specificare entrambi.<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Eventuali collegamenti alle proprietà Bing.com negli oggetti risposta si applicano anche alla lingua specificata.|String|No | 


## <a name="response-objects"></a>Oggetti della risposta  
Di seguito sono indicati gli oggetti JSON che può includere la risposta. Se la richiesta ha esito positivo, l'oggetto di livello più alto nella risposta è [SearchResponse](#searchresponse). Se la richiesta ha esito negativo, l'oggetto di livello più alto è [ErrorResponse](#errorresponse).


|Oggetto|Descrizione|  
|------------|-----------------|  
|[Place](#place)|Definisce le informazioni su un'attività commerciale locale, ad esempio un ristorante o un hotel.|  

  
### <a name="error"></a>Error  
Definisce l'errore che si è verificato.  
  
|Elemento|Descrizione|Tipo|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|Codice di errore che identifica la categoria di errore. Per un elenco di codici possibili, vedere [Codici di errore ](#error-codes).|string|  
|<a name="error-message" />message|Descrizione dell'errore.|string|  
|<a name="error-moredetails" />moreDetails|Descrizione che fornisce altre informazioni sull'errore.|string|  
|<a name="error-parameter" />parameter|Parametro di query nella richiesta che ha causato l'errore.|string|  
|<a name="error-subcode" />subCode|Codice di errore che identifica l'errore. Se ad esempio `code` è InvalidRequest, `subCode` potrebbe essere ParameterInvalid o ParameterInvalidValue. |string|  
|<a name="error-value" />value|Valore del parametro di query che non è valido.|string|  
  

### <a name="errorresponse"></a>ErrorResponse  
Oggetto di livello superiore incluso nella risposta in caso di richiesta con esito negativo.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|_type|Hint per il tipo.|string|  
|<a name="errors" />errors|Un elenco di errori che descrivono i motivi per cui la richiesta non ha avuto esito positivo.|[Error](#error)[]|  

  
  
### <a name="license"></a>License  
Definisce la licenza ai sensi della quale è possibile usare il testo o la foto.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|name|Il nome della licenza.|string|  
|URL|L'URL di un sito Web in cui l'utente può ottenere ulteriori informazioni sulla licenza.<br /><br /> Usare il nome e l'URL per creare un collegamento ipertestuale.|string|  


### <a name="link"></a>Link  
Definisce i componenti di un collegamento ipertestuale.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|_type|Hint per il tipo.|string|  
|text|Testo visualizzato.|string|  
|URL|Uniform Resource Locator. Usare l'URL e il testo visualizzato per creare un collegamento ipertestuale.|string|  
  


  
### <a name="organization"></a>Organization  
Definisce un editore.  
  
Si noti che un editore può fornire il proprio nome e/o il sito Web.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|name|Nome dell'editore.|string|  
|URL|L'URL del sito Web dell'editore.<br /><br /> Si noti che l'editore potrebbe non fornire un sito Web.|string|  
  
  

### <a name="place"></a>Place  
Definisce le informazioni su un'attività commerciale locale, ad esempio un ristorante o un hotel.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che può essere impostato su uno dei valori seguenti:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurant</ul><li>|string|  
|address|Indirizzo postale del luogo in cui si trova l'entità.|[PostalAddress](#postaladdress)|  
|entityPresentationInfo|Informazioni aggiuntive sull'entità, ad esempio gli hint che è possibile usare per determinare il tipo dell'entità. Ad esempio, se si tratta di un ristorante o di un hotel. Il campo `entityScenario` è impostato su ListItem.|[EntityPresentationInfo](#entitypresentationinfo)|  
|name|Nome dell'entità.|string|  
|telephone|Numero di telefono dell'entità.|string|  
|URL|URL del sito Web dell'entità.<br /><br /> Usare questo URL insieme al nome dell'entità per creare un collegamento ipertestuale su cui l'utente può fare clic per visualizzare il sito Web dell'entità.|string|  
|webSearchUrl|URL del risultato della ricerca di Bing per la specifica località.|string| 
  
  
### <a name="querycontext"></a>QueryContext  
Definisce il contesto di query usato da Bing per la richiesta.  
  
|Elemento|Descrizione|Tipo|  
|-------------|-----------------|----------|  
|adultIntent|Valore booleano che indica se la query specificata ha finalità per adulti. Il valore è **true** se la query ha finalità per adulti; in caso contrario, è **false** .|boolean|  
|alterationOverrideQuery|Stringa di query da usare per forzare l'utilizzo della stringa originale in Bing. Ad esempio, se la stringa di query è *saling downwind*, questa stringa di query sarà *+saling downwind*. Ricordarsi di codificare la stringa di query che risulta in *%2Bsaling+downwind*.<br /><br /> Questo campo viene incluso solo se la stringa di query originale contiene un errore di ortografia.|string|  
|alteredQuery|La stringa di query usata da Bing per eseguire la query. Bing usa la stringa di query modificata se la stringa di query originale conteneva errori di ortografia. Ad esempio, se la stringa di query è `saling downwind`, la stringa di query modificata sarà `sailing downwind`.<br /><br /> Questo campo viene incluso solo se la stringa di query originale contiene un errore di ortografia.|string|  
|askUserForLocation|Valore booleano che indica se Bing richiede la posizione dell'utente per fornire risultati accurati. Se è stata specificata la posizione dell'utente usando le intestazioni [X-MSEdge-ClientIP](#clientip) e [X-Search-Location](#location), è possibile ignorare questo campo.<br /><br /> Per le query in grado di riconoscere la posizione, ad esempio "meteo di oggi " o "ristoranti vicino a me" che richiedono la posizione dell'utente per fornire risultati accurati, questo campo è impostato su **true**.<br /><br /> Per le query in grado di riconoscere la posizione che includono la posizione, ad esempio "meteo di Seattle", questo campo è impostato su **false**. Questo campo viene impostato su **false** anche per le query che non riconoscono la posizione, ad esempio "best seller".|boolean|  
|originalQuery|La stringa di query come è stata specificata nella richiesta.|string|  

### <a name="identifiable"></a>Identifiable
|Nome|Valore|Tipo|  
|-------------|-----------------|----------|
|id|Identificatore di risorsa|string|
 
### <a name="rankinggroup"></a>RankingGroup
Definisce un gruppo di risultati di ricerca, ad esempio la riga principale.
|Nome|Valore|Tipo|  
|-------------|-----------------|----------|
|items|Elenco di risultati della ricerca da visualizzare nel gruppo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definisce un elemento dei risultati della ricerca da visualizzare.
|Nome|Valore|Tipo|  
|-------------|-----------------|----------|
|resultIndex|Indice in base zero dell'elemento nella risposta da visualizzare. Se l'elemento non include questo campo, visualizzare tutti gli elementi nella risposta. Ad esempio, visualizzare tutti gli articoli sulle notizie nella risposta di Ricerca notizie.|Integer|
|answerType|Risposta che contiene l'elemento da visualizzare, ad esempio, Ricerca notizie.<br /><br />Usare il tipo per trovare la risposta nell'oggetto SearchResponse. Il tipo è il nome di un campo SearchResponse.<br /><br /> Tuttavia, usare il tipo di risposta solo se questo oggetto include il campo del valore; in caso contrario, ignorarlo.|string|
|textualIndex|Indice della risposta in textualAnswers da visualizzare.| Unsigned Integer|
|value|ID che identifica una risposta o un elemento di una risposta da visualizzare. Se l'ID identifica una risposta, visualizzare tutti gli elementi della risposta.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Definisce il punto in cui il contenuto della pagina dei risultati della ricerca deve essere posizionato e in quale ordine.  
  
|Nome|Valore|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|I risultati della ricerca da visualizzare nella riga principale.|  
|<a name="ranking-pole" />pole|I risultati della ricerca che devono ottenere il trattamento più visibile, ad esempio la visualizzazione sopra la riga principale e la barra laterale.|  
|<a name="ranking-sidebar" />sidebar|I risultati della ricerca da visualizzare nella barra laterale.| 

### <a name="searchresponse"></a>SearchResponse  
Definisce l'oggetto di livello superiore incluso nella risposta quando la richiesta ha esito positivo.  
  
Si noti che se il servizio sospetta un attacco Denial of Service, la richiesta avrà esito positivo (con codice di stato HTTP 200 OK), ma il corpo della risposta sarà vuoto.  
  
|Nome|Valore|Tipo|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su SearchResponse.|string|  
|places|Elenco di entità che sono rilevanti per la query di ricerca.|Oggetto JSON|  
|queryContext|Oggetto che contiene la stringa di query usata da Bing per la richiesta.<br /><br /> Questo oggetto contiene la stringa di query così come specificata dall'utente. Può anche contenere una stringa di query modificata usata da Bing per la query se la stringa contiene un errore di ortografia.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Codici di errore

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.  
  
|Codice di stato|Descrizione|  
|-----------------|-----------------|  
|200|Completamento della procedura.|  
|400|Uno dei parametri di query manca o non è valido.|  
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

|Codice|Sottocodice|Descrizione
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Il codice di stato HTTP è 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloccato|Bing restituisce InvalidRequest ogni volta che una parte della richiesta non è valida, ad esempio quando non è specificato un parametro obbligatorio o un valore di parametro non è valido.<br/><br/>Se l'errore è ParameterMissing o ParameterInvalidValue, il codice di stato HTTP è 400.<br/><br/>Se si usa il protocollo HTTP anziché HTTPS, Bing restituisce HttpNotAllowed e il codice di stato HTTP è 410.
|RateLimitExceeded|Nessun codice secondario|Bing restituisce RateLimitExceeded ogni volta che si supera la quota di query al secondo (QPS) o di query al mese (QPM).<br/><br/>Se è stata superata la quota di query al secondo, Bing restituisce il codice di stato HTTP 429; se è stata superata la quota di query al mese, Bing restituisce il codice di stato 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing restituisce InvalidAuthorization quando non riesce ad autenticare il chiamante, ad esempio quando l'intestazione `Ocp-Apim-Subscription-Key` non è specificata o la chiave di sottoscrizione non è valida.<br/><br/>La ridondanza si verifica se si specificano più metodi di autenticazione.<br/><br/>Se l'errore è InvalidAuthorization, il codice di stato HTTP è 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing restituisce InsufficientAuthorization quando il chiamante non ha le autorizzazioni per accedere alla risorsa. Ciò può verificarsi se la chiave di sottoscrizione è stata disabilitata o è scaduta. <br/><br/>Se l'errore è InsufficientAuthorization, il codice di stato HTTP è 403.

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva a Local Business Search](quickstarts/local-quickstart.md)
- [Guida introduttiva a Local Business Search in Java](quickstarts/local-search-java-quickstart.md)
- [Guida introduttiva a Local Business Search in Node](quickstarts/local-search-node-quickstart.md)
- [Guida introduttiva a Local Business Search in Python](quickstarts/local-search-python-quickstart.md)