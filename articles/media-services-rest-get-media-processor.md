<properties 
	pageTitle="Come creare un processore di contenuti multimediali - Azure" 
	description="Informazioni su come creare un componente del processore di contenuti multimediali per codificare, decodificare, convertire il formato, crittografare o decrittografare contenuti multimediali per Servizi multimediali di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Procedura: Ottenere un'istanza del processore di contenuti multimediali

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](../media-services-video-on-demand-workflow). 

## Informazioni generali

In Servizi multimediali un processore di contenuti multimediali è un componente che gestisce un'attività di elaborazione specifica, ad esempio la codifica, la conversione del formato, la crittografia o la decrittografia di contenuti multimediali. Un processore di contenuti multimediali viene generalmente creato durante la creazione di un'attività per la codifica, la crittografia o la conversione di formato di contenuto multimediale.

Nella tabella seguente sono riportati il nome e la descrizione di tutti i processori di contenuti multimediali disponibili.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nome del processore di contenuti multimediali</th>
       <th>Descrizione</th>
	<th>Altre informazioni</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Consente di eseguire attività di codifica usando il codificatore multimediale.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Stringhe di set di impostazioni di attività per Azure Media Encoder</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Consente di convertire asset di file multimediali dal formato MP4 al formato Smooth Streaming. Consente inoltre di convertire asset di file multimediali dal formato Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Consente di crittografare asset di file multimediali usando PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Stringhe di set di impostazioni di attività per Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>Consente di rendere disponibili per la ricerca file e contenuti multimediali, oltre a generare tracce e parole chiave per i sottotitoli codificati.</td>
		<td>N/D</td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Consente di decrittografare asset di file multimediali crittografati mediante la crittografia di archiviazione.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

## Ottenere un'istanza di MediaProcessor

>[AZURE.NOTE] Quando si usa l'API REST di Servizi multimediali, tenere presenti le seguenti considerazioni:
>
>Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Installazione per lo sviluppo nell'API REST di Servizi multimediali](../media-services-rest-how-to-use).

>Dopo avere stabilito la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 che indica un altro URI di Servizi multimediali. Le chiamate successive dovranno essere effettuate al nuovo URI, come descritto in [Connessione all'account di Servizi multimediali mediante l'API REST](../media-services-rest-connect_programmatically/). 



La seguente chiamata REST mostra come ottenere un'istanza del processore di contenuti multimediali, in questo caso **Azure Media Encoder**. 

	
Richiesta:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
Risposta:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


## Passaggi successivi
Dopo avere ottenuto un'istanza del processore di contenuti multimediali, passare all'argomento [Come codificare un asset][] che illustra come usare Azure Media Encoder per codificare un asset.

[Come codificare un asset]: ../media-services-rest-encode-asset/
[Stringhe di set di impostazioni di attività per Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procedura: Connettersi a Servizi multimediali a livello di codice]: ../media-services-rest-connect_programmatically/

<!--HONumber=45--> 
