---
title: Informazioni di riferimento per i criteri di Gestione API di Azure
description: Informazioni sui criteri disponibili per configurare Gestione API.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Informazioni di riferimento per i criteri di Gestione API di Azure
Questa sezione include un indice dei criteri disponibili in [Informazioni di riferimento per i criteri di Gestione API][API Management policy reference]. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API][Policies in API Management].

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri quali [Flusso di controllo][Control flow] e [Imposta variabile][Set variable] sono basati su espressioni di criteri. Per altre informazioni, vedere [Criteri avanzati][Advanced policies] ed [Espressioni di criteri][Policy expressions]

## <a name="policy-reference-index"></a>Indice delle informazioni di riferimento per i criteri
* [Criteri per le restrizioni sull'accesso][Access restriction policies]
  * [Controlla intestazione HTTP][Check HTTP header]: impone l'esistenza e/o il valore di un'intestazione HTTP.
  * [Limita frequenza delle chiamate per sottoscrizione][Limit call rate by subscription]: impedisce picchi di utilizzo delle API limitando la frequenza delle chiamate per ogni sottoscrizione.
  * [Limita frequenza delle chiamate per chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey): impedisce picchi di utilizzo delle API limitando la frequenza delle chiamata, per ogni chiave.
  * [Limita IP chiamanti][Restrict caller IPs]: filtra (permette/rifiuta) le chiamate provenienti da indirizzi IP e/o intervalli di indirizzi IP specifici.
  * [Imposta quota di utilizzo per sottoscrizione][Set usage quota by subscription]: consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per sottoscrizione.
  * [Imposta quota di utilizzo per chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey): consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per chiave.
  * [Convalida JWT][Validate JWT]: impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.
* [Criteri avanzati][Advanced policies]
  * [Flusso di controllo][Control flow]: applica in modo condizionale le istruzioni dei criteri in base ai risultati della valutazione di [espressioni][expressions] booleane.
  * [Inoltra richiesta][Forward request]: inoltra la richiesta al servizio back-end.
  * [Registra in Hub eventi][Log to Event Hub]: invia messaggi nel formato specificato a una destinazione del messaggio definita da un'entità [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Riprova](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry): riprova l'esecuzione delle istruzioni dei criteri, se e fino a quando non viene soddisfatta la condizione. L'esecuzione verrà ripetuta a specifici intervalli di tempo e per il numero di tentativi indicato.
  * [Restituisci risposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) : l’esecuzione nella pipeline viene interrotta e viene restituita la risposta specificata direttamente al chiamante.
  * [Invia richiesta unidirezionale](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) : invia una richiesta all'URL specificato senza attendere una risposta.
  * [Invia richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) : invia una richiesta all'URL specificato.
  * [Imposta metodo di richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) : consente di modificare il metodo HTTP per una richiesta.
  * [Imposta stato](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus): modifica il codice di stato HTTP per il valore specificato.
  * [Imposta variabile][Set variable]: rende persistente un valore in una variabile [context][context] denominata e consente di accedervi in un momento successivo.
  * [Traccia](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace): aggiunge una stringa nell'output di [Controllo API](api-management-howto-api-inspector.md).
  * [Attendi](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait): attende il completamento dei criteri inclusi per l'invio della richiesta, il recupero del valore dalla cache o il flusso di controllo prima di procedere.
* [Criteri di autenticazione][Authentication policies]
  * [Autenticazione di base][Authenticate with Basic]: consente di eseguire l'autenticazione con un servizio di back-end tramite l'autenticazione di base.
  * [Autentica con certificato client][Authenticate with client certificate]: consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.
* [Criteri di memorizzazione nella cache][Caching policies] 
  * [Recupera dalla cache][Get from cache]: esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.
  * [Archivia nella cache][Store to cache]: memorizza nella cache la risposta in base alla configurazione del controllo cache specificata.
  * [Recupera valore dalla cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey): recupera un elemento memorizzato nella cache per chiave.
  * [Archivia valore nella cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) : archivia un elemento nella cache per chiave.
  * [Rimuovi valore dalla cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey): rimuove un elemento dalla cache in base alla chiave.
* [Criteri tra domini][Cross domain policies] 
  * [Permetti chiamate tra domini][Allow cross-domain calls]: rende accessibile l'API da client basati su browser Adobe Flash e Microsoft Silverlight.
  * [CORS][CORS]: aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.
  * [JSONP][JSONP]: aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.
* [Criteri di trasformazione][Transformation policies] 
  * [Converti JSON in XML][Convert JSON to XML]: converte il corpo della richiesta o della risposta da JSON a XML.
  * [Converti XML in JSON][Convert XML to JSON]: converte il corpo della richiesta o della risposta da XML a JSON.
  * [Trova e sostituisci stringa nel corpo][Find and replace string in body]: trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.
  * [Maschera URL nel contenuto][Mask URLs in content]: riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento al collegamento equivalente tramite il gateway.
  * [Imposta servizio back-end][Set backend service]: cambia il servizio back-end per una richiesta in ingresso.
  * [Imposta corpo][Set body]: consente di impostare il corpo del messaggio per le richieste in ingresso e in uscita.
  * [Imposta intestazione HTTP][Set HTTP header]: assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.
  * [Imposta parametro di stringa di query][Set query string parameter]: aggiunge, sostituisce il valore o elimina il parametro di stringa della query di richiesta.
  * [Riscrivi URL][Rewrite URL]: converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle espressioni di criteri, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx


