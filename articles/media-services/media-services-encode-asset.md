<properties 
	pageTitle="Panoramica e confronto dei codificatori multimediali su richiesta di Azure" 
	description="In questo argomento viene fornita una panoramica e un confronto tra i codificatori multimediali su richiesta di Azure." 
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
	ms.date="09/23/2015"  
	ms.author="juliako"/>

#Panoramica e confronto dei codificatori multimediali su richiesta di Azure

##Panoramica della codifica

I codificatori comprimono i file multimediali digitali mediante l'uso di codec. In genere nei codificatori sono disponibili diverse impostazioni, che permettono di specificare le proprietà dei file multimediali generati, ad esempio i codec usati, il formato dei file, la risoluzione e la velocità in bit. I formati di file sono contenitori che includono il video compresso, oltre a informazioni sui codec usati per la compressione del video.

I codec consistono in due componenti: un componente per la compressione dei file multimediali digitali per la trasmissione e un altro per la decompressione dei file multimediali digitali per la riproduzione. Sono disponibili codec audio per la compressione e la decompressione di audio e codec video per la compressione e decompressione di video. I codec possono usare la compressione con o senza perdita di dati. I codec senza perdita di dati conservano tutte le informazioni durante la compressione. Quando il file è decompresso, si ottiene un file identico al file multimediale di input. I codec senza perdita di dati sono quindi ideali per l'archiviazione. I codec con perdita di dati perdono alcune informazioni durante la codifica e producono file di dimensioni minori rispetto all'originale, ma con una riduzione della qualità del video. Sono ideali per la trasmissione in streaming su Internet.

È importante comprendere la differenza tra codec e formati di file. I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso. Per altre informazioni, vedere il post di blog relativo al [confronto tra le funzionalità di codifica e di creazione pacchetti](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Servizi multimediali fornisce il servizio di creazione dinamica dei pacchetti, che consente di distribuire i contenuti codificati in formato MP4 o Smooth Streaming con velocità in bit adattiva in formati di streaming supportati da Servizi multimediali (MPEG DASH, HLS, Smooth Streaming, HDS), senza dover ricreare i pacchetti con questi formati di streaming.

Per sfruttare i vantaggi del servizio di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md), è necessario seguire questa procedura:

- Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti più avanti in questa esercitazione).
- Ottenere almeno un'unità di streaming on demand per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere l'articolo sulla [procedura per scalare unità riservate di streaming on demand](media-services-manage-origins.md#scale_streaming_endpoints/).

Servizi multimediali supporta i seguenti codificatori su richiesta descritti in questo articolo:

- **Codificatore multimediale standard**
- **Azure Media Encoder** 
- **Flusso di lavoro Premium del codificatore multimediale**

In questo articolo è fornita una breve panoramica dei codificatori multimediali su richiesta e sono presenti collegamenti ad articoli che contengono informazioni più dettagliate. L'argomento fornisce inoltre il confronto dei codificatori.

Si noti che per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni, vedere [Scalabilità dell’unità di codifica](media-services-portal-encoding-units.md).

##Codificatore multimediale standard

###Panoramica

È consigliabile utilizzare il codificatore multimediale standard. Tuttavia attualmente non è esposto tramite il portale di Azure.

Rispetto ad Azure Media Encoder, questo codificatore supporta più codec e formati di input e output. Questo approccio offre i vantaggi seguenti:

- Maggiore tolleranza del metodo di creazione del file di input
- Dispone di una migliore qualità del codec H.264 di Azure Media Encoder
- Si basa su una pipeline più recente e più flessibile
- È più affidabile/resiliente

###Utilizzo

[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formati

[Codec e formati](media-services-media-encoder-standard-formats.md)

###Impostazioni predefinite

Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadati di input e output

I metadati di input dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

I metadati di output dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Immagine di anteprima

Per informazioni su come generare anteprime, vedere [Come generare anteprime utilizzando Media Encoder Standard](media-services-dotnet-generate-thumbnail-with-mes.md).

###Sovrapposizioni audio e video

Attualmente non supportate.

###Vedere anche

[Blog di Servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Azure Media Encoder

###Panoramica

Azure Media Encoder è uno dei codificatori supportati da Servizi multimediali. A partire da luglio 2015, si consiglia di utilizzare [Media Encoder Standard](media-services-encode-asset.md#media_encoder_standard).

###Utilizzo

[Come codificare con Azure Media Encoder](media-services-dotnet-encode-asset.md)

###Formati

[Codec e formati](media-services-azure-media-encoder-formats.md)

###Impostazioni predefinite

Il Codificatore multimediale di Azure viene configurato mediante una delle impostazioni predefinite del codificatore descritte [qui](https://msdn.microsoft.com/library/azure/dn619392.aspx). In alternativa, è possibile ottenere i file del set di impostazioni predefinite di Azure Media Encoder [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Metadati di input e output

I metadati di input dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

I metadati di output dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Immagine di anteprima

[Creazione di un'immagine di anteprima](https://msdn.microsoft.com/library/hh973624.aspx)

###Sovrapposizioni audio e video

[Creazione di sovrimpressioni](media-services-azure-media-customize-ame-presets.md#creating-overlays).

###Convenzione di denominazione

[Come modificare i nomi dei file di output](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###Vedere anche

[Codifica di file multimediali con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md)

##Flusso di lavoro Premium del codificatore multimediale

###Panoramica

[Introduzione alla codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###Utilizzo

Il flusso di lavoro Premium del codificatore multimediale viene configurato usando flussi di lavoro complessi. Per creare e aggiornare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md).

[Come usare la codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>Confronto tra i codificatori

###<a id="billing"></a>Metro di fatturazione usato da ogni codificatore

Nome del processore multimediale|Prezzi applicabili|Note
---|---|---
**Codificatore multimediale standard** |CODIFICATORE|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE.
**Azure Media Encoder** |CODIFICATORE|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE.
**Flusso di lavoro Premium del codificatore multimediale** |CODIFICATORE PREMIUM|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE PREMIUM.


Questa sezione mette a confronto le funzionalità di codifica di **Media Encoder Standard**, **Azure Media Encoder** e **Media Encoder Premium Workflow**.


###Contenitore di input/formati di file

Contenitore di input/formati di file|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
Adobe® Flash® F4V |Sì|No |Sì
MXF/SMPTE 377M |Sì|Limitato|Sì
GXF |Sì|No |Sì
MPEG-2 Transport Stream |Sì|Sì |Sì
MPEG-2 Program Stream |Sì|Sì |Sì
MPEG-4/MP4 |Sì|Sì |Sì
Windows Media/ASF |Sì|Sì |Sì
AVI (non compresso 8 bit/10 bit)|Sì|Sì |Sì
3GPP/3GPP2 |Sì|Sì |No
Formato di file Smooth Streaming (PIFF 1.3)|Sì|Sì|No
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sì|No|No
Matroska/WebM |Sì|No|No

###Codec video di input

Codec video di input|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit|Solo 4:2:0 a 8 bit|Sì
Avid DNxHD (in MXF) |Sì|No|Sì
DVCPro/DVCProHD (in MXF) |Sì|No|Sì
JPEG2000 |Sì|No|Sì
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Fino a 422 Profile|Fino a 422 Profile|Sì
MPEG-1 |Sì|Sì|Sì
Windows Media Video/VC-1 |Sì|Sì|Sì
Canopus HQ/HQX |No|Sì|No
MPEG-4 parte 2 |Sì|No|No
[Theora](https://en.wikipedia.org/wiki/Theora) |Sì|No|No

###Codec audio di input

Codec audio di input|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003) |No|No|Sì
Dolby® E |No|No|Sì
Dolby® Digital (AC3) |No|Sì|Sì
Dolby® Digital Plus (E-AC3) |No|No|Sì
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì|Sì
MPEG Layer 2|Sì|Sì|Sì
MP3 (MPEG-1 Audio Layer 3)|Sì|Sì|Sì
Windows Media Audio|Sì|Sì|Sì
WAV/PCM|Sì|Sì|Sì
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sì|No|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sì|No|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sì|No|No


###Contenitore di output/formati di file

Contenitore di output/formati di file|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
Adobe® Flash® F4V|No|No|Sì
MXF (OP1a, XDCAM e AS02)|No|No|Sì
DPP (incluso AS11)|No|No|Sì
GXF|No|No|Sì
MPEG-4/MP4|Sì|Sì|Sì
MPEG-TS|Sì|No|Sì
Windows Media/ASF|No|Sì|Sì
AVI (non compresso 8 bit/10 bit)|No|No|Sì
Formato di file Smooth Streaming (PIFF 1.3)|No|Sì|Sì

###Codec video di output

Codec video di output|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Solo 4:2:0 a 8 bit|Solo 4:2:0 a 8 bit fino a 1080 p|Sì
Avid DNxHD (in MXF)|No|No|Sì
DVCPro/DVCProHD (in MXF)|No|No|Sì
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|No|No|Sì
MPEG-1|No|No|Sì
Windows Media Video/VC-1|No|Sì|Sì
Creazione anteprime JPEG|No|Sì|Sì

###Codec audio di output

Codec audio di output|Codificatore multimediale standard|Azure Media Encoder|Flusso di lavoro Premium del codificatore multimediale
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|No|No|Sì
Dolby® Digital (AC3)|No|Sì|Sì
Dolby® Digital Plus (E-AC3) fino a 7.1|No|Fino a 5.1|Sì
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì|Sì
MPEG Layer 2|No|No|Sì
MP3 (MPEG-1 Audio Layer 3)|No|No|Sì
Windows Media Audio|No|Sì|Sì


##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Articoli correlati

- [Quote e limitazioni](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Sept15_HO4-->