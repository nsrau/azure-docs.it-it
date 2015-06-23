<properties 
	pageTitle="Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure" 
	description="Questo argomento descrive alcuni scenari comuni relativi alla distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure

## Informazioni generali

Questo argomento descrive i passaggi di un tipico flusso di lavoro VOD (Video On Demand) di Servizi multimediali di Azure. Ogni passaggio è collegato a uno specifico argomento. Per le attività che possono essere svolte mediante diverse tecnologie, sono disponibili pulsanti di collegamento alla tecnologia scelta, ad esempio .NET o REST.   

Si noti che è possibile integrare Servizi multimediali con gli strumenti e i processi esistenti. Ad esempio, codificare i contenuti in sede e quindi caricarli in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. 

Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali interessate dal Flusso di lavoro Video on Demand.
![Flusso di lavoro VoD][vod-overview]

## <a id="vod_scenarios"></a>Scenari comuni: distribuzione di contenuti multimediali su richiesta. 

### Protezione dei contenuti nella risorsa di archiviazione e distribuzione dei flussi multimediali in chiaro (non crittografati)

1. Caricare un file in formato intermedio di qualità elevata in un asset.
	
	Si consiglia di applicare all'asset l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti sia durante il caricamento sia mentre si trovano nella risorsa di archiviazione. 
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. 

	Si consiglia di applicare all'asset di output l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti anche quando non vengono usati.
	
1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti). 
	
	Se l'asset è protetto con crittografia di archiviazione, è **necessario** configurare i criteri di distribuzione degli asset. 

1. Pubblicare l'asset creando un localizzatore OnDemand.

	Accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere i contenuti in streaming.

1. Trasmettere in streaming i contenuti pubblicati.

### Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati  

Per poter usare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere in streaming i contenuti crittografati.

1. Caricare un file in formato intermedio di qualità elevata in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
1. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
2. Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti e la crittografia dinamica).
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati. 

### Indicizzazione dei contenuti

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Indicizzare i contenuti.

	Il processo di indicizzazione genera dei file che possono essere usati come sottotitoli nella riproduzione del video. Genera anche dei file che consentono di eseguire ricerche all'interno dei video e passare all'esatta posizione di un video.	

1. Usare i contenuti indicizzati.


### Definizione del download progressivo 

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva on un singolo MP4.
1. Pubblicare l'asset creando un localizzatore OnDemand o SAS.

	Se si usa un localizzatore OnDemand, accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si pianifica di trasmettere i contenuti in streaming.

	Se si usa un localizzatore SAS, i contenuti vengono scaricati dall'archiviazione BLOB di Azure. In questo caso, non è necessario disporre di unità riservate di streaming.
  
1. Eseguire il download progressivo.

Questo articolo contiene collegamenti ad argomenti che illustrano come configurare un ambiente di sviluppo ed eseguire le attività sopra descritte.


## Concetti

Per concetti relativi alla distribuzione di contenuti su richiesta, vedere [Concetti su Servizi multimediali di Azure](media-services-concepts.md).

## Creazione di un account di Servizi multimediali

Usare il **portale di gestione di Azure** per [creare un account di Servizi multimediali di Azure](media-services-create-account.md). 

## Configurazione dell'ambiente di sviluppo  

Scegliere **.NET** o **API REST** per l'ambiente di sviluppo.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

## Connessione a livello di codice  

Scegliere **.NET** o **API REST** per connettersi a livello di codice a Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


## Configurazione degli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e sulle modalità per gestirli, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md).

## Caricamento di file multimediali 

Caricare i file usando il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

## Creazione di processi \ attività

Un processo è un'entità contenente i metadati relativi a un set di attività, ad esempio la codifica o l'indicizzazione. Ogni attività esegue in modo atomico un'operazione sugli asset di input. Per un esempio su come creare processi di codifica, vedere:

Per informazioni generali, vedere l'articolo relativo all'[utilizzo dei processi di Servizi multimediali di Azure](media-services-jobs.md).

Ottenere un processore di contenuti multimediali appropriato per l'attività con **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

Il seguente esempio crea processi di codifica con il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

## Indicizzazione

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

## Codifica 

**Informazioni generali**: [Codifica di contenuti su richiesta con Servizi multimediali di Azure](media-services-encode-asset.md).

Codifica avanzata con il **flusso di lavoro Premium del codificatore multimediale** tramite **.NET**. 

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

Codificare con **Azure Media Encoder** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

## Monitoraggio dello stato di avanzamento dei processi

Monitorare lo stato di avanzamento dei processi tramite il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

## Creazione di una chiave simmetrica

Creare una chiave simmetrica da usare per crittografare l'asset mediante **.NET** o l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

## Configurazione dei criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## Configurazione dei criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## Pubblicazione e distribuzione degli asset

**Informazioni generali**: [Informazioni generali sulla distribuzione di contenuti](media-services-deliver-content-overview.md)


Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

## Abilitazione della rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md#enable_cdn).

## Ridimensionamento di un account di Servizi multimediali

È possibile ridimensionare **Servizi multimediali** specificando il numero di **unità riservate di streaming** e **unità riservate di codifica** di cui si vuole effettuare il provisioning nell'account in uso. 

È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per espandere lo spazio di archiviazione oltre i limiti predefiniti, è possibile scegliere di collegare più account di archiviazione a un singolo account di Servizi Multimediali.

[Questo](media-services-how-to-scale.md) argomento include collegamenti agli argomenti rilevanti.

## Riproduzione dei contenuti con lettori esistenti

Per altre informazioni, vedere l'articolo relativo alla [riproduzione di contenuti con i lettori esistenti](media-services-playback-content-with-existing-players.md).


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png


<!--HONumber=52--> 