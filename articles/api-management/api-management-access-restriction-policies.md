---
title: Criteri di limitazione dell'accesso di Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri di limitazione dell'accesso disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 70cccd5ac7fbea36f02150e3da319a043c22c677
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---
# <a name="api-management-access-restriction-policies"></a>Criteri di limitazione dell'accesso di Gestione API
Questo argomento fornisce un riferimento per i criteri di Gestione API seguenti. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Criteri di limitazione dell'accesso  
  
-   [check-header](api-management-access-restriction-policies.md#CheckHTTPHeader) : impone l'esistenza e/o il valore di un'intestazione HTTP.  
  
-   [Limita frequenza delle chiamate per sottoscrizione](api-management-access-restriction-policies.md#LimitCallRate) : impedisce picchi di utilizzo delle API limitando la frequenza delle chiamate per ogni sottoscrizione.  
  
-   [Limita frequenza delle chiamate per chiave](#LimitCallRateByKey) : impedisce picchi di utilizzo delle API limitando la frequenza delle chiamata, per ogni chiave.  
  
-   [ip-filter](api-management-access-restriction-policies.md#RestrictCallerIPs) : filtra (permette/rifiuta) le chiamate provenienti da indirizzi IP e/o intervalli di indirizzi IP specifici.  
  
-   [Imposta quota di utilizzo per sottoscrizione](api-management-access-restriction-policies.md#SetUsageQuota) : consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per sottoscrizione.  
  
-   [Imposta quota di utilizzo per chiave](#SetUsageQuotaByKey) : consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per chiave.  
  
-   [validate-JWT](api-management-access-restriction-policies.md#ValidateJWT) : impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.  
  
##  <a name="CheckHTTPHeader"></a> Intestazione check-header  
 Usare il criterio `check-header` per applicare una richiesta che presenta un'intestazione HTTP specificata. Facoltativamente, è possibile verificare se l'intestazione presenta un valore specifico o un intervallo di valori consentiti. Se la verifica ha esito negativo, il criterio termina l'elaborazione della richiesta e restituisce il codice di stato HTTP e il messaggio di errore specificati dai criteri.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|check-header|Elemento radice.|Sì|  
|value|Valore dell'intestazione HTTP consentito. Quando vengono specificati più elementi per il valore, in caso di corrispondenza di uno dei valori il controllo ha esito positivo.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|Messaggio di errore da restituire nel corpo della risposta HTTP se l'intestazione non esiste o presenta un valore non valido. I caratteri speciali eventualmente contenuti in questo messaggio devono essere adeguatamente preceduti da un carattere di escape.|Sì|N/D|  
|failed-check-httpcode|Codice di stato HTTP da restituire se l'intestazione non esiste o presenta un valore non valido.|Sì|N/D|  
|header-name|Il nome dell'intestazione HTTP da verificare.|Sì|N/D|  
|ignore-case|Può essere impostato su True o False. Se impostato su True, il maiuscolo viene ignorato quando il valore dell'intestazione viene confrontato con il set di valori accettabili.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="LimitCallRate"></a> Limita frequenza delle chiamate per sottoscrizione  
 Il criterio `rate-limit` impedisce picchi d'uso dell'API per ogni sottoscrizione impostando la frequenza delle chiamate su un numero specificato per un periodo di tempo specificato. All'attivazione di questo criterio, il chiamate riceve il codice di stato della risposta `429 Too Many Requests`.  
  
> [!IMPORTANT]
>  Questo criterio può essere impiegato una sola volta per ogni documento dei criteri.  
>   
>  Per questo criterio, non è possibile usare [espressioni di criteri](api-management-policy-expressions.md) negli attributi dei criteri.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="name" calls="number" renewal-period="seconds">  
        <operation name="name" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|set-limit|Elemento radice.|Sì|  
|api|Aggiungere almeno uno di questi elementi per imporre un limite di frequenza delle chiamate alle API all'interno del prodotto. I limiti alla frequenza delle chiamate API e al prodotto vengono applicati in modo indipendente.|No|  
|operation|Aggiungere almeno uno di questi elementi per imporre un limite di frequenza delle chiamate alle operazioni all'interno di un'API. I limiti alla frequenza delle chiamate alle operazioni, all'API e al prodotto vengono applicati in modo indipendente.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|name|Il nome dell'API a cui si desidera applicare il limite di frequenza.|Sì|N/D|  
|calls|Il numero totale massimo di chiamate consentite durante l'intervallo di tempo specificato in `renewal-period`.|Sì|N/D|  
|renewal-period|Periodo, in secondi, dopo il quale la quota si reimposta.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** prodotto  
  
##  <a name="LimitCallRateByKey"></a> Limita la frequenza delle chiamate per chiave  
 Il criterio `rate-limit-by-key` impedisce picchi d'uso dell'API per ogni chiave impostando la frequenza delle chiamate su un numero specificato per un periodo di tempo specificato. La chiave può avere un valore di stringa arbitrario e viene indicata in genere usando un'espressione di criteri. Per specificare le richieste da considerare nel limite, è possibile aggiungere una condizione opzionale di incremento. All'attivazione di questo criterio, il chiamate riceve il codice di stato della risposta `429 Too Many Requests`.  
  
 Per altre informazioni ed esempi su questo criterio, vedere [Advanced request throttling with Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/) (Limitazione avanzata delle richieste con Gestione API di Azure).  
  
> [!IMPORTANT]
>  Questo criterio può essere impiegato una sola volta per ogni documento dei criteri.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Esempio  
 Nell'esempio seguente il limite alla frequenza viene associato a una chiave dall'indirizzo IP del chiamante.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|set-limit|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|calls|Il numero totale massimo di chiamate consentite durante l'intervallo di tempo specificato in `renewal-period`.|Sì|N/D|  
|counter-key|La chiave deve essere usata per i criteri relativi ai limiti di frequenza.|Sì|N/D|  
|increment-condition|Espressione booleana che specifica se la richiesta deve essere conteggiata ai fini della quota (`true`).|No|N/D|  
|renewal-period|Periodo, in secondi, dopo il quale la quota si reimposta.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="RestrictCallerIPs"></a> ip-filter  
 Il criterio `ip-filter` filtra (permette/rifiuta) le chiamate da indirizzi IP e/o intervalli di indirizzi IP specifici.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|ip-filter|Elemento radice.|Sì|  
|Address|Specifica un singolo indirizzo IP su cui applicare il filtro.|È obbligatorio almeno un elemento `address` o `address-range`.|  
|address-range from="address" to="address"|Specifica un intervallo di indirizzi IP su cui applicare il filtro.|È obbligatorio almeno un elemento `address` o `address-range`.|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|address-range from="address" to="address"|Intervallo di indirizzi IP per cui permettere o negare l'accesso.|Obbligatorio quando viene usato l'elemento `address-range`.|N/D|  
|ip-filter action="allow &#124; forbid"|Specifica se le chiamate devono essere consentite o rifiutate per gli indirizzi e gli intervalli di indirizzi IP specificati.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="SetUsageQuota"></a> Imposta quota di utilizzo per sottoscrizione  
 Il criterio `quota` consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per sottoscrizione.  
  
> [!IMPORTANT]
>  Questo criterio può essere impiegato una sola volta per ogni documento dei criteri.  
>   
>  Per questo criterio, non è possibile usare [espressioni di criteri](api-management-policy-expressions.md) negli attributi dei criteri.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="name" calls="number" bandwidth="kilobytes">  
        <operation name="name" calls="number" bandwidth="kilobytes" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|quota|Elemento radice.|Sì|  
|api|Aggiungere almeno uno di questi elementi per imporre una quota alle API all'interno del prodotto. Le quote delle API e del prodotto vengono applicate in modo indipendente.|No|  
|operation|Aggiungere almeno uno di questi elementi per imporre una quota alle operazioni all'interno di un'API. Le quote delle operazioni, delle API e del prodotto vengono applicate in modo indipendente.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|name|Nome dell'API o dell'operazione per cui è applicabile la quota.|Sì|N/D|  
|bandwidth|Il numero totale massimo di kilobyte consentiti durante l'intervallo di tempo specificato in `renewal-period`.|Devono essere specificati `calls`, `bandwidth` o entrambi.|N/D|  
|calls|Il numero totale massimo di chiamate consentite durante l'intervallo di tempo specificato in `renewal-period`.|Devono essere specificati `calls`, `bandwidth` o entrambi.|N/D|  
|renewal-period|Periodo, in secondi, dopo il quale la quota si reimposta.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** prodotto  
  
##  <a name="SetUsageQuotaByKey"></a> Impostare la quota per chiave  
 Il criterio `quota-by-key` consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per chiave. La chiave può avere un valore di stringa arbitrario e viene indicata in genere usando un'espressione di criteri. Per specificare le richieste da considerare nella quota, è possibile aggiungere una condizione opzionale di incremento.  
  
 Per altre informazioni ed esempi su questo criterio, vedere [Advanced request throttling with Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/) (Limitazione avanzata delle richieste con Gestione API di Azure).  
  
> [!IMPORTANT]
>  Questo criterio può essere impiegato una sola volta per ogni documento dei criteri.  
>   
>  Per questo criterio, non è possibile usare [espressioni di criteri](api-management-policy-expressions.md) negli attributi dei criteri.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Esempio  
 Nell'esempio seguente la quota viene associata a una chiave dall'indirizzo IP del chiamante.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|quota|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|bandwidth|Il numero totale massimo di kilobyte consentiti durante l'intervallo di tempo specificato in `renewal-period`.|Devono essere specificati `calls`, `bandwidth` o entrambi.|N/D|  
|calls|Il numero totale massimo di chiamate consentite durante l'intervallo di tempo specificato in `renewal-period`.|Devono essere specificati `calls`, `bandwidth` o entrambi.|N/D|  
|counter-key|La chiave deve essere usata per i criteri relativi alla quota.|Sì|N/D|  
|increment-condition|Espressione booleana che specifica se la richiesta deve essere conteggiata ai fini della quota (`true`).|No|N/D|  
|renewal-period|Periodo, in secondi, dopo il quale la quota si reimposta.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
##  <a name="ValidateJWT"></a> Convalida token JWT  
 Il criterio `validate-jwt` impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.  
  
> [!IMPORTANT]
>  Il criterio `validate-jwt` richiede che l'attestazione `exp` registrata venga inclusa nel token JWT, a meno che non venga specificato l'attributo `require-expiration-time` e impostato su `false`.  
> Il criterio `validate-jwt` supporta gli algoritmi di firma HS256 e RS256. Per HS256, la chiave deve essere fornita incorporata all'interno del criterio nel formato con codificata Base64. Per RS256, la chiave deve essere fornita attraverso un endpoint di configurazione Open ID.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any">  
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="azure-mobile-services-token-validation"></a>Convalida del token dei Servizi mobili di Azure  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Convalida del token di Azure Active Directory  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Convalida del token di Azure Active Directory B2C  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizzare l'accesso a operazioni basate su attestazioni dei token  
 Questo esempio illustra come usare il criterio [Convalida token JWT](api-management-access-restriction-policies.md#ValidateJWT) per preautorizzare l'accesso alle operazioni in base alle attestazioni dei token. Per una dimostrazione relativa alla configurazione e all'uso di questo criterio, vedere l'[episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 13:50. Passare a 15:00 minuti per vedere i criteri configurati nell'editor dei criteri e quindi a 18:50 minuti per una dimostrazione della chiamata di un'operazione dal portale per sviluppatori, con e senza il token di autorizzazione richiesto.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Elementi  
  
|Elemento|Descrizione|Obbligatorio|  
|-------------|-----------------|--------------|  
|validate-jwt|Elemento radice.|Sì|  
|audiences|Contiene un elenco di attestazioni "audience" accettabili che possono essere presenti nel token. Se sono presenti più valori "audience", viene provato ogni valore fino al completamento di tutti i valori (caso in cui la convalida ha esito negativo) o fino a quando un valore non ha esito positivo. È necessario specificare almeno un "audience".|No|  
|issuer-signing-keys|Elenco di chiavi di sicurezza con codifica Base64 usato per convalidare i token firmati. Se sono presenti più chiavi di sicurezza, viene provata ogni chiave fino al completamento di tutte le chiavi (caso in cui la convalida ha esito negativo) o fino a quando una chiave non ha esito positivo. Gli elementi chiave contengono un attributo `id` facoltativo, usato per il confronto con l'attestazione `kid`.|No|  
|issuers|Elenco di entità accettabili che hanno emesso il token. Se sono presenti più valori emittenti, viene provato ogni valore fino al completamento di tutti i valori (caso in cui la convalida ha esito negativo) o fino a quando un valore non ha esito positivo.|No|  
|openid-config|Elemento usato per specificare un endpoint di configurazione Open ID conforme da cui è possibile ottenere le chiavi di firma e l'emittente.|No|  
|required-claims|Contiene un elenco di attestazioni da includere nel token affinché possa essere considerato valido. Perché la convalida abbia esito positivo, quando l'attributo `match` è impostato su `all` ogni valore dell'attestazione nel criterio deve essere presente nel token. Perché la convalida abbia esito positivo, quando l'attributo `match` è impostato su `any` almeno un'attestazione deve essere presente nel token.|No|  
|zumo-master-key|Chiave master per i token rilasciati da Servizi mobili di Azure|No|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|clock-skew|TimeSpan. Fornisce un minimo margine temporale nel caso in cui nel token sia presente l'attestazione di scadenza del token e abbia superato la data/l'ora corrente.|No|0 secondi|  
|failed-validation-error-message|Messaggio di errore da restituire nel corpo della risposta HTTP se il token JWT non supera la convalida. I caratteri speciali eventualmente contenuti in questo messaggio devono essere adeguatamente preceduti da un carattere di escape.|No|Il messaggio di errore predefinito dipende dal problema della convalida, ad esempio "JWT not present" ("JWT non presente").|  
|failed-validation-httpcode|Codice di stato HTTP da restituire se il token JWT non supera la convalida.|No|401|  
|header-name|Il nome dell'intestazione HTTP che contiene il token.|È necessario specificare `header-name` o `query-paremeter-name`, ma non entrambi.|N/D|  
|id|L'attributo `id` nell'elemento `key` consente di specificare la stringa che verrà confrontata con l'attestazione `kid` nel token (se presente) per individuare la chiave appropriata da usare per la convalida della firma.|No|N/D|  
|match|Perché la convalida abbia esito positivo, l'attributo `match` sull'elemento `claim` specifica se il valore dell'attestazione nel criterio deve essere presente nel token. I valori possibili sono:<br /><br /> -                          `all`: ogni valore dell'attestazione nel criterio deve essere presente nel token perché la convalida abbia esito positivo.<br /><br /> -                          `any`: almeno un valore dell'attestazione deve essere presente nel token perché la convalida abbia esito positivo.|No|tutti|  
|query-paremeter-name|Nome di parametro di query che contiene il token.|È necessario specificare `header-name` o `query-paremeter-name`, ma non entrambi.|N/D|  
|require-expiration-time|Booleano. Specifica se è necessaria un'attestazione di scadenza nel token.|No|true|
|require-scheme|Il nome dello schema di token, ad esempio "Bearer". Quando questo attributo è impostato, il criterio assicura che lo schema specificato sia presente nel valore dell'intestazione di autorizzazione.|No|N/D|
|require-signed-tokens|Booleano. Specifica se è necessario firmare un token.|No|true|  
|URL|URL dell'endpoint di configurazione Open ID dal quale è possibile ottenere i metadati della configurazione Open ID. Per Azure Active Directory, usare il seguente URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` sostituendo il nome del tenant della directory in uso, ad esempio `contoso.onmicrosoft.com`.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound  
  
-   **Ambiti del criterio:** globale, prodotto, API, operazione  
  
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei criteri, vedere [Criteri di Gestione API](api-management-howto-policies.md).  

