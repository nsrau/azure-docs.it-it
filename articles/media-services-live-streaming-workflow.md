<properties 
	pageTitle="Flusso di lavoro Live Streaming di Servizi multimediali" 
	description="Questo argomento descrive i passaggi di un tipico flusso di lavoro Live Streaming di Servizi multimediali." 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


# Flusso di lavoro Live Streaming di Servizi multimediali

## Informazioni generali

Questo argomento descrive i passaggi di un tipico flusso di lavoro Live Streaming di Servizi multimediali di Azure. Ogni passaggio è collegato a uno specifico argomento. Per le attività che possono essere svolte mediante diverse tecnologie, sono disponibili pulsanti di collegamento alla tecnologia scelta, ad esempio .NET o REST.   

Si noti che è possibile integrare Servizi multimediali con gli strumenti e i processi esistenti. Ad esempio, codificare il contenuto in sede e quindi caricarlo in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. 

Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali interessate dal Flusso di lavoro Video on Demand.
![Live workflow][live-overview]


Per altre informazioni, vedere la [panoramica di Servizi multimediali](../media-services-overview).

## Creare un account di Servizi multimediali

Usare il **portale di gestione di Azure** per [creare un account di Servizi multimediali di Azure](../media-services-create-account/).

## Configurazione degli endpoint di streaming

[lavori in corso]

## Configurare l'ambiente di sviluppo  

Scegliere **.NET** o **API REST** per l'ambiente di sviluppo.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Connettersi a livello di codice  

Scegliere **.NET** o **API REST** per connettersi a livello di codice a Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## Usare i transcodificatori live

Per altre informazioni, vedere [Uso di codificatori di terze parti con Servizi multimediali di Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

## Gestire canali, programmi e assets

Per altre informazioni, vedere l'argomento relativo a [Streaming live](https://msdn.microsoft.com/library/azure/dn783466.aspx).

## Configurare la protezione del contenuto e i criteri di autorizzazione chiave simmetrica 

Configurare i criteri di autorizzazione della chiave simmetrica usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## Configurare i criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Pubblicare asset

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Scalare un account di Servizi multimediali

È possibile scalare **Servizi multimediali** specificando il numero di **unità riservate di streaming** di cui eseguire il provisioning nell'account in uso. 

Per informazioni sul ridimensionamento delle unità di streaming, vedere le sezioni relative alla [scalabilità di unità di streaming](../media-services-manage-origins#scale_streaming_endpoints).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
