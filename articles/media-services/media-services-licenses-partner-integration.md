<properties 
	pageTitle="Uso di partner per distribuire licenze Widevine a Servizi multimediali di Azure" 
	description="Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze castLabs." 
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
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Uso di partner per distribuire licenze Widevine a Servizi multimediali di Azure

##Panoramica

Servizi multimediali di Microsoft Azure consente di distribuire contenuto protetto MPEG-DASH con DRM Widevine, che viene crittografato per la specifica CENC (Common Encryption).

>[AZURE.NOTE]Attualmente, Servizi multimediali non fornisce un server licenze Widevine. È possibile utilizzare i seguenti partner AMS per consentire la distribuzione di licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

È possibile utilizzare [castLabs](http://castlabs.com/company/partners/azure/) per distribuire licenze Widevine. Per ulteriori informazioni, vedere [Uso di castLabs per distribuire licenze DRM a Servizi multimediali di Azure](media-services-castlabs-integration.md)

##Axinom

È possibile utilizzare [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) per distribuire licenze Widevine. Per ulteriori informazioni, vedere [Uso di Axinom per distribuire licenze DRM a Servizi multimediali di Azure](media-services-axinom-integration.md)


##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Vedere anche

[Uso della crittografia comune dinamica PlayReady e/o Widevine](media-services-protect-with-drm.md)

[Blog di Mingfei](https://azure.microsoft.com/it-IT/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Oct15_HO2-->