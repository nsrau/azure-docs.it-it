<properties 
	pageTitle="Connessione a un account di Servizi multimediali mediante l'API REST" 
	description="Questo argomento illustra come connettersi a Servizi multimediali mediante l'API REST." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="04/18/2016"   
	ms.author="juliako"/>


# Connessione a un account di Servizi multimediali mediante l'API REST di Servizi multimediali

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [REST](media-services-rest-connect-programmatically.md)

Questo argomento descrive come ottenere una connessione a Servizi multimediali di Microsoft Azure a livello di codice quando si programma con l'API REST di Servizi multimediali.

Quando si accede a Servizi multimediali di Microsoft Azure sono necessari due elementi: un token di accesso fornito da Servizi di controllo di accesso di Azure e l'URI di Servizi multimediali. Per creare queste richieste è possibile procedere come si preferisce, ma è necessario specificare i valori di intestazione corretti e passare correttamente il token di accesso quando si esegue una chiamata in Servizi multimediali.

I seguenti passaggi descrivono i flussi di lavoro comuni relativi all'uso dell'API REST per connettersi a Servizi multimediali:

1. Recupero di un token di accesso 
2. Connessione all'URI di Servizi multimediali 

	>[AZURE.NOTE] Dopo aver stabilito la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 che indica un altro URI di Servizi multimediali. Le chiamate successive dovranno essere effettuate al nuovo URI. È anche possibile ricevere una risposta HTTP/1.1 200 contenente la descrizione dei metadati dell'API ODATA.

3. Inviare le successive chiamate API al nuovo URL.

	Se, ad esempio, dopo aver tentato la connessione si ottiene la seguente risposta:

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Si consiglia di inviare le successive chiamate API a https://wamsbayclus001rest-hs.cloudapp.net/api/.

##Indirizzo del controllo di accesso

L'indirizzo del controllo di accesso di Servizi multimediali è https://wamsprodglobal001acs.accesscontrol.windows.net, eccetto per la Cina settentrionale, dove è invece https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##Recupero di un token di accesso

Per accedere a Servizi multimediali direttamente dall'API REST, recuperare un token di accesso da Servizi di controllo di accesso e usarlo per ogni richiesta HTTP effettuata nel servizio. Questo token è simile ad altri token forniti da Servizi di controllo di accesso in base alle attestazioni di accesso riportate nell'intestazione di una richiesta HTTP che usano il protocollo OAuth versione 2. Non sono previsti altri prerequisiti per connettersi direttamente a Servizi multimediali.

Il seguente esempio illustra l'intestazione e il corpo della richiesta HTTP usati per recuperare un token.

**Intestazione**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**Corpo**:

È necessario verificare i valori client\_id e client\_secret nel corpo di questa richiesta. client\_id e client\_secret corrispondono rispettivamente ai valori AccountName e AccountKey. Questi valori vengono forniti da Servizi multimediali quando si configura l'account.

Si noti che il valore AccountKey per l'account di Servizi multimediali deve essere codificato nell'URL (vedere [Percent-Encoding](http://tools.ietf.org/html/rfc3986#section-2.1) quando viene usato come valore client\_secret nella richiesta del token di accesso.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Ad esempio:

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


Il seguente esempio illustra la risposta HTTP contenente il token di accesso nel corpo della risposta.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]
È consigliabile memorizzare nella cache i valori "access\_token" e "expires\_in" usando una risorsa di archiviazione esterna. I dati del token potranno quindi essere recuperati da tale risorsa e riusati nelle chiamate all'API REST di Servizi multimediali. Ciò è particolarmente utile in scenari in cui il token può essere condiviso in modo sicuro tra più processi o computer.

Assicurarsi di monitorare il valore "expires\_in" del token di accesso e di aggiornare le chiamate all'API REST con i nuovi token a seconda delle esigenze.

###Connessione all'URI di Servizi multimediali

L'URI radice per Servizi multimediali è https://media.windows.net/. Connettersi inizialmente a questo URI. Se si ottiene un reindirizzamento 301 come risposta, effettuare le chiamate successive al nuovo URI. Inoltre, evitare di usare la logica di reindirizzamento automatico/collegamento nelle richieste. I corpi delle richieste e i verbi HTTP non verranno inoltrati al nuovo URI.

Tenere presente che l'URI radice per il caricamento e il download di file di Asset è https://yourstorageaccount.blob.core.windows.net/, dove il nome dell'account di archiviazione corrisponde a quello usato durante la configurazione dell'account di Servizi multimediali.

Il seguente esempio illustra la richiesta HTTP all'URI radice di Servizi multimediali (https://media.windows.net/). La richiesta ottiene un reindirizzamento 301 come risposta. La richiesta successiva usa il nuovo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).

**Richiesta HTTP**:
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: media.windows.net


**Risposta HTTP**:
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**Richiesta HTTP** (con il nuovo URI):
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**Risposta HTTP**:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE] Il nuovo URI ottenuto è quello da usare per comunicare con Servizi multimediali.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0622_2016-->