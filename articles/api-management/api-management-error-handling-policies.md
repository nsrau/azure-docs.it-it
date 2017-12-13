---
title: Gestione degli errori nei criteri di Gestione API di Azure | Microsoft Docs
description: Informazioni su come rispondere alle condizioni di errore che possono verificarsi durante l'elaborazione delle richieste in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="error-handling-in-api-management-policies"></a>Gestione degli errori nei criteri di Gestione API
Gestione API di Azure consente agli autori di rispondere alle condizioni di errore che possono verificarsi durante l'elaborazione delle richieste al proxy fornendo un oggetto `ProxyError`. È possibile accedere all'oggetto `ProxyError` tramite la proprietà [context.LastError](api-management-policy-expressions.md#ContextVariables). I criteri possono utilizzarlo nella sezione dei criteri `on-error`. In questo argomento vengono indicati dei riferimenti per le funzionalità di gestione degli errori in Gestione API di Azure.  
  
## <a name="error-handling-in-api-management"></a>Gestione degli errori in Gestione API  
 I criteri in Gestione API di Azure sono divisi nelle sezioni `inbound`, `backend`, `outbound` e `on-error` come mostrato nell'esempio seguente.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 Durante l'elaborazione di una richiesta, i passaggi predefiniti vengono eseguiti insieme a tutti i criteri che rientrano nell'ambito per la richiesta. Se si verifica un errore, l'elaborazione passa immediatamente alla sezione di criteri `on-error`. È possibile usare la sezione di criteri `on-error` in qualsiasi ambito. Gli autori di API possono configurare comportamenti personalizzati come la registrazione degli errori nell'hub eventi o la creazione di una nuova risposta da restituire al chiamante.  
  
> [!NOTE]
>  Per impostazione predefinita, la sezione `on-error` non è presente nei criteri. Per aggiungere la sezione `on-error` a un criterio, selezionare il criterio desiderato nell'editor dei criteri e aggiungerlo. Per ulteriori informazioni sulla configurazione dei criteri, vedere [Criteri di Gestione API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Se la sezione `on-error` non è presente, il chiamante riceverà dei messaggi di risposta 400 o 500 HTTP in caso di una condizione di errore.  
  
### <a name="policies-allowed-in-on-error"></a>Criteri consentiti in on-error  
 È possibile usare i seguenti criteri nella sezione di criteri `on-error`.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 Quando si verifica un errore e il controllo passa alla sezione di criteri `on-error`, l'errore viene conservato nella proprietà [context.LastError](api-management-policy-expressions.md#ContextVariables), accessibile dai criteri nella sezione `on-error` e dotata delle seguenti proprietà.  
  
|Nome|Tipo|Descrizione|Obbligatorio|  
|----------|----------|-----------------|--------------|  
|Sorgente|string|Indica l'elemento in cui si è verificato l'errore. Può trattarsi di un criterio o di un nome di passaggio predefinito nella pipeline.|Sì|  
|Motivo|string|Codice errore leggibile tramite computer, da utilizzare se necessario nella gestione degli errori.|No|  
|Message|string|Descrizione dell'errore leggibile dall'utente.|Sì|  
|Scope|string|Nome dell'ambito in cui si è verificato l'errore. Può essere "global", "product", "api" o "operation"|No|  
|Sezione|string|Nome della sezione in cui si è verificato l'errore. Può essere "inbound", "backend", "outbound" o "on-error".|No|  
|Path|string|Specifica i criteri annidati, ad esempio "choose[3]/when[2]".|No|  
|PolicyId|string|Valore dell'attributo `id`, se specificato dal cliente, nel criterio in cui si è verificato l'errore|No|  
  
> [!NOTE]
>  Tutti i criteri dispongono di un attributo `id` facoltativo che è possibile aggiungere al loro elemento radice. Se questo attributo è presente in un criterio quando si verifica una condizione di errore, il valore dell'attributo può essere recuperato tramite la proprietà `context.LastError.PolicyId`.  
  
## <a name="predefined-errors-for-built-in-steps"></a>Errori predefiniti per i passaggi predefiniti  
 Gli errori seguenti sono predefiniti per le condizioni di errore che possono verificarsi durante la valutazione dei passaggi di elaborazione predefiniti.  
  
|Sorgente|Condizione|Motivo|Message|  
|------------|---------------|------------|-------------|  
|configurazione|L'URI non corrisponde a un'API o a un'operazione|OperationNotFound|Impossibile associare la richiesta in ingresso a un'operazione.|  
|autorizzazione|Chiave di sottoscrizione non fornita|SubscriptionKeyNotFound|Accesso negato, chiave di sottoscrizione mancante. Assicurarsi di includere la chiave di sottoscrizione quando si effettuano richieste a questa API.|  
|autorizzazione|Il valore della chiave di sottoscrizione non è valido|SubscriptionKeyInvalid|Accesso negato, la chiave di sottoscrizione non è valida. Assicurarsi di fornire la chiave valida di una sottoscrizione attiva.|  
  
## <a name="predefined-errors-for-policies"></a>Errori predefiniti per i criteri  
 Gli errori seguenti sono predefiniti per le condizioni di errore che possono verificarsi durante la valutazione dei criteri.  
  
|Sorgente|Condizione|Motivo|Message|  
|------------|---------------|------------|-------------|  
|rate-limit|Limite di velocità superato|RateLimitExceeded|Il limite di velocità è stato superato|  
|quota|La quota è stata superata|QuotaExceeded|La quota del volume di chiamate è esaurita. La quota verrà ripristinata in xx:xx:xx. -oppure- La quota della larghezza di banda è esaurita. La quota verrà ripristinata in xx:xx:xx.|  
|jsonp|Il valore del parametro callback non è valido (contiene caratteri errati)|CallbackParameterInvalid|Il valore del parametro callback {callback-parameter-name} non è un identificatore JavaScrpit valido.|  
|ip-filter|Impossibile analizzare l'IP del chiamante dalla richiesta|FailedToParseCallerIP|Impossibile stabilire l'indirizzo IP per il chiamante. Accesso negato.|  
|ip-filter|L'IP del chiamante non è presente nell'elenco degli IP consentiti|CallerIpNotAllowed|L'indirizzo IP del chiamante {ip-address} non è consentito. Accesso negato.|  
|ip-filter|L'IP del chiamante è presente nell'elenco degli IP bloccati|CallerIpBlocked|L'indirizzo IP del chiamante è bloccato. Accesso negato.|  
|check-header|Intestazione richiesta non presentata o valore mancante|HeaderNotFound|Impossibile trovare l'intestazione {header-name} nella richiesta. Accesso negato.|  
|check-header|Intestazione richiesta non presentata o valore mancante|HeaderValueNotAllowed|Il valore {header-value} dell'intestazione {header-name} non è consentito. Accesso negato.|  
|validate-jwt|La richiesta non contiene il token JWT|TokenNotFound|JWT non trovato nella richiesta. Accesso negato.|  
|validate-jwt|Convalida della firma non riuscita|TokenSignatureInvalid|<messaggio dalla libreria JWT\>. Accesso negato.|  
|validate-jwt|Destinatari non validi|TokenAudienceNotAllowed|<messaggio dalla libreria JWT\>. Accesso negato.|  
|validate-jwt|Autorità di certificazione non valida|TokenIssuerNotAllowed|<messaggio dalla libreria JWT\>. Accesso negato.|  
|validate-jwt|Token scaduto|TokenExpired|<messaggio dalla libreria JWT\>. Accesso negato.|  
|validate-jwt|La chiave della firma non è stata risolta dall'ID|TokenSignatureKeyNotFound|<messaggio dalla libreria JWT\>. Accesso negato.|  
|validate-jwt|Attestazioni necessarie non presenti nel token|TokenClaimNotFound|Il token JWT non contiene le attestazioni seguenti: <c1\>, <c2\>, … Accesso negato.|  
|validate-jwt|I valori di attestazione non corrispondono|TokenClaimValueNotAllowed|Il valore {claim-value} dell'attestazione {claim-name} non è consentito. Accesso negato.|  
|validate-jwt|Altri errori di convalida|JwtInvalid|<messaggio dalla libreria JWT\>|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [API Transform](transform-api.md)
+ [Informazioni di riferimento per i criteri](api-management-policy-reference.md) per un elenco completo di istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)   