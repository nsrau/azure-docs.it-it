<properties 
	pageTitle="Informazioni generali sulla distribuzione di contenuti ai clienti" 
	description="Questo argomento fornisce informazioni generali su tutti gli aspetti inerenti la distribuzione di contenuti con Servizi multimediali di Azure." 
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
	ms.date="07/12/2016"
	ms.author="juliako"/>


#Informazioni generali sulla distribuzione di contenuti ai clienti

##Panoramica

Quando si distribuiscono contenuti ai clienti (eventi in live streaming o video on demand), l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete.

Per raggiungere questo obiettivo:

- Codificare il flusso video a più velocità in bit (velocità in bit adattiva), garantendo in questo modo la qualità e le condizioni di rete.
- Usare la funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md) di Servizi multimediali per riorganizzare dinamicamente il flusso in nuovi pacchetti creati con protocolli diversi, garantendo in questo modo la trasmissione a diversi tipi di dispositivi. Servizi multimediali supporta operazioni di trasmissione nelle seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access).

In questo argomento viene fornita una panoramica dei concetti di consegna dei contenuti importanti.

Per verificare i problemi noti, vedere [questa](media-services-deliver-content-overview.md#known-issues) sezione.

##Creazione dinamica dei pacchetti


Servizi multimediali fornisce il servizio di creazione dinamica dei pacchetti, che consente di distribuire i contenuti codificati in formato MP4 o Smooth Streaming con velocità in bit adattiva in formati di streaming supportati da Servizi multimediali (MPEG DASH, HLS, Smooth Streaming, HDS), senza dover ricreare i pacchetti con questi formati di streaming. Si consiglia di usare la creazione dinamica dei pacchetti per distribuire i contenuti.

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

- Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva.
- Ottenere almeno un'unità di streaming on demand per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere l'articolo sulla [procedura per scalare unità riservate di streaming on demand](media-services-manage-origins.md#scale_streaming_endpoints).

Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un unico formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Oltre alle funzionalità di creazione dinamica dei pacchetti, le unità riservate di streaming on demand offrono capacità in uscita dedicate, acquistabili in incrementi di 200 Mbps. Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming on demand, si consiglia di acquistare unità riservate di streaming on demand.

Per altre informazioni, vedere [Creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md).

##Filtri e manifesti dinamici

Servizi multimediali consente di definire filtri per i propri asset. I filtri sono costituiti da regole lato server che consentono ai clienti di eseguire operazioni particolari, come riprodurre solo una sezione di un video (anziché il video intero) oppure specificare solo un sottoinsieme di rendering audio e video, in modo che possa essere gestito dal dispositivo del cliente (anziché tutti i rendering associati all'asset). Il filtro degli asset viene eseguito attraverso **manifesti dinamici** creati su richiesta del cliente per trasmettere un video in streaming in base ai filtri specificati.

Per altre informazioni, vedere [Filtri e manifesti dinamici](media-services-dynamic-manifest-overview.md).

##Localizzatori

Per poter fornire all'utente un URL da usare per scaricare o trasmettere in streaming i contenuti distribuiti, è prima necessario "pubblicare" la risorsa creando un localizzatore. I localizzatori forniscono un punto di ingresso per accedere ai file contenuti in un asset. Servizi multimediali supporta due tipi di localizzatori:

- Localizzatori **OnDemandOrigin**, che consentono di trasmettere in streaming file multimediali (ad esempio MPEG DASH, HLS o Smooth Streaming) o eseguire il download progressivo dei file.
-  Localizzatori di URL **SAS** (firma di accesso), che consentono di scaricare file multimediali nel computer locale.

Per definire le autorizzazioni (ad esempio di lettura, scrittura ed elenco) e il periodo di tempo durante il quale un client può accedere a un determinato asset, viene usato un **criterio di accesso**. Si noti che l'autorizzazione di elenco (AccessPermissions.List) non deve essere usata durante la creazione di un localizzatore OnDemandOrigin.

Per i localizzatori viene definita una data di scadenza. Quando si pubblicano asset attraverso il portale, vengono creati localizzatori con scadenza a 100 anni.

>[AZURE.NOTE] I localizzatori creati attraverso il portale prima del mese di marzo 2015 hanno una data di scadenza di due anni.

Per aggiornare la data di scadenza di un localizzatore, è possibile usare [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Si noti che quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato.
 
I localizzatori non sono progettati per gestire il controllo dell'accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management). Per altre informazioni, vedere [Protezione dei file multimediali](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando si crea un localizzatore, è possibile che si verifichi un ritardo di 30 secondi a causa dei processi di archiviazione e propagazione necessari in Archiviazione di Azure.


##Streaming adattivo 

Le tecnologie a velocità in bit adattiva consentono al lettore video di determinare le condizioni della rete e di effettuare una selezione tra più velocità in bit. Se le prestazioni della comunicazione di rete diminuiscono, il client può selezionare una velocità in bit inferiore consentendo al lettore di continuare la riproduzione del video con una qualità leggermente ridotta. Se invece le prestazioni migliorano, il client può passare a una velocità in bit maggiore e fornire quindi una migliore qualità video. Servizi multimediali di Azure supporta le seguenti tecnologie a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS.

Per poter fornire agli utenti URL di streaming, è prima necessario creare un localizzatore OnDemandOrigin. Creando il localizzatore è possibile ottenere il valore Path di base dell'asset che include i contenuti da trasmettere in streaming. Tuttavia, per trasmettere in streaming questi contenuti, è necessario modificare ulteriormente il percorso. Per creare un URL completo per il file manifesto di streaming, si deve concatenare il valore Path del localizzatore e il nome del file manifesto (nomefile.ism), quindi aggiungere /Manifest e un formato appropriato (se necessario) al percorso di origine del localizzatore.

>[AZURE.NOTE]Lo streaming dei contenuti può essere eseguito anche tramite una connessione SSL. A questo scopo, verificare che gli URL di streaming inizino con HTTPS.

Lo streaming tramite SSL è possibile solo se l'endpoint di streaming da cui si inviano i contenuti è stato creato dopo il 10 settembre 2014. Se gli URL di streaming si basano sugli endpoint di streaming creati dopo il 10 settembre, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). Gli URL di streaming contenenti "origin.mediaservices.windows.net" (il vecchio formato) non supportano SSL. Se l'URL è nel vecchio formato e si desidera poter eseguire lo streaming tramite SSL, creare un nuovo endpoint di streaming. Usare gli URL creati con il nuovo endpoint di streaming per lo streaming dei contenuti tramite SSL.


##Formati degli URL di streaming URL:

###Formato MPEG DASH

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=mpd-time-csf)

Esempio

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



###Formato Apple HTTP Live Streaming (HLS) V4

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

###Formato Apple HTTP Live Streaming (HLS) V3

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

###Formato Apple HTTP Live Streaming (HLS) con il filtro solo audio

Per impostazione predefinita le tracce di solo audio sono incluse nel contenuto HLS del manifesto. È necessario per la certificazione di Apple store per reti cellulari. In questo caso, se un client non dispone di larghezza di banda sufficiente o connessa tramite una connessione 2G si passa alla sola riproduzione di audio. Ciò consente di mantenere costante il flusso senza memorizzazione nel buffer, ma comunque con l’inconveniente di non visualizzare alcun video. Tuttavia, in alcuni scenari, si potrebbe preferire il buffer del lettore di Windows solamente per l’audio. Se si desidera rimuovere la traccia solo audio è possibile aggiungere (solo audio = false) per l'URL e la si rimuove.

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


###Formato Smooth Streaming

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest

Esempio:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

###<a id="fmp4_v20"></a>Manifesto Smooth Streaming 2.0 (manifesto legacy)

Per impostazione predefinita, il manifesto Smooth Streaming contiene il tag di ripetizione (r-tag). Alcuni lettori, tuttavia, non supportano gli r-tag. Tali client possono usare formati che disabilitano gli r-tag:

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

###HDS (solo per licenze Adobe PrimeTime/Access)

{nome endpoint di streaming-nome account servizi multimediali}.streaming.mediaservices.windows.net/{ID localizzatore}/{nome file}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

##Download progressivo 

Il download progressivo è una tecnologia che consente di avviare la riproduzione di file multimediali prima che il download dell'intero file sia stato completato. Non è possibile eseguire il download progressivo di file .ism* (ismv, isma, ismt, ismc).

Per eseguire il download progressivo di contenuti, usare un localizzatore di tipo OnDemandOrigin. Il seguente esempio mostra l'URL basato sul tipo di localizzatore OnDemandOrigin:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Tenere presenti le seguenti considerazioni:

- Per eseguire il download progressivo è necessario decrittografare qualsiasi asset di archiviazione crittografato che si desideri trasmettere in streaming dal servizio di origine.

##Scaricare

Per scaricare i contenuti in un dispositivo client, è necessario creare un localizzatore SAS, che offre l'accesso al contenitore di archiviazione di Azure in cui si trova il file. Per compilare l'URL di download, è necessario incorporare il nome di file tra l'host e la firma SAS.

Il seguente esempio mostra l'URL basato sul localizzatore SAS:

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Si applicano le considerazioni seguenti:

- Per eseguire il download progressivo è necessario decrittografare qualsiasi asset di archiviazione crittografato che si desideri trasmettere in streaming dal servizio di origine.
- Un download ha esito negativo se non viene completato entro 12 ore.

##Endpoint di streaming

Un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per la successiva distribuzione. Il flusso in uscita da un servizio endpoint di streaming può essere costituito da un flusso live o da un asset video on demand associato all'account di Servizi multimediali. È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità riservate di streaming. È consigliabile allocare almeno un'unità riservata per le applicazioni in un ambiente di produzione. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di un servizio multimediale](media-services-manage-origins.md#scale_streaming_endpoints).

##Problemi noti

### Modifiche alla versione del manifesto Smooth Streaming

Prima della versione del servizio di luglio 2016, quando gli asset prodotti da Media Encoder Standard, dal flusso di lavoro Premium del codificatore multimediale o dalla versione legacy di Azure Media Encoder venivano trasmessi con la creazione dinamica dei pacchetti, il manifesto Smooth Streaming restituito era conforme alla versione 2.0, in cui le durate dei frammenti non usano i cosiddetti tag di ripetizione ('r'). ad esempio:

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" n="0" />
			<c d="2000" />
			<c d="2000" />
			<c d="2000" />
		</StreamIndex>
	</SmoothStreamingMedia>

Dopo la versione del servizio di luglio 2016, il manifesto Smooth Streaming generato è conforme alla versione 2.2, con durate di frammento che usano i tag di ripetizione, ad esempio:

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" r="4" />
		</StreamIndex>
	</SmoothStreamingMedia>

È possibile che alcuni client Smooth Streaming legacy non supportino i tag di ripetizione e non carichino il manifesto. Per attenuare il problema è possibile usare il parametro del formato manifesto legacy, **format=fmp4-v20**. Per altre informazioni, vedere [questa](media-services-deliver-content-overview.md#fmp4_v20) sezione. In alternativa, aggiornare il client alla versione più recente che supporta i tag di ripetizione.

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Argomenti correlati

[Aggiornamento di Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione](media-services-roll-storage-access-keys.md)
 

<!---HONumber=AcomDC_0713_2016-->