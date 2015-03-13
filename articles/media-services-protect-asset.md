<properties 
	pageTitle="Come crittografare asset in Servizi multimediali - Azure" 
	description="Informazioni su come usare la tecnologia di protezione Microsoft PlayReady per crittografare un asset in Servizi multimediali. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
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


# Procedura: Crittografare un asset dinamicamente con PlayReady o AES-128

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](../media-services-video-on-demand-workflow) and [Media Services Live Streaming workflow](../media-services-live-streaming-workflow) .
  
## Informazioni generali

Servizi multimediali di Microsoft Azure consente di distribuire contenuti crittografati (dinamicamente) con AES (Advanced Encryption Standard), mediante chiavi di crittografia a 128 bit, e con PlayReady DRM. Servizi multimediali fornisce inoltre un servizio per la distribuzione di chiavi e licenze PlayReady ai client autorizzati. Per distribuire contenuti protetti, è necessario configurare i criteri di autorizzazione della chiave simmetrica e i criteri di distribuzione degli asset.

## Configurazione

Per informazioni su come configurare i criteri di autorizzazione della chiave simmetrica, fare clic su uno dei seguenti pulsanti: 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Per informazioni su come configurare i criteri di distribuzione degli asset, fare clic su uno dei seguenti pulsanti:
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 
<!--HONumber=45--> 
