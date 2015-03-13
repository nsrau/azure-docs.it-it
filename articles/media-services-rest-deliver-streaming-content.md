<properties 
	pageTitle="Come distribuire contenuti in streaming da Servizi multimediali" 
	description="Informazioni su come creare un localizzatore da usare per un URL di streaming. Nel codice viene usata l'API REST." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="juliako"/>


# Procedura: Distribuire contenuti in streaming

Questo articolo fa parte delle serie [Flusso di lavoro Video on Demand di Servizi multimediali](../media-services-video-on-demand-workflow) e [Flusso di lavoro Live Streaming di Servizi multimediali](../media-services-live-streaming-workflow).  

## Informazioni generali


È possibile trasmettere in streaming un set MP4 a velocità in bit adattiva creando un localizzatore di streaming OnDemand e un URL di streaming. L'argomento relativo alla [codifica di un asset](../media-services-rest-encode-asset) mostra come codificare un asset in un set MP4 a velocità in bit adattiva. Prima di creare un localizzatore, è consigliabile configurare i criteri di distribuzione degli asset, come descritto in [questo](../media-services-rest-configure-asset-delivery-policy) argomento. 

È inoltre possibile usare un localizzatore di streaming OnDemand per creare URL che puntano a file MP4 scaricabili in modo progressivo.  

Questo argomento illustra come creare un localizzatore di streaming OnDemand, per pubblicare l'asset e creare URL di streaming Smooth, MPEG DASH e HLS, e come creare URL di download progressivo.

La [seguente](#types) sezione mostra i tipi enum i cui valori vengono usati nelle chiamate REST.   
  
## Creare un localizzatore di streaming OnDemand

Per creare un localizzatore di streaming OnDemand e ottenere gli URL, è necessario effettuare le seguenti operazioni:


   1. Definire i criteri di accesso.
   2. Creare un localizzatore di streaming OnDemand.
   3. Se si pianifica lo streaming, ottenere il file manifesto di streaming (.ism) nell'asset. 
   		
	Se si pianifica il download progressivo, ottenere i nomi dei file MP4 nell'asset. 
   4. Creare URL che puntano al file manifesto o ai file MP4. 


### Creare i criteri di accesso

Richiesta:
		
	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	Host: media.windows.net
	Content-Length: 68
	
	{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
	
Risposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 311
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
	Server: Microsoft-IIS/8.5
	request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:52:09 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### Creare un localizzatore di streaming OnDemand

Creare il localizzatore per l'asset specificato e i relativi criteri.

Richiesta:
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.8
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	Host: media.windows.net
	Content-Length: 181
	
	{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Risposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 637
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
	Server: Microsoft-IIS/8.5
	request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:58:37 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### Creare URL di streaming

Usare il valore **Path** restituito dopo la creazione del localizzatore per creare gli URL Smooth, HLS e MPEG DASH. 

Smooth Streaming: **Path** + nome file manifesto + "/manifest"

esempio:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Path** + nome file manifesto + "/manifest(format=m3u8-aapl)"

esempio:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Path** + nome file manifesto + "/manifest(format=mpd-time-csf)"


esempio:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### Creare URL di download progressivo

Usare il valore **Path** restituito dopo la creazione del localizzatore per creare l'URL di download progressivo.   

URL: **Path** + nome file mp4 asset

esempio:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Tipi enum

    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

<!--HONumber=45--> 
