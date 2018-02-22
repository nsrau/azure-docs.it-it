---
title: Criteri di trasformazione di Gestione API di Azure | Documentazione Microsoft
description: Informazioni sui criteri di trasformazione disponibili in Gestione API di Azure.
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 18b0a7d15c50ee147690063ac251f815c7fa34be
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="api-management-transformation-policies"></a>Criteri di trasformazione di Gestione API
Questo argomento fornisce un riferimento per i criteri di Gestione API seguenti. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="TransformationPolicies"></a> Criteri di trasformazione  
  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML) : converte il corpo della richiesta o della risposta da JSON a XML.  
  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON) : converte il corpo della richiesta o della risposta da XML a JSON.  
  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody) : trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.  
  
-   [Maschera URL nel contenuto](api-management-transformation-policies.md#MaskURLSContent) : riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento al collegamento equivalente tramite il gateway.  
  
-   [set-backend-service](api-management-transformation-policies.md#SetBackendService) : consente di cambiare il servizio back-end per una richiesta in ingresso.  
  
-   [set-body](api-management-transformation-policies.md#SetBody) : consente di impostare il corpo del messaggio per richieste in ingresso e in uscita.  
  
-   [Imposta intestazione HTTP](api-management-transformation-policies.md#SetHTTPheader) : assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.  
  
-   [Imposta parametro di stringa della query](api-management-transformation-policies.md#SetQueryStringParameter) : aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.  
  
-   [rewrite-uri](api-management-transformation-policies.md#RewriteURL) : converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.  
  
-   [Trasforma XML usando una trasformazione XSLT](api-management-transformation-policies.md#XSLTransform) - applica una trasformazione da XSL a XML nel corpo della richiesta o della risposta.  
  
##  <a name="ConvertJSONtoXML"></a> Converti JSON in XML  
 Il criterio `json-to-xml` converte il corpo della richiesta o della risposta da JSON a XML.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false"/>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
        <json-to-xml apply="always" consider-accept-header="false" />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|json-to-xml|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|NOME|DESCRIZIONE|Obbligatoria|Default|  
|----------|-----------------|--------------|-------------|  
|apply|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   always - applica sempre la conversione.<br />-   content-type-json - applica la conversione solo se l'intestazione Content-Type della risposta indica la presenza di JSON.|Sì|N/D|  
|consider-accept-header|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   true - applica la conversione se JSON è richiesto nell'intestazione Accept della richiesta.<br />-   false - applica sempre la conversione.|No |true|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, on-error  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="ConvertXMLtoJSON"></a> Converti XML in JSON  
 Il criterio `xml-to-json` converte il corpo della richiesta o della risposta da XML a JSON. Il criterio può essere applicato per modernizzare le API basate su servizi Web back-end solo di tipo XML.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|xml-to-json|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|kind|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   javascript-friendly - il JSON convertito ha un formato intuitivo per gli sviluppatori JavaScript.<br />-   direct - il JSON convertito riflette la struttura del documento XML originario.|Sì|N/D|  
|apply|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   always - esegue sempre la conversione.<br />-   content-type-xml - applica la conversione solo se l'intestazione Content-Type della risposta indica la presenza di XML.|Sì|N/D|  
|consider-accept-header|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   true - applica la conversione se XML è richiesto nell'intestazione Accept della richiesta.<br />-   false - applica sempre la conversione.|No |true|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, on-error  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="Findandreplacestringinbody"></a> Trova e sostituisci stringa nel corpo  
 Il criterio `find-and-replace` trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<find-and-replace from="what to replace" to="replacement" />  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<find-and-replace from="notebook" to="laptop" />  
```  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|find-and-replace|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|from|Stringa da cercare.|Sì|N/D|  
|to|La stringa di sostituzione. Specificare una stringa di sostituzione con lunghezza zero per rimuovere la stringa di ricerca.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="MaskURLSContent"></a> Maschera URL nel contenuto  
 Il criterio `redirect-content-urls` riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento al collegamento equivalente tramite il gateway. Usare la sezione outbound per riscrivere i collegamenti al corpo della risposta affinché facciano riferimento al gateway. Usare la sezione inbound per ottenere l'effetto opposto.  
  
> [!NOTE]
>  Questo criterio non modifica i valori delle intestazioni, ad esempio le intestazioni `Location`. Per modificare i valori delle intestazioni, usare il criterio [set-header](api-management-transformation-policies.md#SetHTTPheader).  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<redirect-content-urls />  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<redirect-content-urls />  
```  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|redirect-content-urls|Elemento radice.|Sì|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="SetBackendService"></a> Imposta servizio back-end  
 Usare il criterio `set-backend-service` per reindirizzare una richiesta in ingresso a un back-end diverso da quello specificato nelle impostazioni dell'API per l'operazione. Questo criterio cambia l'URL di base del servizio back-end della richiesta in arrivo con quello specificato nel criterio.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-backend-service base-url="base URL of the backend service" />  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <choose>  
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">  
                <set-backend-service base-url="http://contoso.com/api/8.2/" />  
            </when>  
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">  
                <set-backend-service base-url="http://contoso.com/api/9.1/" />  
            </when>  
        </choose>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
In questo esempio il criterio del servizio back-end indirizza le richieste in base al valore di versione passato nella stringa di query a un servizio back-end diverso rispetto a quello specificato nell'API.
  
Inizialmente l'URL di base del servizio back-end è quello specificato nelle impostazioni dell'API. Pertanto, l'URL della richiesta `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` diventa `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` dove `http://contoso.com/api/10.4/` è l'URL del servizio back-end specificato nelle impostazioni dell'API.  
  
Quando viene applicata l'istruzione del criterio [<choose\>](api-management-advanced-policies.md#choose), l'URL di base del servizio back-end può essere di nuovo modificato in `http://contoso.com/api/8.2` o `http://contoso.com/api/9.1`, a seconda del valore del parametro della query versione richiesta. Se, ad esempio, il valore è `"2013-15"`, l'URL della richiesta finale sarà `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.  
  
Se occorre un'ulteriore trasformazione della richiesta, è possibile usare altri [criteri di trasformazione](api-management-transformation-policies.md#TransformationPolicies). Ad esempio, per rimuovere il parametro della query di versione una volta instradata la richiesta verso un back-end specifico, ad esempio, è possibile usare il criterio [Imposta parametro della stringa di query](api-management-transformation-policies.md#SetQueryStringParameter) per rimuovere l'attributo della versione divenuta ridondante.  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" /> 
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
In questo esempio il criterio indirizza la richiesta a un back-end dell'infrastruttura del servizio tramite la stringa di query dell'ID utente come chiave di partizione e tramite la replica della partizione primaria.  

### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|set-backend-service|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|base-url|Nuovo URL di base del servizio back-end.|No |N/D|  
|backend-id|Identificatore del back-end verso cui avviene il routing.|No |N/D|  
|sf-partition-key|Applicabile solo quando il back-end è un servizio di Service Fabric e viene specificato tramite "backend-id". Usato per risolvere una partizione specifica dal servizio di risoluzione del nome.|No |N/D|  
|sf-replica-type|Applicabile solo quando il back-end è un servizio di Service Fabric e viene specificato tramite "backend-id". Controlla se la richiesta deve passare alla replica primaria o secondaria di una partizione. |No |N/D|    
|sf-resolve-condition|Applicabile solo quando il back-end è un servizio di Service Fabric. Condizione che identifica se la chiamata al back-end di Service Fabric deve essere ripetuta con una nuova risoluzione.|No |N/D|    
|sf-service-instance-name|Applicabile solo quando il back-end è un servizio di Service Fabric. Consente di modificare le istanze del servizio durante il runtime. |No |N/D|    
|sf-listener-name|Applicabile solo quando il back-end è un servizio di Service Fabric e viene specificato tramite "backend-id". Reliable Services di Service Fabric consente di creare più listener in un servizio. Questo attributo viene usato per selezionare un listener specifico quando un'istanza di Reliable Services back-end ha più di un listener. Se questo attributo non viene specificato, il servizio Gestione API tenterà di usare un listener senza nome. Un listener senza nome è tipico di un'istanza di Reliable Services con un solo listener. |No |N/D|  

### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, backend  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="SetBody"></a> Imposta corpo  
 Il criterio `set-body` consente di impostare il corpo del messaggio per le richieste in ingresso e in uscita. Per accedere al corpo del messaggio è possibile usare la proprietà `context.Request.Body` o `context.Response.Body`, a seconda che il criterio sia nella sezione inbound o outbound.  
  
> [!IMPORTANT]
>  Si noti che, per impostazione predefinita, quando si accede al corpo del messaggio con `context.Request.Body` o `context.Response.Body`, il corpo del messaggio originale viene perso e deve essere impostato riportandolo nell'espressione. Per mantenere il contenuto del corpo, impostare il parametro `preserveContent` su `true` quando si accede al messaggio. Se `preserveContent` è impostato su `true` e l'espressione restituisce un corpo diverso, viene usato il corpo restituito.  
>   
>  Tenere presente le considerazioni seguenti quando si usa il criterio `set-body`.  
>   
>  -   Se il criterio `set-body` viene usato per restituire un corpo nuovo o aggiornato non è necessario impostare `preserveContent` su `true`, perché il nuovo contenuto del corpo viene fornito in modo esplicito.  
> -   Non ha senso mantenere il contenuto di una risposta nella pipeline in ingresso, poiché non è ancora stata ricevuta alcuna risposta.  
> -   Non ha senso neanche mantenere il contenuto di una risposta nella pipeline in uscita perché a questo punto la richiesta è già stata inviata al back-end.  
> -   Se questo criterio viene usato quando non vi è alcun corpo del messaggio, ad esempio in un'operazione GET in ingresso, viene generata un'eccezione.  
  
 Per altre informazioni, vedere le sezioni `context.Request.Body`, `context.Response.Body`e `IMessage` nella tabella [Variabile di contesto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-body>new body value as text</set-body>  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="literal-text-example"></a>Esempio di testo letterale  
  
```xml  
<set-body>Hello world!</set-body>  
```  
  
#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Esempio di accesso al corpo come stringa. Si noti che il corpo della richiesta originale viene mantenuto per potervi accedere più avanti nella pipeline.
  
```xml  
<set-body>  
@{   
    string inBody = context.Request.Body.As<string>(preserveContent: true);   
    if (inBody[0] =='c') {   
        inBody[0] = 'm';   
    }   
    return inBody;   
}  
</set-body>  
```  
  
#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Esempio di accesso al corpo come JObject. Si noti che il corpo della richiesta originale non viene mantenuto e l'accesso a tale corpo più avanti nella pipeline genererà un'eccezione.  
  
```xml  
<set-body>   
@{   
    JObject inBody = context.Request.Body.As<JObject>();   
    if (inBody.attribute == <tag>) {   
        inBody[0] = 'm';   
    }   
    return inBody.ToString();   
}   
</set-body>  
  
```  
  
#### <a name="filter-response-based-on-product"></a>Filtrare la risposta in base al prodotto  
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

### <a name="using-liquid-templates-with-set-body"></a>Uso di modelli Liquid con set body 
Il criterio `set-body` può essere configurato per l'uso del linguaggio di modellazione [Liquid](https://shopify.github.io/liquid/basics/introduction/) per trasformare il corpo di una richiesta o di una risposta. Può essere molto efficace se si desidera modificare completamente il formato del messaggio.

> [!IMPORTANT]
> L'implementazione di Liquid usato nel criterio `set-body` è configurato in "modalità C#". Questo è particolarmente importante quando si eseguono operazioni quali l'applicazione del filtro. Ad esempio, l'uso di un filtro data richiede l'uso della convenzione Pascal e della formattazione delle date C#, ad esempio:
>
> {{body.foo.startDateTime| Data:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Per eseguire correttamente l'associazione a un corpo XML utilizzando il modello Liquid, usare un criterio `set-header` per impostare il tipo di contenuto su application/xml, text/xml (o su qualsiasi tipo che termini con +xml); per un corpo JSON, deve essere application/json, testo/json (o qualsiasi tipo che termini con +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Convertire JSON in SOAP usando un modello Liquid
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="tranform-json-using-a-liquid-template"></a>Trasformare JSON usando un modello Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|set-body|Elemento radice. Contiene il testo del corpo o un'espressione che restituisce un corpo.|Sì|  

### <a name="properties"></a>Properties  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|template|Consente di modificare la modalità di modello in cui verrà eseguito il criterio del corpo impostato. Al momento, l'unico valore supportato è:<br /><br />-liquid - i criteri del corpo impostati useranno il motore del modello liquidi |No |liquid|  

Per accedere alle informazioni sulla richiesta e la risposta, il modello Liquid può essere associato a un oggetto di contesto con le proprietà seguenti: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, back-end  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="SetHTTPheader"></a> Imposta intestazione HTTP  
 Il criterio `set-header` assegna un valore a un'intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.  
  
 Inserisce un elenco di intestazioni HTTP in un messaggio HTTP. Se inserito in una pipeline in entrata, questo criterio imposta le intestazioni HTTP per la richiesta passata al servizio di destinazione. Se inserito in una pipeline in uscita, questo criterio imposta le intestazioni HTTP per la risposta inviata al client del gateway.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-header name="header name" exists-action="override | skip | append | delete">  
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->  
</set-header>  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="example"></a>Esempio  
  
```xml  
<set-header name="some header name" exists-action="override">  
    <value>20</value>   
</set-header>  
```  
  
#### <a name="forward-context-information-to-the-backend-service"></a>Inoltro di informazioni di contesto al servizio back-end  
 Questo esempio illustra come applicare criteri a livello di API per fornire informazioni di contesto al servizio back-end. Per una dimostrazione relativa alla configurazione e all'uso di questo criterio, vedere l'[episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 10:30. Al minuto 12:10 è illustrato come richiamare un'operazione nel portale per sviluppatori, dove è possibile vedere all'opera il criterio in questione.  
  
```xml  
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->  
<set-header name="x-request-context-data" exists-action="override">  
  <value>@(context.User.Id)</value>  
  <value>@(context.Deployment.Region)</value>  
</set-header>  
```  
  
 Per altre informazioni, vedere [Espressioni di criteri](api-management-policy-expressions.md) e [Variabile di contesto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|set-header|Elemento radice.|Sì|  
|value|Specifica il valore dell'intestazione da impostare. Se occorrono più intestazioni con lo stesso nome, aggiungere altri elementi `value`.|Sì|  
  
### <a name="properties"></a>Properties  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|exists-action|Specifica l'azione da eseguire quando l'intestazione è già specificata. Questo attributo deve avere uno dei valori seguenti.<br /><br /> -   override - sostituisce il valore dell'intestazione esistente.<br />-   skip - non sostituisce il valore dell'intestazione esistente.<br />-   append - aggiunge il valore dell'intestazione esistente.<br />-   delete - elimina l'intestazione dalla richiesta.<br /><br /> Se è impostato su `override`, l'integrazione di più voci con lo stesso nome avrà come risultato l'impostazione dell'intestazione in base a tutte le voci, che saranno elencate più volte. Nel risultato saranno impostati solo i valori elencati.|No |override|  
|name|Specifica il nome dell'intestazione da impostare.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:**in ingresso, in uscita, back-end, on-error  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="SetQueryStringParameter"></a> Imposta parametro di stringa della query  
 Il criterio `set-query-parameter` aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta. Può essere usato per passare i parametri di query previsti dal servizio back-end che sono facoltativi o non sono mai presenti nella richiesta.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<set-query-parameter name="param name" exists-action="override | skip | append | delete">  
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->  
</set-query-parameter>  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="example"></a>Esempio  
  
```xml  
  
<set-query-parameter>  
  <parameter name="api-key" exists-action="skip">  
    <value>12345678901</value>  
  </parameter>  
  <!-- for multiple parameters with the same name add additional value elements -->  
</set-query-parameter>  
  
```  
  
#### <a name="forward-context-information-to-the-backend-service"></a>Inoltro di informazioni di contesto al servizio back-end  
 Questo esempio illustra come applicare criteri a livello di API per fornire informazioni di contesto al servizio back-end. Per una dimostrazione relativa alla configurazione e all'uso di questo criterio, vedere l'[episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 10:30. Al minuto 12:10 è illustrato come richiamare un'operazione nel portale per sviluppatori, dove è possibile vedere all'opera il criterio in questione.  
  
```xml  
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->  
<set-query-parameter name="x-product-name" exists-action="override">  
  <value>@(context.Product.Name)</value>  
</set-query-parameter>  
  
```  
  
 Per altre informazioni, vedere [Espressioni di criteri](api-management-policy-expressions.md) e [Variabile di contesto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|set-query-parameter|Elemento radice.|Sì|  
|value|Specifica il valore del parametro di query da impostare. Se occorrono più parametri di query con lo stesso nome, aggiungere altri elementi `value`.|Sì|  
  
### <a name="properties"></a>Properties  
  
|NOME|DESCRIZIONE|Obbligatoria|Predefinito|  
|----------|-----------------|--------------|-------------|  
|exists-action|Specifica l'azione da eseguire quando il parametro di query è già specificato. Questo attributo deve avere uno dei valori seguenti.<br /><br /> -   override - sostituisce il valore del parametro esistente.<br />-   skip - non sostituisce il valore del parametro di query esistente.<br />-   append - aggiunge il valore del parametro di query esistente.<br />-   delete - elimina il parametro di query dalla richiesta.<br /><br /> Se è impostato su `override`, l'integrazione di più voci con lo stesso nome avrà come risultato l'impostazione del parametro di query in base a tutte le voci, che saranno elencate più volte. Nel risultato saranno impostati solo i valori elencati.|No |override|  
|name|Specifica il nome del parametro di query da impostare.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, backend  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="RewriteURL"></a> Riscrivi URL  
 Il criterio `rewrite-uri` converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web, come mostrato nell'esempio seguente.  
  
-   URL pubblico - `http://api.example.com/storenumber/ordernumber`  
  
-   URL richiesta - `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`  
  
 Il criterio può essere usato quando un URL ideale per utenti e/o per il browser deve essere trasformato nel formato di URL previsto dal servizio Web. Il criterio deve essere applicato soltanto quando viene esposto un formato di URL alternativo, ad esempio URL semplici, URL RESTful, URL ideali per gli utenti o URL ideali per SEO, che sono URL puramente strutturali, non includono una stringa di query ma contengono solo il percorso della risorsa, dopo lo schema e l'autorità. Sono spesso usati a fini estetici, di usabilità o di ottimizzazione dei motori di ricerca (SEO, Search Engine Optimization).  
  
> [!NOTE]
>  Il criterio consente di aggiungere solo parametri delle stringhe di query. Non è possibile aggiungere un parametro di percorso di modello aggiuntivo nell'URL riscritto.  

### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/put" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
<!-- Resulting URL will be /put?c=d -->
```  
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/put" copy-unmatched-params="false" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|rewrite-uri|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Attributo|DESCRIZIONE|Obbligatoria|Predefinito|  
|---------------|-----------------|--------------|-------------|  
|template|URL effettivo del servizio Web con eventuali parametri delle stringhe di query. Quando si usano le espressioni, l'intero valore deve essere un'espressione.|Sì|N/D|  
|copy-unmatched-params|Specifica se i parametri di query nella richiesta in ingresso non presenti nel modello di URL originale vengono aggiunti all'URL definito dal modello di riscrittura|No |true|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** prodotto, API, operazione  
  
##  <a name="XSLTransform"></a>Trasforma XML usando XSLT  
 Il criterio `Transform XML using an XSLT` applica una trasformazione XSL all'XML nel corpo della richiesta o della risposta.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<xsl-transform>  
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>  
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">  
        <xsl:output method="xml" indent="yes" />  
        <xsl:param name="User-Agent" />  
        <xsl:template match="* | @* | node()">  
            <xsl:copy>  
                <xsl:if test="self::* and not(parent::*)">  
                    <xsl:attribute name="User-Agent">  
                        <xsl:value-of select="$User-Agent" />  
                    </xsl:attribute>  
                </xsl:if>  
                <xsl:apply-templates select="* | @* | node()" />  
            </xsl:copy>  
        </xsl:template>  
    </xsl:stylesheet>  
  </xsl-transform>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
  <inbound>  
      <base />  
  </inbound>  
  <outbound>  
      <base />  
      <xsl-transform>  
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">  
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />  
            <!-- Copy all nodes directly-->  
            <xsl:template match="node()| @*|*">  
                <xsl:copy>  
                    <xsl:apply-templates select="@* | node()|*" />  
                </xsl:copy>  
            </xsl:template>  
        </xsl:stylesheet>  
    </xsl-transform>  
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|NOME|DESCRIZIONE|Obbligatoria|  
|----------|-----------------|--------------|  
|xsl-transform|Elemento radice.|Sì|  
|parametro|Consente di definire le variabili usate nella trasformazione|No |  
|xsl:stylesheet|Elemento del foglio di stile principale. Tutti gli elementi e gli attributi definiti rispettano le [specifiche XSLT](http://www.w3.org/TR/xslt) standard|Sì|  
  
### <a name="usage"></a>Uso  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli argomenti seguenti:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Informazioni di riferimento sui criteri](api-management-policy-reference.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)   
