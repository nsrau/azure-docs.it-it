<properties 
	pageTitle="Riferimento ai criteri di Gestione API di Azure" 
	description="Informazioni sui criteri disponibili per configurare Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Riferimento ai criteri di Gestione API di Azure

Questa sezione include un indice dei criteri disponibili in [Informazioni di riferimento per i criteri di Gestione API di Azure][]. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API][].

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose][] e [set-variable][], sono basati su espressioni di criteri. Per altre informazioni, vedere [Criteri avanzati][] ed [Espressioni di criteri][]

## Indice delle informazioni di riferimento per i criteri

-	[Criteri di limitazione dell'accesso][]
	-	[check-header][]: impone l'esistenza e/o il valore di un'intestazione HTTP.
	-	[rate-limit][]: per impedire picchi di utilizzo delle API, limita le chiamate e/o la percentuale di consumo della larghezza di banda.
	-	[ip-filter][]: filtra (permette/rifiuta) le chiamate provenienti da indirizzi IP e/o intervalli di indirizzi IP specifici.
	-	[quota][]: consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda.
	-	[validate-JWT][]: impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.
-	[Criteri avanzati][]
	-	[choose][]: applica in modo condizionale istruzioni dei criteri sulla base dei risultati della valutazione di [espressioni][] booleane.
	-	[set-variable][]: rende persistente un valore in una variabile [context][] denominata e consente di accedervi successivamente.
-	[Criteri di autenticazione][]
	-	[authentication-basic][]: consente di eseguire l'autenticazione con un servizio back-end tramite l'autenticazione di base.
	-	[authentication-certificate][]: consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.
-	[Criteri di memorizzazione nella cache][] 
	-	[Recupera dalla cache][]: esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.
	-	[cache-lookup][]: memorizza nella cache la risposta in base alla configurazione del controllo cache specificata.
-	[Criteri tra domini][] 
	-	[Permetti chiamate tra i domini][]: rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.
	-	[CORS][]: aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.
	-	[JSONP][]: aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.
-	[Criteri di trasformazione][] 
	-	[json-to-xml][]: converte il corpo della richiesta o della risposta da JSON a XML.
	-	[xml-to-json][]: converte il corpo della richiesta o della risposta da XML a JSON.
	-	[find-and-replace][]: trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.
	-	[redirect-body-urls][]: riscrive (maschera) i collegamenti nel corpo della risposta e nell'intestazione del percorso, in modo che facciano riferimento al collegamento equivalente tramite il proxy.
	-	[set-backend-service][]: consente di cambiare il servizio back-end per una richiesta in ingresso.
	-	[set-body][]: consente di impostare il corpo del messaggio per richieste in ingresso e in uscita.
	-	[Imposta intestazione HTTP][]: assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.
	-	[Imposta parametro di stringa della query][]: aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.
	-	[rewrite-uri][]: converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.

## Passaggi successivi

Per altre informazioni sulle espressioni di criteri, vedere il video seguente.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Criteri di limitazione dell'accesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[check-header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[rate-limit]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[ip-filter]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[quota]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[validate-JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Criteri avanzati]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[choose]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[set-variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[espressioni]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables

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
[redirect-body-urls]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[set-backend-service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[set-body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Imposta intestazione HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Imposta parametro di stringa della query]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[rewrite-uri]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Criteri di Gestione API]: api-management-howto-policies.md
[Informazioni di riferimento per i criteri di Gestione API di Azure]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Espressioni di criteri]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=62-->