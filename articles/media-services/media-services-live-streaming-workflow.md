<properties 
	pageTitle="Distribuzione di Live Streaming con Servizi multimediali di Azure" 
	description="Questo argomento descrive i passaggi di un tipico flusso di lavoro Live Streaming di Servizi multimediali." 
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


# Distribuzione di Live Streaming con Servizi multimediali di Azure

## Informazioni generali

Questo argomento descrive i passaggi di un tipico flusso di lavoro Live Streaming di Servizi multimediali di Azure. Ogni passaggio è collegato a uno specifico argomento. Per le attività che possono essere svolte mediante diverse tecnologie, sono disponibili pulsanti di collegamento alla tecnologia scelta, ad esempio .NET o REST.   

Si noti che è possibile integrare Servizi multimediali con gli strumenti e i processi esistenti. Ad esempio, codificare i contenuti in sede e quindi caricarli in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. 

Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali interessate dal flusso di lavoro Live Streaming.

![Flusso di lavoro Live][live-overview]

Questo argomento descrive i concetti inerenti a Live Streaming e fornisce collegamenti ad altri argomenti che illustrano come eseguire attività di Live Streaming.

## Concetti

Per altri concetti relativi a Live Streaming, vedere [Concetti su Servizi multimediali di Azure](media-services-concepts.md).

## Creazione di un account di Servizi multimediali

Usare il **portale di gestione di Azure** per [creare un account di Servizi multimediali di Azure](media-services-create-account.md).

## Configurazione degli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e sulle modalità per gestirli, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md)

## Configurazione dell'ambiente di sviluppo  

Scegliere **.NET** o **API REST** per l'ambiente di sviluppo.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

## Connessione a livello di codice  

Scegliere **.NET** o **API REST** per connettersi a livello di codice a Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


## Utilizzo di codificatori live locali per eseguire l'output di flussi a più velocità in bit in un canale

## Utilizzo di codificatori live di terze parti

Per altre informazioni, vedere [Uso di codificatori di terze parti con Servizi multimediali di Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

## Gestione di canali, programmi e asset

**Informazioni generali**: [Gestione di canali e programmi ](media-services-manage-channels-overview.md).

Scegliere **Portale**, **.NET**, **REST API** per visualizzare alcuni esempi.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

## Configurazione dei criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## Creazione di una chiave simmetrica

Creare una chiave simmetrica da usare per crittografare l'asset mediante **.NET** o l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

## Configurazione dei criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


## Pubblicazione e distribuzione degli asset

**Informazioni generali**: [Informazioni generali sulla distribuzione di contenuti](media-services-deliver-content-overview.md)

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


## Abilitazione della rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md#enable_cdn).

## Ridimensionamento di un account di Servizi multimediali

È possibile scalare **Servizi multimediali** specificando il numero di **unità riservate di streaming** di cui eseguire il provisioning nell'account in uso. 

Per informazioni sul ridimensionamento delle unità di streaming, vedere l'articolo relativo alla [scalabilità di unità di streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png


<!--HONumber=52--> 