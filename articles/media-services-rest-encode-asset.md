<properties 
	pageTitle="Come codificare un asset mediante Azure Media Encoder" 
	description="Informazioni su come usare Azure Media Encoder per codificare contenuti multimediali in Servizi multimediali. Negli esempi di codice viene usata l'API REST." 
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


# Come codificare un asset mediante Azure Media Encoder

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) . 

## Informazioni generali
Per distribuire un video digitale tramite Internet è necessario comprimere il file multimediale. I file video digitali hanno dimensioni piuttosto elevate e possono risultare troppo grandi per la distribuzione su Internet o per la visualizzazione corretta sui dispositivi dei clienti. Mediante il processo di codifica è possibile comprimere video e audio per consentire ai clienti di visualizzare i file multimediali.

I processi di codifica sono tra le operazioni di elaborazione più frequenti in Servizi multimediali. Questi processi vengono creati per convertire i file multimediali da una codifica all'altra. Durante la codifica è possibile usare il codificatore multimediale incorporato in Servizi multimediali. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali. I codificatori di terze parti sono disponibili tramite Azure Marketplace. È possibile specificare i dettagli relativi alle attività di codifica usando stringhe di set di impostazioni definite per il codificatore oppure file di configurazione di set di impostazioni. Per i tipi di set di impostazioni disponibili, vedere Set di impostazioni di attività per Servizi multimediali di Azure. Se si usa un codificatore di terze parti, è necessario [convalidare i file](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Si consiglia di codificare sempre i file in formato intermedio con un set MP4 a velocità in bit adattiva e quindi convertire il set nel formato desiderato mediante la [creazione dinamica dei pacchetti](https://msdn.microsoft.com/library/azure/jj889436.aspx).

## Creare un processo con una singola attività di codifica 

>[AZURE.NOTE] Quando si usa l'API REST di Servizi multimediali, tenere presenti le seguenti considerazioni:
>
>Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Installazione per lo sviluppo nell'API REST di Servizi multimediali](media-services-rest-how-to-use.md).

>Dopo avere stabilito la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 che indica un altro URI di Servizi multimediali. Le chiamate successive dovranno essere effettuate al nuovo URI, come descritto in [Connessione all'account di Servizi multimediali mediante l'API REST](media-services-rest-connect_programmatically.md). 


Il seguente esempio mostra come creare e pubblicare un processo con un'attività impostata per codificare un video con determinati valori di risoluzione e qualità. Quando si esegue la codifica mediante Azure Media Encoder, è possibile usare i set di impostazioni di attività specificati [qui](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Richiesta:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Risposta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

## Creare un processo con attività concatenate

In molti scenari di applicazione, gli sviluppatori desiderano creare una serie di attività di elaborazione. In Servizi multimediali è possibile creare una serie di attività concatenate. Ogni attività esegue diversi passaggi di elaborazione e può usare processori di contenuti multimediali differenti. Le attività concatenate possono trasferire un asset da un'attività a un'altra e consentono quindi l'esecuzione delle attività dell'asset in sequenza lineare. Tuttavia, non è necessario che le attività eseguite in un processo siano in sequenza. Quando si crea un'attività concatenata, gli oggetti **ITask** concatenati vengono creati in un singolo oggetto **IJob**.

>[AZURE.NOTE] Attualmente è previsto un limite di 30 attività per processo. Se è necessario concatenare più di 30 attività, creare più processi in modo da contenerle tutte.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


## Passaggi successivi
Dopo avere creato un processo per la codifica di un asset, passare all'argomento [Come verificare l'avanzamento di un processo con Servizi multimediali](media-services-rest-check-job-progress.md) .

[Azure Marketplace]: https://datamarket.azure.com/
[Impostazioni per il codificatore]: http://msdn.microsoft.com/library/dn619392.aspx
[Procedura: Ottenere un'istanza del processore di contenuti multimediali]:http://go.microsoft.com/fwlink/?LinkId=301732
[Procedura: Caricare un asset crittografato]:http://go.microsoft.com/fwlink/?LinkId=301733
[Procedura: Distribuire un asset mediante download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Come controllare lo stato dei processi]:http://go.microsoft.com/fwlink/?LinkId=301737
[Set di impostazioni per Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=47-->
