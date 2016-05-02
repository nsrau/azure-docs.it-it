<properties 
	pageTitle="Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure" 
	description="Questo argomento descrive alcuni scenari comuni relativi alla distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="04/18/2016" 
	ms.author="juliako"/>


#Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure

##Panoramica

Questo argomento descrive i passaggi di un tipico flusso di lavoro VOD (Video On Demand) di Servizi multimediali di Azure. Ogni passaggio è collegato a uno specifico argomento. Per le attività che possono essere svolte mediante diverse tecnologie, sono disponibili pulsanti di collegamento alla tecnologia scelta, ad esempio .NET o REST.

Si noti che è possibile integrare Servizi multimediali con gli strumenti e i processi esistenti. Ad esempio, codificare i contenuti in sede e quindi caricarli in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti.

Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali interessate dal Flusso di lavoro Video on Demand. ![Flusso di lavoro VoD][vod-overview]

##<a id="vod_scenarios"></a>Scenari comuni: distribuzione di contenuti multimediali su richiesta

###Protezione dei contenuti nella risorsa di archiviazione e distribuzione dei flussi multimediali in chiaro (non crittografati)

1. Caricare un file in formato intermedio di qualità elevata in un asset.
	
	Si consiglia di applicare all'asset l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti sia durante il caricamento sia mentre si trovano nella risorsa di archiviazione. 
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. 

	Si consiglia di applicare all'asset di output l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti anche quando non vengono usati.
	
1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti).
	
	Se l'asset è protetto con crittografia di archiviazione, è **necessario** configurare i criteri di distribuzione degli asset.

1. Pubblicare l'asset creando un localizzatore OnDemand.

	Accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere i contenuti in streaming.

1. Trasmettere in streaming i contenuti pubblicati.

###Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati  

Per poter usare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere in streaming i contenuti crittografati.

1. Caricare un file in formato intermedio di qualità elevata in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
1. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
2. Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti e la crittografia dinamica).
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati. 

###Indicizzazione dei contenuti

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Indicizzare i contenuti.

	Il processo di indicizzazione genera dei file che possono essere usati come sottotitoli nella riproduzione del video. Genera anche dei file che consentono di eseguire ricerche all'interno dei video e passare all'esatta posizione di un video.

1. Usare i contenuti indicizzati.


###Definizione del download progressivo 

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva on un singolo MP4.
1. Pubblicare l'asset creando un localizzatore OnDemand o SAS.

	Se si usa un localizzatore OnDemand, accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si pianifica di trasmettere i contenuti in streaming.

	Se si usa un localizzatore SAS, i contenuti vengono scaricati dall'archiviazione BLOB di Azure. In questo caso, non è necessario disporre di unità riservate di streaming.
  
1. Eseguire il download progressivo.

Questo articolo contiene collegamenti ad argomenti che illustrano come configurare un ambiente di sviluppo ed eseguire le attività sopra descritte.


##Concetti

Per concetti relativi alla distribuzione di contenuti su richiesta, vedere [Concetti su Servizi multimediali](media-services-concepts.md). players.md).

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=AcomDC_0420_2016-->