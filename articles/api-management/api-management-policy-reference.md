---
title: Informazioni di riferimento per i criteri di Gestione API di Azure
description: Informazioni sui criteri disponibili per configurare Gestione API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: apimpm

---
# Informazioni di riferimento per i criteri di Gestione API di Azure
Questa sezione include un indice dei criteri disponibili in [Informazioni di riferimento per i criteri di Gestione API di Azure][Informazioni di riferimento per i criteri di Gestione API di Azure]. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API][Criteri di Gestione API].

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose][choose] e [set-variable][set-variable], sono basati su espressioni di criteri. Per altre informazioni, vedere [Criteri avanzati][Criteri avanzati] ed [Espressioni di criteri][Espressioni di criteri]

## Indice delle informazioni di riferimento per i criteri
* [Criteri di limitazione dell'accesso][Criteri di limitazione dell'accesso]
  * [check-header][check-header]\: impone l'esistenza e/o il valore di un'intestazione HTTP.
  * [Limita frequenza delle chiamate per sottoscrizione][Limita frequenza delle chiamate per sottoscrizione]\: impedisce picchi di utilizzo delle API limitando la frequenza delle chiamate per ogni sottoscrizione.
  * [Limita frequenza delle chiamate per chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey): impedisce picchi di utilizzo delle API limitando la frequenza delle chiamata, per ogni chiave.
  * [ip-filter][ip-filter]\: filtra (permette/rifiuta) le chiamate provenienti da indirizzi IP e/o intervalli di indirizzi IP specifici.
  * [Imposta quota di utilizzo per sottoscrizione][Imposta quota di utilizzo per sottoscrizione]\: consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per sottoscrizione.
  * [Imposta quota di utilizzo per chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey): consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per chiave.
  * [validate-JWT][validate-JWT]\: impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.
* [Criteri avanzati][Criteri avanzati]
  * [choose][choose]\: applica in modo condizionale istruzioni dei criteri sulla base dei risultati della valutazione di [espressioni][espressioni] booleane.
  * [Inoltra richiesta][Inoltra richiesta]\: inoltra la richiesta al servizio back-end.
  * [Registra a Hub eventi][Registra a Hub eventi]\: invia messaggi nel formato specificato a una destinazione del messaggio definita da un’entità [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
  * [Riprova](https://msdn.microsoft.com/it-IT/library/dn894085.aspx#Retry): riprova l'esecuzione delle istruzioni dei criteri, se e fino a quando non viene soddisfatta la condizione. L'esecuzione verrà ripetuta a specifici intervalli di tempo e per il numero di tentativi indicato.
  * [Restituisci risposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse): l’esecuzione nella pipeline viene interrotta e viene restituita la risposta specificata direttamente al chiamante.
  * [Invia richiesta unidirezionale](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest): invia una richiesta all'URL specificato senza attendere una risposta.
  * [Invia richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest): invia una richiesta all'URL specificato.
  * [Imposta metodo di richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod): consente di modificare il metodo HTTP per una richiesta.
  * [Imposta stato](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus): modifica il codice di stato HTTP per il valore specificato.
  * [set-variable][set-variable]\: rende persistente un valore in una variabile [context][context] denominata e consente di accedervi successivamente.
  * [Traccia](https://msdn.microsoft.com/it-IT/library/dn894085.aspx#Trace): aggiunge una stringa nell'output di [Controllo API](api-management-howto-api-inspector.md).
  * [Attendi](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait): attende il completamento dei criteri inclusi per l'invio della richiesta, il recupero del valore dalla cache o il flusso di controllo prima di procedere.
* [Criteri di autenticazione][Criteri di autenticazione]
  * [authentication-basic][authentication-basic]\: consente di eseguire l'autenticazione con un servizio back-end tramite l'autenticazione di base.
  * [authentication-certificate][authentication-certificate]\: consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.
* [Criteri di memorizzazione nella cache][Criteri di memorizzazione nella cache]
  * [Recupera dalla cache][Recupera dalla cache]\: esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.
  * [cache-lookup][cache-lookup]\: memorizza nella cache la risposta in base alla configurazione del controllo cache specificata.
  * [Recupera valore dalla cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey): recupera un elemento memorizzato nella cache per chiave.
  * [Archivia valore nella cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey): archivia un elemento nella cache per chiave.
  * [ valore dalla cache](https://msdn.microsoft.com/it-IT/library/dn894086.aspx#RemoveCacheByKey): rimuove un elemento dalla cache in base alla chiave.
* [Criteri tra domini][Criteri tra domini]
  * [Permetti chiamate tra i domini][Permetti chiamate tra i domini]\: rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.
  * [CORS][CORS]\: aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.
  * [JSONP][JSONP]\: aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.
* [Criteri di trasformazione][Criteri di trasformazione]
  * [json-to-xml][json-to-xml]\: converte il corpo della richiesta o della risposta da JSON a XML.
  * [xml-to-json][xml-to-json]\: converte il corpo della richiesta o della risposta da XML a JSON.
  * [find-and-replace][find-and-replace]\: trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.
  * [Maschera URL nel contenuto][Maschera URL nel contenuto]\: riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento al collegamento equivalente tramite il gateway.
  * [set-backend-service][set-backend-service]\: consente di cambiare il servizio back-end per una richiesta in ingresso.
  * [set-body][set-body]\: consente di impostare il corpo del messaggio per richieste in ingresso e in uscita.
  * [Imposta intestazione HTTP][Imposta intestazione HTTP]\: assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.
  * [Imposta parametro di stringa della query][Imposta parametro di stringa della query]\: aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.
  * [rewrite-uri][rewrite-uri]\: converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.

## Passaggi successivi
Per altre informazioni sulle espressioni di criteri, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Criteri di limitazione dell'accesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[check-header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limita frequenza delle chiamate per sottoscrizione]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[ip-filter]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Imposta quota di utilizzo per sottoscrizione]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[validate-JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Criteri avanzati]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[choose]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[set-variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[espressioni]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Inoltra richiesta]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registra a Hub eventi]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Criteri di autenticazione]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[authentication-basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[authentication-certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Criteri di memorizzazione nella cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Recupera dalla cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[cache-lookup]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Criteri tra domini]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permetti chiamate tra i domini]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Criteri di trasformazione]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[json-to-xml]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[xml-to-json]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[find-and-replace]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Maschera URL nel contenuto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[set-backend-service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[set-body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Imposta intestazione HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Imposta parametro di stringa della query]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[rewrite-uri]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Criteri di Gestione API]: api-management-howto-policies.md
[Informazioni di riferimento per i criteri di Gestione API di Azure]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Espressioni di criteri]: https://msdn.microsoft.com/library/azure/dn910913.aspx



<!---HONumber=AcomDC_0831_2016-->