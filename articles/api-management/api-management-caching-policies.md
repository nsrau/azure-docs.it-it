---
title: Criteri di memorizzazione nella cache in Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri di memorizzazione nella cache disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 488a4c4b7daf5c07ca5f6b6bb72464279658d372
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="api-management-caching-policies"></a>Criteri di memorizzazione nella cache in Gestione API
Questo argomento fornisce un riferimento per i seguenti criteri di Gestione API. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a> Criteri di memorizzazione nella cache  
  
-   Criteri di memorizzazione nella cache della risposta  
  
    -   [Recupera dalla cache](api-management-caching-policies.md#GetFromCache): esegue una ricerca nella cache e restituisce risposte valide memorizzate nella cache, se disponibili.  
    -   [Archivia nella cache](api-management-caching-policies.md#StoreToCache): memorizza nella cache le risposte in base alla configurazione del controllo cache specificata.  
  
-   Criteri di memorizzazione dei valori nella cache  

    -   [Recupera valore dalla cache](#GetFromCacheByKey) : recupera un elemento memorizzato nella cache per chiave. 
    -   [Archivia valore nella cache](#StoreToCacheByKey) : archivia un elemento nella cache per chiave. 
    -   [Rimuovi valore dalla cache](#RemoveCacheByKey) : rimuove un elemento dalla cache in base alla chiave.  
  
##  <a name="GetFromCache"></a> Recupera dalla cache  
 Usare il criterio `cache-lookup` per eseguire una ricerca nella cache e restituire una risposta valida memorizzata nella cache, se disponibile. Questo criterio può essere applicato nei casi in cui il contenuto della risposta rimane statico in un periodo di tempo. La memorizzazione delle risposte nella cache riduce la larghezza di banda e i requisiti di elaborazione imposti sul server Web back-end e riduce la latenza percepita dagli utenti delle API.  
  
> [!NOTE]
>  Questo criterio deve essere associato a un criterio [Archivia nella cache](api-management-caching-policies.md#StoreToCache) corrispondente.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-private-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Esempio con espressioni di criteri  
 Questo esempio mostra come per configurare la durata della memorizzazione nella cache di Gestione API corrispondente alla memorizzazione delle risposte nella cache del serivzio back-end, come specificato dalla direttiva `Cache-Control` del servizio in questione. Per una dimostrazione relativa alla configurazione e all'uso di questi criteri, vedere l' [episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Per altre informazioni, vedere [Espressioni di criteri](api-management-policy-expressions.md) e [Variabile di contesto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|cache-lookup|Elemento radice.|Sì|  
|vary-by-header|Avviare la memorizzazione delle risposte nella cache per ogni valore dell'intestazione specificata, come ad esempio Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host e If-Match.|No|  
|vary-by-query-parameter|Avvia risposte di memorizzazione nella cache per ogni valore dei parametri di query specificati. Immettere un singolo parametro o più parametri. Usare un punto e virgola (;) come separatore. Se non è specificato alcun nome, verranno usati tutti i parametri di query.|No|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|allow-private-response-caching|Se impostato su `true`, consente la memorizzazione nella cache delle richieste contenenti un'intestazione di autorizzazione.|No|false|  
|downstream-caching-type|Questo attributo deve essere impostato su uno dei valori seguenti.<br /><br /> -   none - Non è consentita la memorizzazione nella cache downstream.<br />-   private - È consentita la memorizzazione nella cache downstream privata.<br />-   public - È consentita la memorizzazione nella cache downstream privata e condivisa.|No|nessuno|  
|must-revalidate|Quando è abilitata la memorizzazione nella cache downstream, questo attributo attiva o disattiva la direttiva di controllo di memorizzazione della cache `must-revalidate` nelle risposte del gateway.|No|true|  
|vary-by-developer|Impostare su `true` per memorizzare le risposte nella cache per ogni chiave sviluppatore.|Sì||  
|vary-by-developer-groups|Impostare su `true` per memorizzare le risposte nella cache per ogni ruolo utente.|Sì||  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** in ingresso  
-   **Ambiti del criterio:** API, operazione, prodotto  
  
##  <a name="StoreToCache"></a> Archivia nella cache  
 Il criterio `cache-store` memorizza nella cache le risposte in base alle impostazioni specificate per la cache. Questo criterio può essere applicato nei casi in cui il contenuto della risposta rimane statico in un periodo di tempo. La memorizzazione delle risposte nella cache riduce la larghezza di banda e i requisiti di elaborazione imposti sul server Web back-end e riduce la latenza percepita dagli utenti delle API.  
  
> [!NOTE]
>  Questo criterio deve essere associato a un criterio [Recupera dalla cache](api-management-caching-policies.md#GetFromCache) corrispondente.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Esempi  
  
#### <a name="example"></a>Esempio  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Esempio con espressioni di criteri  
 Questo esempio mostra come per configurare la durata della memorizzazione nella cache di Gestione API corrispondente alla memorizzazione delle risposte nella cache del serivzio back-end, come specificato dalla direttiva `Cache-Control` del servizio in questione. Per una dimostrazione relativa alla configurazione e all'uso di questi criteri, vedere l' [episodio 177 di Cloud Cover su altre funzionalità di Gestione API con Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e passare direttamente al minuto 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Per altre informazioni, vedere [Espressioni di criteri](api-management-policy-expressions.md) e [Variabile di contesto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|cache-store|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|duration|Durata (TTL, Time-To-Live) delle voci memorizzate nella cache, in secondi.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere utilizzato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) di criteri seguenti.  
  
-   **Sezioni del criterio:** in uscita    
-   **Ambiti del criterio:** API, operazione, prodotto  
  
##  <a name="GetFromCacheByKey"></a> Recupera valore dalla cache  
 Usare il criterio `cache-lookup-value` per eseguire la ricerca nella cache in base alla chiave e restituiscono un valore memorizzato nella cache. La chiave può avere un valore di stringa arbitrario e viene indicata in genere usando un'espressione di criteri.  
  
> [!NOTE]
>  Questo criterio deve essere associato a un criterio [Archivia valore nella cache](#StoreToCacheByKey) corrispondente.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Esempio  
 Per ulteriori informazioni ed esempi su questo criterio, vedere [Memorizzazione nella cache personalizzata in Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|cache-lookup-value|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|default-value|Un valore che verrà assegnato alla variabile se la ricerca della chiave nella cache non produce risultati. Se questo attributo viene omesso, viene assegnato `null`.|No|`null`|  
|key|Valore della chiave della cache da usare nella ricerca.|Sì|N/D|  
|variable-name|Nome della [variabile di contesto](api-management-policy-expressions.md#ContextVariables) a cui verrà assegnato il valore cercato, se la ricerca ha esito positivo. Se la ricerca non produce risultati, alla variabile sarà assegnato il valore dell'attributo `default-value` o `null`, se l'attributo `default-value` è omesso.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, backend, on-error  
-   **Ambiti del criterio:** global, API, operation, product  
  
##  <a name="StoreToCacheByKey"></a> Archivia valore nella cache  
 `cache-store-value` esegue l'archiviazione nella cache in base alla chiave. La chiave può avere un valore di stringa arbitrario e viene indicata in genere usando un'espressione di criteri.  
  
> [!NOTE]
>  Questo criterio deve essere associato a un criterio [Recupera valore dalla cache](#GetFromCacheByKey) corrispondente.  
  
### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Esempio  
 Per ulteriori informazioni ed esempi su questo criterio, vedere [Memorizzazione nella cache personalizzata in Gestione API di Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|cache-store-value|Elemento radice.|Sì|  
  
### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|duration|Il valore verrà memorizzato nella cache per il valore di durata specificato, espresso in secondi.|Sì|N/D|  
|key|La chiave della cache in cui verrà archiviato il valore.|Sì|N/D|  
|value|Il valore da memorizzare nella cache.|Sì|N/D|  
  
### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, backend, on-error  
-   **Ambiti del criterio:** global, API, operation, product  
  
###  <a name="RemoveCacheByKey"></a> Rimuovi valore dalla cache  
`cache-remove-value` elimina un elemento memorizzato nella cache identificato in base alla chiave. La chiave può avere un valore di stringa arbitrario e viene indicata in genere usando un'espressione di criteri.  
  
#### <a name="policy-statement"></a>Istruzione del criterio  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Esempio  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementi  
  
|Nome|Descrizione|Obbligatorio|  
|----------|-----------------|--------------|  
|cache-remove-value|Elemento radice.|Sì|  
  
#### <a name="attributes"></a>Attributi  
  
|Nome|Descrizione|Obbligatorio|Default|  
|----------|-----------------|--------------|-------------|  
|key|La chiave del valore memorizzato in precedenza nella cache da rimuovere dalla cache.|Sì|N/D|  
  
#### <a name="usage"></a>Utilizzo  
 Questo criterio può essere usato nelle [sezioni](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e negli [ambiti](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) del criterio seguenti.  
  
-   **Sezioni del criterio:** inbound, outbound, backend, on-error  
-   **Ambiti del criterio:** global, API, operation, product  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [API Transform](transform-api.md)
+ [Informazioni di riferimento per i criteri](api-management-policy-reference.md) per un elenco completo di istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)   
