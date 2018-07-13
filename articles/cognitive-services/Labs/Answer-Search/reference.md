---
title: Riferimento per il progetto Ricerca di risposte - Servizi cognitivi Microsoft | Microsoft Docs
description: Riferimento per l'endpoint del progetto Ricerca di risposte.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8c95fac0c031ec62a9d98d6c3278bd3b3f345140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376449"
---
# <a name="project-answer-search-v7-reference"></a>Riferimento per il progetto Ricerca di risposte v7

L'API Ricerca di risposte Bing accetta un parametro di query e restituisce un valore `searchResponse` con `answerType`: `facts` o `entities`. 

Le applicazioni che usano questa API inviano richieste all'endpoint con un URL da visualizzare in anteprima in un parametro di query.  La richiesta deve includere il parametro `q=searchTerm` e l'intestazione *Ocp-Apim-Subscription-Key*.   

È possibile analizzare la risposta JSON per individuare i fact e le entità che contengono i dettagli sull'oggetto della ricerca.

## <a name="endpoint"></a>Endpoint
Per richiedere i risultati della ricerca di risposte, è consigliabile inviare una richiesta al seguente endpoint. Usare le intestazioni e i parametri URL per definire altre specifiche.

Endpoint GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

La richiesta deve usare il protocollo HTTPS e includere il parametro di query seguente:
-  q =<URL>: la query che identifica l'oggetto della ricerca

Per gli esempi che illustrano come effettuare richieste, vedere l'articolo sull'[avvio rapido in C#](c-sharp-quickstart.md) oppure sull'[avvio rapido in Java](java-quickstart.md). 

Le sezioni seguenti contengono i dettagli tecnici sugli oggetti risposta, i parametri di query e le intestazioni che interessano i risultati della ricerca. 
  
Per informazioni sulle intestazioni che devono essere incluse nelle richieste, vedere [Intestazioni](#headers).  
  
Per informazioni sui parametri di query che devono essere inclusi nelle richieste, vedere [Parametri di query](#query-parameters).  
  
Per informazioni sugli oggetti JSON inclusi nella risposta, vedere [Oggetti risposta](#response-objects).

La lunghezza massima consentita dell'URL di query è 2.048 caratteri. Per garantire che la lunghezza dell'URL non superi il limite, la lunghezza massima dei parametri di query deve essere inferiore a 1.500 caratteri. Se l'URL supera 2.048 caratteri, il server restituisce l'errore 404 Non trovato.  

Per informazioni sull'utilizzo consentito e la visualizzazione dei risultati, vedere [Requisiti per l'uso e la visualizzazione](use-display-requirements.md). 

> [!NOTE]
> Alcune intestazioni della richiesta che sono significative per le altre API di ricerca non incidono su Anteprima URL
> - Pragma: il chiamante non ha il controllo sull'eventualità che Anteprima URL usi la cache
> - Cache-Control: il chiamante non ha il controllo sull'eventualità che Anteprima URL usi la cache
> - User-Agent

> Inoltre, alcuni parametri non sono attualmente significativi per l'API Anteprima URL, ma potranno essere usati in futuro per un processo migliore di globalizzazione. 
 
## <a name="headers"></a>Headers  
Di seguito sono riportate le intestazioni che una richiesta e una risposta possono includere.  
  
|Intestazione|DESCRIZIONE|  
|------------|-----------------|  
|Accept|Intestazione di richiesta facoltativa.<br /><br /> Il tipo di contenuto multimediale predefinito è application/json. Per specificare che la risposta usi [JSON-LD](http://json-ld.org/), impostare l'intestazione Accept su application/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da virgole di lingue da usare per le stringhe dell'interfaccia utente. L'elenco è in ordine decrescente di preferenza. Per altre informazioni, incluso il formato previsto, vedere [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Questa intestazione e il parametro di query [setLang](#setlang) si escludono a vicenda, quindi non specificare entrambi.<br /><br /> Se si imposta questa intestazione, è necessario anche specificare il parametro di query [cc](#cc). Per determinare il mercato per cui restituire i risultati, Bing usa la prima lingua supportata individuata nell'elenco e la combina con il valore del parametro `cc`. Se l'elenco non include una lingua supportata, Bing trova la corrispondenza più vicina della lingua e il mercato che supporta la richiesta oppure usa un mercato aggregato o predefinito per i risultati. Per determinare il mercato usato da Bing, vedere l'intestazione BingAPIs-Market.<br /><br /> Usare questa intestazione e il parametro di query `cc` solo se si specificano più lingue. In caso contrario, usare i parametri di query [mkt](#mkt) e [setLang](#setlang).<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Eventuali collegamenti alle proprietà Bing.com negli oggetti di risposta si applicano alla lingua specificata.|  
|<a name="market" />BingAPIs-Market|Intestazione della risposta.<br /><br /> Il mercato usato dalla richiesta. Il formato è \<languageCode\>-\<countryCode\>, ad esempio en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Intestazione della risposta.<br /><br /> L'ID della voce di registro che contiene i dettagli della richiesta. In caso di errore acquisire questo ID. Se non si riesce a individuare e risolvere il problema, includere questo ID con le altre informazioni fornite al team di supporto.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria.<br /><br /> La chiave di sottoscrizione ricevuta quando è stata effettuata l'iscrizione per questo servizio in [Servizi cognitivi](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Intestazione di richiesta facoltativa<br /><br /> Per impostazione predefinita, Bing restituisce il contenuto memorizzato nella cache, se disponibile. Per impedire a Bing di restituire il contenuto memorizzato nella cache, impostare l'intestazione Pragma su no-cache, ad esempio, Pragma: no-cache.
|<a name="useragent" />User-Agent|Intestazione di richiesta facoltativa.<br /><br /> L'agente utente di origine della richiesta. Bing usa l'agente utente per fornire agli utenti di dispositivi mobili un'esperienza ottimizzata. Sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione.<br /><br /> La stringa user-agent deve essere la stessa stringa inviata da qualsiasi browser di uso comune. Per informazioni sugli agenti utente, vedere [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Di seguito sono riportati esempi di stringhe user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Intestazione di richiesta e risposta facoltativa.<br /><br /> Bing usa questa intestazione per fornire agli utenti un comportamento coerente tra le chiamate API Bing. Bing spesso invia in versione di anteprima i miglioramenti e le nuove funzionalità e usa l'ID client come chiave per l'assegnazione del traffico per le diverse versioni di anteprima. Se non si usa lo stesso ID client per un utente in più richieste, Bing può assegnare l'utente a più versioni di anteprima in conflitto. L'assegnazione a più versioni di anteprima in conflitto può generare un'esperienza utente incoerente. Ad esempio, se alla seconda richiesta viene assegnata una versione di anteprima diversa rispetto alla prima, è possibile che l'esperienza non sia quella prevista. Inoltre, Bing può usare l'ID client per adattare i risultati Web alla cronologia di ricerca dell'ID client, fornendo un'esperienza più completa per l'utente.<br /><br /> Bing usa questa intestazione anche per migliorare le classifiche dei risultati analizzando l'attività generata da un ID client. I miglioramenti di pertinenza migliorano la qualità dei risultati forniti dalle API Bing consentendo, di conseguenza, percentuali di click-through più elevate per il consumer API.<br /><br /> **IMPORTANTE:** sebbene sia facoltativa, è opportuno considerare obbligatoria questa intestazione. Salvare in modo permanente l'ID client in più richieste per la stessa combinazione di dispositivo e utente finale consente 1) al consumer API di ottenere un'esperienza utente coerente e 2) percentuali di click-through più elevate migliorando la qualità dei risultati dalle API Bing.<br /><br /> Di seguito sono riportate le regole di utilizzo di base che si applicano a questa intestazione.<br /><ul><li>Ogni utente che usa l'applicazione nel dispositivo deve avere un ID client univoco generato da Bing.<br /><br/>Se non si include questa intestazione nella richiesta, Bing genera un ID e lo restituisce nell'intestazione della risposta X-MSEdge-ClientID. L'unica volta in cui NON è necessario includere questa intestazione in una richiesta è la prima volta l'utente usa l'app nel dispositivo.<br /><br/></li><li>Usare l'ID client per ogni richiesta dell'API Bing che l'app effettua per questo utente nel dispositivo.<br /><br/></li><li>**ATTENZIONE:** è necessario assicurarsi che questo ID client non sia collegabile a informazioni sull'account utente autenticabili.</li><br/><li>Salvare in modo permanente l'ID client. Per salvare in modo permanente l'ID in un'app browser, usare un cookie HTTP permanente per verificare che l'ID venga usato in tutte le sessioni. Non usare un cookie di sessione. Per altre app, ad esempio le app per dispositivi mobili, usare l'archiviazione permanente del dispositivo per salvare in modo permanente l'ID.<br /><br/>La volta successiva che l'utente usa l'app in questo dispositivo, ottiene l'ID client salvato in modo permanente.</li></ul><br /> **NOTA:** le risposte Bing possono includere o no questa intestazione. Se la risposta include questa intestazione, acquisire l'ID client e usarlo per tutte le richieste Bing successive per l'utente in tale dispositivo.<br /><br /> **NOTA:** se si include X-MSEdge-ClientID, non includere i cookie nella richiesta.|  
|<a name="clientip" />X-MSEdge-ClientIP|Intestazione di richiesta facoltativa.<br /><br /> Indirizzo IPv4 o IPv6 del dispositivo client. L'indirizzo IP viene usato per individuare la posizione dell'utente. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura.<br /><br /> **NOTA:** sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione e l'intestazione X-Search-Location.<br /><br /> Non offuscare l'indirizzo, ad esempio, modificando l'ultimo ottetto su 0. L'offuscamento dei risultati degli indirizzi nella posizione che non sono in un punto vicino alla posizione effettiva del dispositivo può generare risultati errati in Bing.|  
|<a name="location" />X-Search-Location|Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da punto e virgola di coppie chiave/valore che descrivono la posizione geografica del client. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura e per restituire il contenuto locale pertinente. Specificare la coppia chiave/valore come \<chiave\>:\<valore\>. Di seguito sono riportate le chiavi usate per specificare la posizione dell'utente.<br /><br /><ul><li>lat: la latitudine della posizione del client, in gradi. La latitudine deve essere maggiore o uguale a -90.0 e minore o uguale a + 90.0. I valori negativi indicano le latitudini meridionali e i valori positivi indicano le latitudini settentrionali.<br /><br /></li><li>long: la longitudine della posizione del client, in gradi. La longitudine deve essere maggiore o uguale a -180.0 e minore o uguale a +180.0. I valori negativi indicano le latitudini occidentali e i valori positivi indicano le latitudini orientali.<br /><br /></li><li>re: il raggio, in metri, che specifica la precisione orizzontale delle coordinate. Passare il valore restituito dal servizio che rileva la posizione del dispositivo. I valori tipici potrebbero essere 22 m per GPS/Wi-Fi, 380 m per la triangolazione dei ripetitori di rete e 18.000 m per la ricerca inversa degli indirizzi IP.<br /><br /></li><li>ts: il timestamp UNIX UTC del momento in cui il client si trovava nella posizione. Il timestamp UNIX è il numero di secondi trascorsi dal 1° gennaio 1970.<br /><br /></li><li>head: facoltativo. L'intestazione o la direzione di viaggio relative al client. Specificare la direzione di viaggio come gradi da 0 a 360, partendo in senso orario rispetto al vero nord. Specificare questa chiave solo se la chiave `sp` è diversa da zero.<br /><br /></li><li>sp: la velocità orizzontale, in metri al secondo, con cui viaggia il dispositivo client.<br /><br /></li><li>alt: l'altitudine del dispositivo client, in metri.<br /><br /></li><li>are: facoltativo. Il raggio, in metri, che specifica la precisione verticale delle coordinate. Il raggio predefinito è 50 km. Specificare questa chiave solo se si specifica la chiave `alt`.<br /><br /></li></ul> **NOTA:** sebbene queste chiavi siano facoltative, più informazioni vengono fornite, più accurati sono i risultati relativi alle posizioni.<br /><br /> **NOTA:** è consigliabile specificare sempre la posizione geografica dell'utente. Specificare la posizione è particolarmente importante se l'indirizzo IP del client non riflette accuratamente la posizione fisica dell'utente, ad esempio, se il client usa una VPN. Per ottenere risultati ottimali, è necessario includere questa intestazione e l'intestazione X-MSEdge-ClientIP ma, è necessario almeno includere questa intestazione.|

> [!NOTE] 
> Si tenga presente che le condizioni per l'utilizzo richiedono la conformità con tutte le leggi applicabili, tra cui l'utilizzo di queste intestazioni. Per esempio, in alcune giurisdizioni, come l'Europa, sono previsti requisiti per ottenere il consenso dell'utente prima di inserire dispositivi di tracciamento nei dispositivi dell'utente.
  

## <a name="query-parameters"></a>Parametri di query  
La richiesta può includere i seguenti parametri di query. Vedere i parametri obbligatori nella colonna corrispondente. È necessario eseguire la codifica URL dei parametri di query.  
  
  
|NOME|Valore|type|Obbligatoria|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Mercato dal quale provengono i risultati. <br /><br />Per un elenco di possibili valori di mercato, vedere [Codici di mercato](#market-codes).<br /><br /> **NOTA:** attualmente l'API Anteprima URL supporta solo la lingua o il mercato en-us.<br /><br />|string|Sì|  
|<a name="query" />q|URL per l'anteprima|string|Sì|  
|<a name="responseformat" />responseFormat|Tipo di contenuto multimediale da usare per la risposta. Di seguito sono riportati i valori possibili con distinzione tra maiuscole e minuscole.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Il valore predefinito è JSON. Per informazioni sugli oggetti JSON contenuti nella risposta, vedere [Oggetti risposta](#response-objects).<br /><br />  Se si specifica JsonLd, il corpo della risposta include gli oggetti JSON-LD contenenti i risultati della ricerca. Per informazioni su JSON-LD, vedere [JSON-LD](http://json-ld.org/).|string|No |  
|<a name="safesearch" />safeSearch|Filtro usato per filtrare il contenuto per adulti. Di seguito sono riportati i possibili valori di filtro con distinzione tra maiuscole e minuscole.<br /><ul><li>Disattivata: vengono restituite le pagine Web con testo, immagini o video per adulti.<br /><br/></li><li>Moderata: vengono restituite le pagine Web con testo per adulti, ma non le immagini o i video per adulti.<br /><br/></li><li>Completa: non vengono restituite le pagine Web con testo, immagini o video per adulti.</li></ul><br /> Il valore predefinito è Moderata.<br /><br /> **NOTA:** se la richiesta proviene da un mercato in cui il criterio per adulti di Bing richiede che `safeSearch` sia impostata su Completa, Bing ignora il valore di `safeSearch` e usa Completa.<br/><br/>**NOTA:** se si usa l'operatore di query `site:`, esiste la possibilità che la risposta possa includere contenuti per adulti indipendentemente dall'impostazione del parametro di query `safeSearch`. Usare `site:` solo se si è a conoscenza del contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti. |string|No |  
|<a name="setlang" />setLang|Lingua da usare per le stringhe dell'interfaccia utente. Specificare la lingua tramite il codice lingua a due lettere ISO 639-1. Ad esempio, il codice lingua per l'inglese è EN. L'impostazione predefinita è EN (inglese).<br /><br /> Sebbene sia facoltativa, è opportuno specificare sempre la lingua. In genere, si imposta `setLang` sulla stessa lingua specificata da `mkt`, a meno che non si desideri visualizzare le stringhe dell'interfaccia utente in un'altra lingua.<br /><br /> Questo parametro e l'intestazione [Accept-Language](#acceptlanguage) si escludono a vicenda, quindi non specificare entrambi.<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Inoltre, eventuali collegamenti alle proprietà Bing.com negli oggetti risposta si applicano alla lingua specificata.|string|No | 


## <a name="response-objects"></a>Oggetto risposta  
Lo schema di risposta è [WebPage] o ErrorResponse, come nell'API Ricerca Web. Se la richiesta ha esito negativo, l'oggetto di livello superiore è [ErrorResponse](#errorresponse).


|Oggetto|DESCRIZIONE|  
|------------|-----------------|  
|[WebPage]|Oggetto JSON di livello superiore che contiene gli attributi dell'anteprima.|  
|[Fact]|Oggetto JSON di livello superiore che contiene i fact.| 
|[Entities]|Oggetto JSON di livello superiore che contiene i dettagli delle entità.| 

  
### <a name="error"></a>Tipi di errore  
Definisce l'errore che si è verificato.  
  
|Elemento|DESCRIZIONE|type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|Codice di errore che identifica la categoria di errore. Per un elenco di codici possibili, vedere [Codici di errore ](#error-codes).|string|  
|<a name="error-message" />message|Descrizione dell'errore.|string|  
|<a name="error-moredetails" />moreDetails|Descrizione che fornisce ulteriori informazioni sull'errore.|string|  
|<a name="error-parameter" />parameter|Parametro di query nella richiesta che ha causato l'errore.|string|  
|<a name="error-subcode" />subCode|Codice di errore che identifica l'errore. Ad esempio, se `code` è InvalidRequest, `subCode` potrebbe essere ParameterInvalid o ParameterInvalidValue. |string|  
|<a name="error-value" />value|Valore del parametro di query che non è valido.|string|  
  

### <a name="errorresponse"></a>ErrorResponse  
Oggetto di livello superiore incluso nella risposta in caso di richiesta con esito negativo.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo.|string|  
|<a name="errors" />errors|Un elenco di errori che descrivono i motivi per cui la richiesta non ha avuto esito positivo.|[Error](#error)[]|  

  
  
### <a name="license"></a>License  
Definisce la licenza ai sensi della quale è possibile usare il testo o la foto.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|name|Il nome della licenza.|string|  
|URL|L'URL di un sito Web in cui l'utente può ottenere ulteriori informazioni sulla licenza.<br /><br /> Usare il nome e l'URL per creare un collegamento ipertestuale.|string|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definisce una regola contrattuale per l'attribuzione di licenze.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su LicenseAttribution.|string|  
|license|Licenza ai sensi della quale è possibile usare il contenuto.|[License](#license)|  
|licenseNotice|La licenza da visualizzare accanto al campo di destinazione. Ad esempio, "Testo ai sensi della licenza CC-BY-SA".<br /><br /> Usare il nome e l'URL della licenza nel campo `license` per creare un collegamento ipertestuale al sito Web che ne descrive i dettagli. Sostituire quindi il nome di licenza nella stringa `licenseNotice`, ad esempio CC-BY-SA, con il collegamento ipertestuale appena creato.|string|  
|mustBeCloseToContent|Valore booleano che determina se il contenuto della regola deve essere posizionato in stretta prossimità al campo a cui si applica la regola. Se il valore è **true**, il contenuto deve essere posizionato in stretta prossimità. Se il valore è **false** o questo campo non esiste, il contenuto può essere posizionato a discrezione del chiamante.|boolean|  
|targetPropertyName|Il nome del campo a cui si applica la regola.|string|  
  

### <a name="link"></a>Collegamento  
Definisce i componenti di un collegamento ipertestuale.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo.|string|  
|text|Testo visualizzato.|string|  
|URL|Uniform Resource Locator. Usare l'URL e il testo visualizzato per creare un collegamento ipertestuale.|string|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definisce una regola contrattuale per l'attribuzione di collegamenti.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su LinkAttribution.|string|  
|mustBeCloseToContent|Valore booleano che determina se il contenuto della regola deve essere posizionato in stretta prossimità al campo a cui si applica la regola. Se il valore è **true**, il contenuto deve essere posizionato in stretta prossimità. Se il valore è **false** o questo campo non esiste, il contenuto può essere posizionato a discrezione del chiamante.|boolean|  
|targetPropertyName|Il nome del campo a cui si applica la regola.<br /><br /> Se non si specifica una destinazione, l'attribuzione si applica all'entità nel suo complesso e deve essere visualizzata immediatamente dopo la presentazione dell'entità. Se esistono più regole di attribuzione di testo e collegamenti che non specificano una destinazione, è necessario concatenarle e visualizzarle usando un'etichetta "Dati di:". Ad esempio, "Dati di < nome provider1\> &#124; < nome provider2\>".|string|  
|text|Testo dell'attribuzione.|string|  
|URL|L'URL del sito Web del provider. Usare `text` e l'URL per creare un collegamento ipertestuale.|string|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definisce una regola contrattuale per l'attribuzione di contenuti multimediali.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su MediaAttribution.|string|  
|mustBeCloseToContent|Valore booleano che determina se il contenuto della regola deve essere posizionato in stretta prossimità al campo a cui si applica la regola. Se il valore è **true**, il contenuto deve essere posizionato in stretta prossimità. Se il valore è **false** o questo campo non esiste, il contenuto può essere posizionato a discrezione del chiamante.|boolean|  
|targetPropertyName|Il nome del campo a cui si applica la regola.|string|  
|URL|URL usato per creare un collegamento ipertestuale del contenuto mediale. Ad esempio, se la destinazione è un'immagine, si usa l'URL per rendere l'immagine selezionabile.|string|  
  
  
  
### <a name="organization"></a>Organizzazione  
Definisce un editore.  
  
Si noti che un editore può fornire il proprio nome e/o il sito Web.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|name|Nome dell'editore.|string|  
|URL|L'URL del sito Web dell'editore.<br /><br /> Si noti che l'editore potrebbe non fornire un sito Web.|string|  
  
  

### <a name="webpage"></a>WebPage  
Definisce le informazioni su una pagina Web in anteprima.  
  
|NOME|Valore|type|  
|----------|-----------|----------|
|name|Titolo della pagina, non necessariamente il titolo HTML|string|
|URL|URL per cui è stata effettivamente eseguita la ricerca per indicizzazione; la richiesta potrebbe avere seguito reindirizzamenti|string|  
|description|Breve descrizione della pagina e del contenuto|string|  
|isFamilyFriendly|Più preciso per gli elementi nell'indice Web; i recuperi in tempo reale eseguono questo rilevamento esclusivamente in base all'URL e non al contenuto della pagina|boolean|
|primaryImageOfPage/contentUrl|URL di un'immagine rappresentativa da includere nell'anteprima|string| 
  
  
### <a name="querycontext"></a>QueryContext  
Definisce il contesto di query usato da Bing per la richiesta.  
  
|Elemento|DESCRIZIONE|type|  
|-------------|-----------------|----------|  
|adultIntent|Valore booleano che indica se la query specificata ha finalità per adulti. Il valore è **true** se la query ha finalità per adulti; in caso contrario, è **false** .|boolean|  
|alterationOverrideQuery|Stringa di query da usare per forzare l'utilizzo della stringa originale in Bing. Ad esempio, se la stringa di query è *saling downwind*, questa stringa di query sarà *+saling downwind*. Ricordarsi di codificare la stringa di query che risulta in *%2Bsaling+downwind*.<br /><br /> Questo campo viene incluso solo se la stringa di query originale contiene un errore di ortografia.|string|  
|alteredQuery|La stringa di query usata da Bing per eseguire la query. Bing usa la stringa di query modificata se la stringa di query originale conteneva errori di ortografia. Ad esempio, se la stringa di query è `saling downwind`, la stringa di query modificata sarà `sailing downwind`.<br /><br /> Questo campo viene incluso solo se la stringa di query originale contiene un errore di ortografia.|string|  
|askUserForLocation|Valore booleano che indica se Bing richiede la posizione dell'utente per fornire risultati accurati. Se è stata specificata la posizione dell'utente usando le intestazioni [X-MSEdge-ClientIP](#clientip) e [X-Search-Location](#location), è possibile ignorare questo campo.<br /><br /> Per le query in grado di riconoscere la posizione, ad esempio "meteo di oggi " o "ristoranti vicino a me" che richiedono la posizione dell'utente per fornire risultati accurati, questo campo è impostato su **true**.<br /><br /> Per le query in grado di riconoscere la posizione che includono la posizione, ad esempio "meteo di Seattle", questo campo è impostato su **false**. Questo campo viene impostato su **false** anche per le query che non riconoscono la posizione, ad esempio "best seller".|boolean|  
|originalQuery|La stringa di query come è stata specificata nella richiesta.|string|  

### <a name="identifiable"></a>Identifiable
|NOME|Valore|type|  
|-------------|-----------------|----------|
|id|Identificatore di risorsa.|string|
 
### <a name="rankinggroup"></a>RankingGroup
Definisce un gruppo di risultati di ricerca, ad esempio la riga principale.
|NOME|Valore|type|  
|-------------|-----------------|----------|
|items|Elenco di risultati della ricerca da visualizzare nel gruppo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definisce un elemento dei risultati della ricerca da visualizzare.
|NOME|Valore|type|  
|-------------|-----------------|----------|
|resultIndex|Indice in base zero dell'elemento nella risposta da visualizzare. Se l'elemento non include questo campo, visualizzare tutti gli elementi nella risposta. Ad esempio, visualizzare tutti gli articoli sulle notizie nella risposta di Ricerca notizie.|Integer|
|answerType|Risposta che contiene l'elemento da visualizzare, ad esempio, Ricerca notizie.<br /><br />Usare il tipo per trovare la risposta nell'oggetto SearchResponse. Il tipo è il nome di un campo SearchResponse.<br /><br /> Tuttavia, usare il tipo di risposta solo se questo oggetto include il campo del valore; in caso contrario, ignorarlo.|string|
|textualIndex|Indice della risposta in textualAnswers da visualizzare.| Unsigned Integer|
|value|ID che identifica una risposta o un elemento di una risposta da visualizzare. Se l'ID identifica una risposta, visualizzare tutti gli elementi della risposta.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Definisce il punto in cui il contenuto della pagina dei risultati della ricerca deve essere posizionato e in quale ordine.  
  
|NOME|Valore|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|I risultati della ricerca da visualizzare nella riga principale.|  
|<a name="ranking-pole" />pole|I risultati della ricerca che devono ottenere il trattamento più visibile, ad esempio la visualizzazione sopra la riga principale e la barra laterale.|  
|<a name="ranking-sidebar" />sidebar|I risultati della ricerca da visualizzare nella barra laterale.| 


### <a name="searchresponse"></a>SearchResponse  
Definisce l'oggetto di livello superiore incluso nella risposta quando la richiesta ha esito positivo.  
  
Si noti che se il servizio sospetta un attacco Denial of Service, la richiesta avrà esito positivo (con codice di stato HTTP 200 OK), ma il corpo della risposta sarà vuoto.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su SearchResponse.|string|  
|WebPage|Oggetto JSON che definisce l'anteprima|stringa|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definisce una regola contrattuale per l'attribuzione di testo normale.  
  
|NOME|Valore|type|  
|----------|-----------|----------|  
|_type|Hint per il tipo, che è impostato su TextAttribution.|string|  
|text|Testo dell'attribuzione.<br /><br /> L'attribuzione di testo si applica all'entità nel suo complesso e deve essere visualizzata immediatamente dopo la presentazione dell'entità. Se esistono più regole di attribuzione di testo o collegamenti che non specificano una destinazione, è necessario concatenarle e visualizzarle usando un'etichetta "Dati di:".|string| 


## <a name="error-codes"></a>Codici di errore

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.  
  
|Codice di stato|DESCRIZIONE|  
|-----------------|-----------------|  
|200|Completamento della procedura.|  
|400|Uno dei parametri di query manca o non è valido.|  
|401|La chiave di sottoscrizione manca o non è valida.|  
|403|L'utente viene autenticato, ad esempio è stata usata una chiave di sottoscrizione valida, ma non dispone dell'autorizzazione per la risorsa richiesta.<br /><br /> Bing può restituire questo stato anche se il chiamante ha superato la quota di query al mese.|  
|410|La richiesta ha usato il protocollo HTTP, anziché HTTPS. L'unico protocollo supportato è HTTPS.|  
|429|Il chiamante ha superato la quota di query al secondo.|  
|500|Evento server imprevisto.|

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

|Codice|SubCode|DESCRIZIONE
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Il codice di stato HTTP è 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloccato|Bing restituisce InvalidRequest ogni volta che una parte della richiesta non è valida. Ad esempio, manca un parametro obbligatorio o un valore di parametro non è valido.<br/><br/>Se l'errore è ParameterMissing o ParameterInvalidValue, il codice di stato HTTP è 400.<br/><br/>Se si usa il protocollo HTTP anziché HTTPS, Bing restituisce HttpNotAllowed e il codice di stato HTTP è 410.
|RateLimitExceeded|Nessun codice secondario|Bing restituisce RateLimitExceeded ogni volta che si supera la quota di query al secondo (QPS) o di query al mese (QPM).<br/><br/>Se è stata superata la quota di query al secondo, Bing restituisce il codice di stato HTTP 429; se è stata superata la quota di query al mese, Bing restituisce il codice di stato 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing restituisce InvalidAuthorization quando non è possibile autenticare il chiamante. Manca, ad esempio, l'intestazione `Ocp-Apim-Subscription-Key` o la chiave di sottoscrizione non è valida.<br/><br/>La ridondanza si verifica se si specifica più di un metodo di autenticazione.<br/><br/>Se l'errore è InvalidAuthorization, il codice di stato HTTP è 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing restituisce InsufficientAuthorization quando il chiamante non ha le autorizzazioni per accedere alla risorsa. Ciò può verificarsi se la chiave di sottoscrizione è stata disabilitata o è scaduta. <br/><br/>Se l'errore è InsufficientAuthorization, il codice di stato HTTP è 403.

## <a name="next-steps"></a>Passaggi successivi
- [Avvio rapido in C#](c-sharp-quickstart.md)
- [Avvio rapido in Java](java-quickstart.md)
- [Avvio rapido in Node](node-quickstart.md)
- [Avvio rapido in Python](python-quickstart.md)

