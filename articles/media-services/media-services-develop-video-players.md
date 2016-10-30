<properties 
    pageTitle="Sviluppo di applicazioni di lettore video" 
    description="L'argomento offre collegamenti a Player Framework e plug-in che è possibile usare per sviluppare le applicazioni client in modo che usino i flussi multimediali da Servizi multimediali." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>



#<a name="develop-video-player-applications"></a>Sviluppo di applicazioni di lettore video

##<a name="overview"></a>Overview

Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box. Questo argomento offre anche collegamenti a SDK e Player Framework, che è possibile usare per sviluppare le applicazioni client in modo che usino i flussi multimediali da Servizi multimediali di Azure.

##<a name="azure-media-player"></a>Azure Media Player

[Azure Media Player](http://aka.ms/ampinfo) è un lettore video Web progettato per consentire la riproduzione di contenuti multimediali da Servizi multimediali di Microsoft Azure su una vasta gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa. Se questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa una tecnologia di fallback come Flash o Silverlight. Indipendentemente dalla tecnologia di riproduzione usata, quindi, gli sviluppatori disporranno di un'interfaccia JavaScript unificata per accedere alle API. In questo modo, i contenuti distribuiti da Servizi multimediali di Azure potranno essere riprodotti su un'ampia gamma di dispositivi e browser senza alcuna operazione aggiuntiva.

Servizi multimediali di Microsoft Azure consente di distribuire contenuti nei formati di streaming DASH, Smooth Streaming e HLS per consentirne la riproduzione. Azure Media Player prende in considerazione tutti i vari formati e automaticamente riproduce il link migliore in base alle funzionalità della piattaforma o del browser. Servizi multimediali di Microsoft Azure consente inoltre la crittografia dinamica degli asset con la crittografia PlayReady o la crittografia della busta AES a 128 bit. Anche Azure Media Player consente la decrittografia di contenuti crittografati con PlayReady o AES a 128 bit, se correttamente configurati. 

Per altre informazioni:

- [Azure Media Player](http://aka.ms/ampinfo)
- [Documentazione di Azure Media Player](http://aka.ms/ampdocs) 
- [Blog di introduzione ad Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Iscriversi per restare aggiornati sulle ultime novità di Azure Media Player](http://aka.ms/ampsignup)
- [Aggiungere richieste di nuove funzionalità, idee e commenti e suggerimenti](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Altri strumenti per la creazione di applicazioni di lettore

È possibile usare anche uno dei seguenti SDK:

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [App Smooth Streaming Windows Store](media-services-build-smooth-streaming-apps.md)
- [Microsoft Media Platform: Player Framework](http://playerframework.codeplex.com/) 
- [Documentazione di HTML5 Player Framework](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Plug-in Microsoft Smooth Streaming per OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licenza per Microsoft® Smooth Streaming Client Porting Kit](http://aka.ms/sspk) 
- [Sviluppo di applicazioni video per XBox](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Pubblicità

Servizi multimediali di Azure offre il supporto per l'inserimento di annunci tramite la piattaforma Windows Media Platform, ovvero i player framework. Player Framework con supporto per gli annunci sono disponibili per i dispositivi Windows 8, Silverlight, Windows Phone 8 e iOS. Ciascun Player Framework contiene un codice di esempio che illustra come eseguire l'implementazione di un'applicazione di lettore. Nei file multimediali è possibile inserire tre tipi di annunci.

Lineari: annunci con frequenza massima che interrompono il video principale

Non lineari: annunci sovrapposti visualizzati durante la riproduzione del video principale, in genere un logo o un'altra immagine statica all'interno del lettore

Complementari: annunci visualizzati all'esterno del lettore

Gli annunci possono essere inseriti in qualsiasi punto della sequenza temporale del video principale. È necessario indicare al lettore quando riprodurre l'annuncio e quali annunci riprodurre. Questa operazione viene eseguita mediante una serie di file standard basati su XML: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) e Digital Video Player Ad Interface Definition (VPAID). I file VAST indicano quali annunci visualizzare, mentre i file VMAP specificano quando riprodurre i vari annunci e contengono XML VAST. I file MAST rappresentano invece un altro modo di riprodurre in sequenza annunci contenenti XML VAST. I file VPAID, infine, definiscono un'interfaccia tra il lettore video e l'annuncio o il server di annunci. Per altre informazioni, vedere [Inserimento di annunci](https://msdn.microsoft.com/library/dn387398.aspx).

Per informazioni sul supporto di sottotitoli codificati e annunci nei video in streaming live, vedere [Sottotitoli codificati supportati e standard per l'inserimento di annunci](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Integrazione di uno streaming video adattivo MPEG-DASH in un'applicazione HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repository dash.js di GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 



<!--HONumber=Oct16_HO2-->


