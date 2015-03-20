<properties 
	pageTitle="Riprodurre i contenuti" 
	description="Questo argomento descrive come riprodurre i propri contenuti." 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


# Riprodurre i contenuti

Servizi multimediali di Azure supporta molti formati di streaming noti, ad esempio Smooth Streaming, HTTP Live Streaming (HLS) e MPEG-DASH. Questo argomento descrive i lettori esistenti che possono essere usati per testare i propri flussi.  

## Riproduzione del contenuto con i lettori esistenti

Questo argomento illustra i lettori esistenti con cui è possibile la riproduzione del contenuto.

>[AZURE.NOTE]Per riprodurre contenuti organizzati in pacchetti dinamici o con crittografia dinamica, assicurarsi di disporre di almeno un'unità di streaming per l'endpoint di streaming da cui si prevede di inviare il contenuto. Per informazioni sul ridimensionamento delle unità di streaming, vedere le sezioni relative alla [scalabilità di unità di streaming](../media-services-manage-origins#scale_streaming_endpoints).


### Lettore di contenuti di Servizi multimediali del portale di gestione di Azure

il **portale di gestione di Azure** fornisce un lettore di contenuti che può essere usato per testare il proprio video.

Fare clic sul video desiderato (assicurarsi sia stato [pubblicato](../media-services-manage-content#publish)) quindi fare clic sul pulsante di **riproduzione** nella parte inferiore della schermata del portale. 
 
Considerazioni applicabili:

- Il **LETTORE DI CONTENUTI DI SERVIZI MULTIMEDIALI** esegue la riproduzione dall'endpoint di streaming predefinito. Se si vuole eseguire la riproduzione da un endpoint di streaming diverso, usare un altro lettore, ad esempio il [lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

### Lettore di Servizi multimediali di Azure

Usare il [Lettore di Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per riprodurre il proprio contenuto.


## Sviluppo di lettori video

Per informazioni su come sviluppare i propri lettori vedere l'articolo relativo allo [Sviluppo di lettori video](../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
