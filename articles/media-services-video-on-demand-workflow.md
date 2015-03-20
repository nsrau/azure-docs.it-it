<properties 
	pageTitle="Flusso di lavoro VOD (Video On Demand) di Servizi multimediali" 
	description="Questo argomento descrive i passaggi di un tipico flusso di lavoro VOD (Video On Demand) di Servizi multimediali." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Flusso di lavoro VOD (Video On Demand) di Servizi multimediali

##Panoramica

Questo argomento descrive i passaggi di un tipico flusso di lavoro VOD (Video On Demand) di Servizi multimediali di Azure. Ogni passaggio è collegato a uno specifico argomento. Per le attività che possono essere svolte mediante diverse tecnologie, sono disponibili pulsanti di collegamento alla tecnologia scelta, ad esempio .NET o REST.   

Si noti che è possibile integrare Servizi multimediali con gli strumenti e i processi esistenti. Ad esempio, codificare il contenuto in sede e quindi caricarlo in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. 

Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali interessate dal Flusso di lavoro Video on Demand.
![VoD workflow][vod-overview]

Per altre informazioni, vedere la [panoramica di Servizi multimediali](../media-services-overview).

##Creare un account di Servizi multimediali

Usare il **portale di gestione di Azure** per [creare un account di Servizi multimediali di Azure](../media-services-create-account/). 

##Configurazione degli endpoint di streaming

[lavori in corso]

##Configurare l'ambiente di sviluppo  

Scegliere **.NET** o **API REST** per l'ambiente di sviluppo.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Connettersi a livello di codice  

Scegliere **.NET** o **API REST** per connettersi a livello di codice a Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Caricamento di file multimediali 

Caricare i file usando il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Elaborazione degli elementi multimediali: codifica, indicizzazione e monitoraggio dei processi

###Acquisizione del processore di contenuti multimediali

Ottenere il processore di contenuti multimediali con **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###Creazione di processi 

Un processo è un'entità contenente i metadati relativi a un set di attività, ad esempio la codifica o l'indicizzazione. Ogni attività esegue in modo atomico un'operazione sugli asset di input. Per un esempio su come creare processi di codifica, vedere:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Monitoraggio dello stato di avanzamento dei processi

Monitorare lo stato di avanzamento dei processi tramite il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###Indicizzazione

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Codifica 

Vedere [Codifica con Servizi multimediali di Azure](../media-services-encode-asset).

##Configurare i criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configurare i criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Pubblicare asset

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Scalare un account di Servizi multimediali

È possibile ridimensionare **Servizi multimediali** specificando il numero di **unità riservate di streaming** e **unità riservate di codifica** di cui si vuole effettuare il provisioning nell'account in uso. 

È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per espandere lo spazio di archiviazione oltre i limiti predefiniti, è possibile scegliere di collegare più account di archiviazione a un singolo account di Servizi Multimediali.

[Questo](../media-services-how-to-scale) argomento include collegamenti agli argomenti rilevanti.


##Riprodurre i contenuti

Per altre informazioni, vedere l'articolo relativo alla [riproduzione del contenuto con i lettori esistenti](../media-services-playback-content).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
