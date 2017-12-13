---
title: Criteri avanzati di Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri avanzati disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 191870aea5f35830115ae1e8885cd3035597411f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-advanced-policies"></a>Criteri avanzati di gestione API
Questo argomento fornisce un riferimento per i criteri di Gestione API seguenti. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AdvancedPolicies"></a>Criteri avanzati  
  
-   [Controlla flusso](api-management-advanced-policies.md#choose): applica in modo condizionale le istruzioni dei criteri sulla base dei risultati della valutazione di [espressioni](api-management-policy-expressions.md) booleane.  
-   [Inoltra richiesta](#ForwardRequest) : inoltra la richiesta al servizio back-end.
-   [Limita la concorrenza](#LimitConcurrency): previene ai criteri racchiusi l’esecuzione di un numero maggiore di richieste contemporaneamente rispetto a quello specificato.
-   [Registra in Hub eventi](#log-to-eventhub): invia messaggi nel formato specificato a un Hub eventi definito da un'entità Logger. 
-   [Restituisci risposta](#mock-response): interrompe l'esecuzione della pipeline e restituisce una risposta fittizia direttamente al chiamante.
-   [Riprova](#Retry) : riprova l'esecuzione delle istruzioni dei criteri, se e fino a quando non viene soddisfatta la condizione. L'esecuzione verrà ripetuta a specifici intervalli di tempo e per il numero di tentativi indicato.  
-   [Restituisci risposta](#ReturnResponse) : l’esecuzione nella pipeline viene interrotta e viene restituita la risposta specificata direttamente al chiamante. 
-   [Invia richiesta unidirezionale](#SendOneWayRequest) : invia una richiesta all'URL specificato senza attendere una risposta.  
-   [Invia richiesta](#SendRequest) : invia una richiesta all'URL specificato.  
-   [Imposta proxy HTTP](#SetHttpProxy): consente di indirizzare le richieste inoltrate tramite un proxy HTTP.  
-   [Imposta metodo di richiesta](#SetRequestMethod) : consente di modificare il metodo HTTP per una richiesta.  
-   [Imposta codice di stato](#SetStatus): modifica il codice di stato HTTP per il valore specificato.  
-   [Imposta variabile](api-management-advanced-policies.md#set-variable): rende persistente un valore in una variabile di [contesto](api-management-policy-expressions.md#ContextVariables) denominata e consente di accedervi in un momento successivo.  
-   [Traccia](#Trace): aggiunge una stringa nell'output di [Controllo API](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/).  
-   [Attendi](#Wait): attende il completamento dei criteri inclusi per l'[invio della richiesta](api-management-advanced-policies.md#SendRequest), il [recupero del valore dalla cache](api-management-caching-policies.md#GetFromCacheByKey) o il [flusso di controllo](api-management-advanced-policies.md#choose) prima di procedere.  
  
##  <a name="choose"></a>Flusso di controllo  
 Il criterio `choose` si applica alle istruzioni del criterio incluse in base al risultato della valutazione di espressioni booleane, simili a un costrutto if-then-else o switch in un linguaggio di programmazione.  
  
###  <a name="ChoosePolicyStatement"></a>Istruzione del criterio  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 Il criterio del flusso di controllo deve contenere almeno un elemento `<when/>`. L'elemento `<otherwise/>` è facoltativo. Le condizioni negli elementi `<when/>` vengono valutate in ordine di visualizzazione all'interno del criterio. Si applicano le istruzioni del criterio incluse all'interno del primo elemento `<when/>` con attributo di condizione uguale a `true`. I criteri inclusi all'interno dell'elemento `<otherwise/>`, se presente, vengono applicati se tutti gli attributi di condizione dell'elemento `<when/>` sono `false`.  
  
### <a name="examples"></a>Esempi  
  
####  <a name="ChooseExample"></a>Esempio  
 L'esempio seguente illustra un criterio [set-variable](api-management-advanced-policies.md#set-variable) e due criteri di flusso di controllo.  
  
 Il criterio di impostazione della variabile si trova nella sezione in ingresso e crea `isMobile`, una variabile di [contesto](api-management-policy-expressions.md#ContextVariables) booleana, che è impostata su true se l'intestazione della richiesta `User-Agent` contiene il testo `iPad` o `iPhone`.  
  
 Il primo criterio di flusso di controllo è disponibile anche nella sezione in ingresso e applica in modo condizionale uno dei due criteri [Imposta parametro di stringa della query](api-management-transformation-policies.md#SetQueryStringParameter) in base al valore della variabile di contesto `isMobile`.  
  
 Il secondo criterio di flusso di controllo si trova nella sezione in uscita e applica in modo condizionale il criterio [Converti XML in JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) quando `isMobile` è impostato su `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>Esempio  
 Questo esempio mostra come eseguire operazioni di filtro sui contenuti rimuovendo elementi di dati dalla risposta ricevuta dal servizio back-end quando si usa il prodotto `Starter`. Per una dimostrazione relativa alla configurazione e all'uso di questo criterio, vedere l'[episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 34:30. Iniziare dal minuto 31:50 per visualizzare una panoramica di [The Dark Sky Forecast API](https://developer.forecast.io/), l'API usata in questa dimostrazione.  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|choose|Elemento radice.|Sì|  
|when|La condizione da usare per le parti `if` o `ifelse` del criterio `choose`. Se il criterio `choose` ha più sezioni `when`, vengono valutate in modo sequenziale. Una volta che la `condition` di un elemento when risulta `true`, non vengono valutate altre condizioni `when`.|Sì|  
|otherwise|Contiene il frammento di criterio da usare se nessuna delle condizioni `when` viene valutata `true`.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|  
|---------------|-----------------|--------------|  
|condition="Boolean expression &#124; Boolean constant"|La costante o espressione booleana da valutare quando viene valutata l'istruzione del criterio contenente `when`.|Sì|  
  
###  <a name="ChooseUsage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="ForwardRequest"></a>Inoltra richiesta  
 Il criterio `forward-request` inoltra la richiesta in ingresso al servizio back-end specificato nel [contesto](api-management-policy-expressions.md#ContextVariables) della richiesta. L'URL del servizio back-end è specificato nelle [impostazioni](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) API e può essere modificato tramite il criterio [imposta servizio back-end](api-management-transformation-policies.md).  
  
> [!NOTE]
>  Se questo criterio viene rimosso, la richiesta non viene inoltrata al servizio back-end e i criteri nella sezione in uscita vengono valutati immediatamente dopo il completamento dei criteri nella sezione in ingresso.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="example"></a>Esempio  
 Il criterio a livello di API seguente inoltra tutte le richieste al servizio back-end con un intervallo di timeout di 60 secondi.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Esempio  
 Questo criterio a livello di operazione usa l'elemento `base` per ereditare il criterio di back-end dall'ambito di livello API padre.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Esempio  
 Questo criterio a livello di operazione inoltra in modo esplicito tutte le richieste al servizio back-end con un timeout di 120 e non eredita il criterio di back-end a livello API padre.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Esempio  
 Questo criterio a livello di operazione non inoltra le richieste al servizio back-end.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|forward-request|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|timeout="integer"|Intervallo di timeout in secondi prima che la chiamata al servizio back-end abbia esito negativo.|No|No timeout|  
|follow-redirects="true &#124; false"|Specifica se i reindirizzamenti dal servizio back-end sono seguiti dal gateway o restituiti al chiamante.|No|false|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** back-end  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="LimitConcurrency"></a>Limita concorrenza  
 Il criterio `limit-concurrency` previene ai criteri racchiusi l’esecuzione di un numero maggiore di richieste in un dato momento rispetto a quello specificato. In caso di superamento di tale numero, le nuove richieste avranno subito esito negativo con codice di stato 429 Troppe richieste.
  
###  <a name="LimitConcurrencyStatement"></a>Istruzione del criterio  
  
```xml  
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>esempi  
  
#### <a name="example"></a>Esempio  
 Nell'esempio seguente viene illustrato come limitare il numero di richieste inoltrate a un back-end in base al valore di una variabile di contesto.
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|    
|limita concorrenza|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|--------------|  
|key|Stringa. Espressione consentita. Specifica l'ambito di concorrenza. Può essere condivisa da più criteri.|Sì|N/D|  
|numero max|Un intero. Specifica un numero massimo di richieste autorizzate ad accedere al criterio.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  

##  <a name="log-to-eventhub"></a>Registra in Hub eventi  
 Il criterio `log-to-eventhub` invia messaggi nel formato specificato a un Hub eventi definito da un'entità Logger. Come suggerisce il nome, il criterio viene usato per il salvataggio di informazioni selezionate sul contesto di richiesta o risposta per l'analisi online o offline.  
  
> [!NOTE]
>  Per una guida dettagliata sulla configurazione di un hub eventi e la registrazione di eventi, vedere [Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>Esempio  
 È possibile usare qualsiasi stringa come valore da registrare in Hub eventi. In questo esempio la data e l'ora, il nome del servizio di distribuzione, l'ID della richiesta, l'indirizzo IP e il nome dell'operazione per tutte le chiamate in entrata vengono registrati nel Logger dell'hub eventi registrato con l'ID `contoso-logger`.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|log-to-eventhub|Elemento radice. Il valore di questo elemento è la stringa per la registrazione all'hub eventi.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|  
|---------------|-----------------|--------------|  
|logger-id|ID del Logger registrato con il servizio Gestione API.|Sì|  
|partition-id|Specifica l'indice della partizione a cui i messaggi vengono inviati.|facoltativo. Questo attributo non può essere usato se si usa `partition-key`.|  
|partition-key|Specifica il valore usato per l'assegnazione della partizione quando vengono inviati i messaggi.|facoltativo. Questo attributo non può essere usato se si usa `partition-id`.|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  

##  <a name="mock-response"></a> Restituisci risposta  
Il criterio `mock-response`, come implica il nome, viene usato per restituire API e operazioni. Interrompe la normale esecuzione della pipeline e restituisce una risposta fittizia direttamente al chiamante. Il criterio cerca sempre di restituire risposte della massima fedeltà. Include esempi di contenuto di risposta ogni volta che è possibile. Nelle situazioni in cui vengono forniti schemi ma non esempi, genera risposte di esempio dagli schemi. Se non sono presenti né esempi né schemi, restituisce risposte senza contenuto.
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>esempi  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatoria|  
|-------------|-----------------|--------------|  
|mock-response|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|--------------|  
|status-code|Specifica il codice di stato della risposta e viene usato per selezionare l'esempio o lo schema corrispondente.|No|200|  
|content-type|Specifica il valore di intestazione della risposta `Content-Type` e viene usato per selezionare l'esempio o lo schema corrispondente.|No|None|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti

##  <a name="Retry"></a> Riprova  
 Il criterio `retry` esegue i criteri figlio una volta e quindi ritenta l'esecuzione degli stessi fino a quando il tentativo `condition` diventa `false` o fino a esaurimento del tentativo `count`.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>Esempio  
 Nella richiesta di esempio seguente l'inoltro viene ripetuto fino a dieci volte usando un algoritmo di ripetizione esponenziale. Poiché `first-fast-retry` è impostato su false, tutti i tentativi sono soggetti all'algoritmo di ripetizione esponenziale.  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|retry|Elemento radice. Può contenere tutti gli altri criteri come elementi figlio.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|condition|Valore letterale booleano o [espressione](api-management-policy-expressions.md) che specifica se i tentativi devono essere interrotti (`false`) o devono continuare (`true`).|Sì|N/D|  
|count|Numero positivo che specifica il numero massimo di tentativi da eseguire.|Sì|N/D|  
|interval|Numero positivo in secondi che specifica l'intervallo di attesa tra i tentativi di ripetizione.|Sì|N/D|  
|max-interval|Un numero positivo che specifica l'intervallo di attesa massimo tra i tentativi di ripetizione. Viene usato per implementare un algoritmo di ripetizione esponenziale.|No|N/D|  
|delta|Numero positivo in secondi che specifica l'incremento dell'intervallo di attesa. Viene usato per implementare gli algoritmi di ripetizione lineari ed esponenziali.|No|N/D|  
|first-fast-retry|Se impostato su `true`, il primo tentativo di ripetizione viene eseguito immediatamente.|No|`false`|  
  
> [!NOTE]
>  Se è specificato solo `interval`, vengono eseguiti tentativi a intervallo **fisso**.  
>  Se vengono specificati solo `interval` e `delta`, viene usato un algoritmo di ripetizione a intervalli **lineari**, in cui il tempo di attesa tra i tentativi viene calcolato secondo la formula seguente: `interval + (count - 1)*delta`.  
>  Se vengono specificati `interval`, `max-interval` e `delta`,viene applicato un algoritmo di ripetizione a intervalli **esponenziali**, in cui il tempo di attesa tra i tentativi cresce in modo esponenziale dal valore `interval` al valore `max-interval`, secondo la formula seguente: `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti. Si noti che le restrizioni sull'uso dei criteri figlio verranno ereditate da questo criterio.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="ReturnResponse"></a>Restituisci risposta  
 Il criterio `return-response` interrompe l'esecuzione della pipeline e restituisce al chiamante una risposta predefinita o personalizzata. La risposta predefinita è `200 OK`, senza corpo. La risposta personalizzata può essere specificata tramite una variabile di contesto o le istruzioni del criterio. Quando entrambi sono specificati, la risposta contenuta all'interno della variabile di contesto viene modificata tramite le istruzioni del criterio prima di essere restituita al chiamante.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|return-response|Elemento radice.|Sì|  
|set-header|Istruzione del criterio.[set-header](api-management-transformation-policies.md#SetHTTPheader).|No|  
|set-body|Istruzione del criterio.[set-body](api-management-transformation-policies.md#SetBody).|No|  
|set-status|Istruzione del criterio [set-status](api-management-advanced-policies.md#SetStatus).|No|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|  
|---------------|-----------------|--------------|  
|response-variable-name|Nome della variabile di contesto a cui fa riferimento, ad esempio, un criterio di upstream [send-request](api-management-advanced-policies.md#SendRequest) e contenente un oggetto `Response`.|facoltativo.|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="SendOneWayRequest"></a>Invia richiesta unidirezionale  
 Il criterio `send-one-way-request` invia la richiesta specificata all'URL specificato senza attendere una risposta.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>Esempio  
 Questo criterio di esempio illustra come usare il criterio `send-one-way-request` per inviare un messaggio a una chat di Slack, se il codice della risposta HTTP è maggiore o uguale a 500. Per altre informazioni relative a questo esempio, vedere [Uso di servizi esterni dal servizio Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|send-one-way-request|Elemento radice.|Sì|  
|URL|URL della richiesta.|No if mode=copy; otherwise yes.|  
|statico|Metodo HTTP usato nella richiesta.|No if mode=copy; otherwise yes.|  
|intestazione|Intestazione della richiesta. Usare più elementi di intestazione per più intestazioni della richiesta.|No|  
|body|Corpo della richiesta.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determina se questa è una nuova richiesta o una copia della richiesta corrente. In modalità in uscita, mode=copy non avvia il corpo della richiesta.|No|Nuovo|  
|name|Specifica il nome dell'intestazione da impostare.|Sì|N/D|  
|exists-action|Specifica l'azione da eseguire quando l'intestazione è già specificata. Questo attributo deve avere uno dei valori seguenti.<br /><br /> -   override - sostituisce il valore dell'intestazione esistente.<br />-   skip - non sostituisce il valore dell'intestazione esistente.<br />-   append - aggiunge il valore dell'intestazione esistente.<br />-   delete - elimina l'intestazione dalla richiesta.<br /><br /> Se è impostato su `override`, l'integrazione di più voci con lo stesso nome avrà come risultato l'impostazione dell'intestazione in base a tutte le voci, che saranno elencate più volte. Nel risultato saranno impostati solo i valori elencati.|No|override|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="SendRequest"></a> Invio richiesta  
 Il criterio `send-request` invia la richiesta fornita all'URL specificato, attendendo non oltre il valore di timeout impostato.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>Esempio  
 Questo esempio mostra un metodo per verificare un token di riferimento con un server di autorizzazione. Per altre informazioni relative a questo esempio, vedere [Uso di servizi esterni dal servizio Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<inbound>  
  <!-- Extract Token from Authorization header parameter -->  
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />  
  
  <!-- Send request to Token Server to validate token (see RFC 7662) -->  
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">  
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>  
    <set-method>POST</set-method>  
    <set-header name="Authorization" exists-action="override">  
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>  
    </set-header>  
    <set-header name="Content-Type" exists-action="override">  
      <value>application/x-www-form-urlencoded</value>  
    </set-header>  
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>  
  </send-request>  
  
  <choose>  
        <!-- Check active property in response -->  
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
            <!-- Return 401 Unauthorized with http-problem payload -->  
            <return-response response-variable-name="existing response variable">  
                <set-status code="401" reason="Unauthorized" />  
                <set-header name="WWW-Authenticate" exists-action="override">  
                    <value>Bearer error="invalid_token"</value>  
                </set-header>  
            </return-response>  
        </when>  
    </choose>  
  <base />  
</inbound>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|send-request|Elemento radice.|Sì|  
|URL|URL della richiesta.|No if mode=copy; otherwise yes.|  
|statico|Metodo HTTP usato nella richiesta.|No if mode=copy; otherwise yes.|  
|intestazione|Intestazione della richiesta. Usare più elementi di intestazione per più intestazioni della richiesta.|No|  
|body|Corpo della richiesta.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determina se questa è una nuova richiesta o una copia della richiesta corrente. In modalità in uscita, mode=copy non avvia il corpo della richiesta.|No|Nuovo|  
|response-variable-name="string"|Se non è presente, viene usato `context.Response`.|No|N/D|  
|timeout="integer"|Intervallo di timeout in secondi prima che la chiamata all'URL abbia esito negativo.|No|60|  
|ignore-error|Se impostato su true e la richiesta restituisce un errore:<br /><br /> - Se è stato specificato response-variable-name, questo conterrà un valore null.<br />- Se response-variable-name non è stato specificato, context.Request non verrà aggiornato.|No|false|  
|name|Specifica il nome dell'intestazione da impostare.|Sì|N/D|  
|exists-action|Specifica l'azione da eseguire quando l'intestazione è già specificata. Questo attributo deve avere uno dei valori seguenti.<br /><br /> -   override - sostituisce il valore dell'intestazione esistente.<br />-   skip - non sostituisce il valore dell'intestazione esistente.<br />-   append - aggiunge il valore dell'intestazione esistente.<br />-   delete - elimina l'intestazione dalla richiesta.<br /><br /> Se è impostato su `override`, l'integrazione di più voci con lo stesso nome avrà come risultato l'impostazione dell'intestazione in base a tutte le voci, che saranno elencate più volte. Nel risultato saranno impostati solo i valori elencati.|No|override|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="SetHttpProxy"></a>Impostare il proxy HTTP  
 Il criterio `proxy` consente di indirizzare le richieste inoltrate ai back-end tramite un proxy HTTP. È supportato solo HTTP (non HTTPS) tra il gateway e il proxy. Solo autenticazione Basic e NTLM.
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>Esempio  
Si noti l'utilizzo di [proprietà](api-management-howto-properties.md) come valori di nome utente e password per evitare di archiviare informazioni riservate nel documento dei criteri.  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|proxy|Elemento radice|Sì|  

### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|url="string"|URL del proxy nel formato http://host:port.|Sì|N/D |  
|username="string"|Nome utente da usare per l'autenticazione con il proxy.|No|N/D |  
|password="string"|Password da usare per l'autenticazione con il proxy.|No|N/D |  

### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** tutti gli ambiti  

##  <a name="SetRequestMethod"></a> Impostare il metodo di richiesta  
 Il criterio `set-method` consente di modificare il metodo di richiesta HTTP per una richiesta.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>Esempio  
 Questo criterio di esempio che usa il criterio `set-method` mostra un esempio di invio di un messaggio a una chat di Slack, se il codice della risposta HTTP è maggiore o uguale a 500. Per altre informazioni relative a questo esempio, vedere [Uso di servizi esterni dal servizio Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|set-method|Elemento radice. Il valore dell'elemento specifica il metodo HTTP.|Sì|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="SetStatus"></a> Impostare il codice di stato  
 Il criterio `set-status` modifica il codice di stato HTTP sul valore specificato.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>Esempio  
 Questo esempio illustra come restituire una risposta 401 se il token di autorizzazione non è valido. Per altre informazioni, vedere [Uso di servizi esterni dal servizio Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
    <return-response response-variable-name="existing response variable">  
      <set-status code="401" reason="Unauthorized" />  
      <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
      </set-header>  
    </return-response>  
  </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|set-status|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|code="integer"|Il codice di stato HTTP da restituire.|Sì|N/D|  
|reason="string"|Descrizione del motivo per la restituzione del codice di stato.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** outbound, backend, on-error  
-   **Ambiti del criterio:** tutti gli ambiti  

##  <a name="set-variable"></a>Impostare una variabile  
 Il criterio `set-variable` dichiara una variabile di [contesto](api-management-policy-expressions.md#ContextVariables) e assegna a essa un valore specificato tramite un'[espressione](api-management-policy-expressions.md) o un valore letterale di stringa. Se l'espressione contiene un valore letterale, questo verrà convertito in una stringa e il tipo di valore sarà `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a>Istruzione del criterio  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a>Esempio  
 L'esempio seguente illustra un criterio di impostazione della variabile nella sezione in ingresso. Il criterio di impostazione della variabile crea `isMobile`, una variabile di [contesto](api-management-policy-expressions.md#ContextVariables) booleana, che è impostata su true se l'intestazione della richiesta `User-Agent` contiene il testo `iPad` o `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|set-variable|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|  
|---------------|-----------------|--------------|  
|name|Nome della variabile.|Sì|  
|value|Valore della variabile. Può essere un'espressione o un valore letterale.|Sì|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
-   **Ambiti del criterio:** tutti gli ambiti  
  
###  <a name="set-variableAllowedTypes"></a>Tipi consentiti  
 Le espressioni usate nel criterio `set-variable` devono restituire uno dei seguenti tipi di base.  
  
-   System.Boolean  
-   System.SByte  
-   System.Byte  
-   System.UInt16  
-   System.UInt32  
-   System.UInt64  
-   System.Int16  
-   System.Int32  
-   System.Int64  
-   System.Decimal  
-   System.Single  
-   System.Double  
-   System.Guid  
-   System.String  
-   System.Char  
-   System.DateTime  
-   System.TimeSpan  
-   System.Byte?  
-   System.UInt16?  
-   System.UInt32?  
-   System.UInt64?  
-   System.Int16?  
-   System.Int32?  
-   System.Int64?  
-   System.Decimal?  
-   System.Single?  
-   System.Double?  
-   System.Guid?  
-   System.String?  
-   System.Char?  
-   System.DateTime?  

##  <a name="Trace"></a> Traccia  
 Il criterio `trace` aggiunge una stringa nell'output di [Controllo API](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/). Il criterio verrà eseguito solo quando la traccia è attivata, ad esempio quando è presente l'intestazione della richiesta `Ocp-Apim-Trace` ed è impostata su `true` e quando è presente l'intestazione della richiesta `Ocp-Apim-Subscription-Key` e contiene una chiave valida associata all'account amministratore.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|trace|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|una sezione source|Valore letterale della stringa significativo per il visualizzatore di tracce e che specifica l'origine del messaggio.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** tutti gli ambiti  
  
##  <a name="Wait"></a> Attesa  
 Il criterio `wait` esegue i criteri figlio immediati in parallelo e attende che tutti o uno dei relativi criteri figlio immediati vengano completati prima di terminare la sua attività. I criteri di attesa possono avere come criteri figlio immediati i criteri [Invio della richiesta](api-management-advanced-policies.md#SendRequest), [Recupero del valore dalla cache](api-management-caching-policies.md#GetFromCacheByKey) e [Flusso di controllo](api-management-advanced-policies.md#choose).  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>Esempio  
 L'esempio seguente contiene due criteri `choose` come criteri figlio immediato dei criteri `wait`. Ognuno di questi criteri `choose` viene eseguito in parallelo. Ogni criterio `choose` tenta di recuperare un valore memorizzato nella cache. Se si verifica un mancato riscontro nella cache, viene chiamato un servizio di back-end per fornire il valore. In questo esempio il criterio `wait` non si completa fino al completamento di tutti i relativi criteri figlio immediati, poiché l'attributo `for` è impostato su `all`.   In questo esempio le variabili di contesto (`execute-branch-one`, `value-one`, `execute-branch-two` e `value-two`) vengono dichiarate all'esterno dell'ambito di questo criterio di esempio.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|wait|Elemento radice. Può contenere come elementi figlio solo i criteri `send-request`, `cache-lookup-value` e `choose`.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|Descrizione|Obbligatorio|Default|  
|---------------|-----------------|--------------|-------------|  
|for|Determina se il criterio `wait` attende il completamento di tutti o solo uno dei criteri figlio immediati. I valori consentiti sono i seguenti:<br /><br /> -   `all`: consente di attendere il completamento di tutti i criteri figlio immediati<br />-   any: consente di attendere il completamento di uno dei criteri figlio immediati. Dopo il completamento del primo criterio figlio immediato, il criterio `wait` si completa e l'esecuzione di qualsiasi altro criterio figlio immediato viene arrestata.|No|tutti|  
  
### <a name="usage"></a>Utilizzo  
 
Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, back-end  
-   **Ambiti del criterio:** tutti gli ambiti  
  
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:
+ [Criteri in Gestione API](api-management-howto-policies.md) 
+ [Espressioni di criteri](api-management-policy-expressions.md)
+ [Informazioni di riferimento per i criteri](api-management-policy-reference.md) per un elenco completo di istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)   
