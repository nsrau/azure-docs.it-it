<properties 
	pageTitle="Codifica di contenuti su richiesta con Servizi multimediali di Azure" 
	description="Questo argomento fornisce informazioni generali sulla codifica di contenuti su richiesta con Servizi multimediali." 
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
	ms.date="08/11/2015"  
	ms.author="juliako"/>

#Codifica di contenuti su richiesta con Servizi multimediali di Azure

Questo argomento fa parte della serie [Flusso di lavoro Video On Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).

##Panoramica

Servizi multimediali supporta i seguenti codificatori:

- [Codificatore multimediale standard](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [Flusso di lavoro Premium del codificatore multimediale](#media_encoder_premium_workflow)

La [sezione seguente](#compare_encoders) mette a confronto le funzionalità di codifica dei codificatori supportati.

Per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni sulla scalabilità delle unità di codifica, vedere i seguenti argomenti del **portale** e di **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Codificatore multimediale standard

[Formati supportati dal codificatore multimediale standard](media-services-media-encoder-standard-formats.md): illustra i formati di file e flussi supportati dal **codificatore multimediale standard**.

Il **codificatore multimediale standard** viene configurato usando uno dei set di impostazioni del codificatore descritti [qui](http://go.microsoft.com/fwlink/?LinkId=618336) o dei set di impostazioni personalizzati basati su [questi](http://go.microsoft.com/fwlink/?LinkId=618336) set di impostazioni.

Per altre informazioni, vedere [questo blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a>Codificatore multimediale di Azure

[Formati supportati dal codificatore di Servizi multimediali](media-services-azure-media-encoder-formats.md): illustra i formati di file e flussi supportati dal **Codificatore multimediale di Azure**.

Il **Codificatore multimediale di Azure** viene configurato mediante una delle stringhe del set di impostazioni descritte [qui](https://msdn.microsoft.com/library/azure/dn619392.aspx). In alternativa, è possibile ottenere i file del set di impostazioni del Codificatore multimediale di Azure [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Esempio

Codificare con **Azure Media Encoder** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Altri argomenti correlati

[Creazione dinamica dei pacchetti](https://msdn.microsoft.com/library/azure/jj889436.aspx): descrive la procedure per eseguire la codifica in MP4 a velocità in bit adattiva e distribuire in modo dinamico file Smooth Streaming, Apple HLS o MPEG-DASH.

[Controllo dei nomi file di output del codificatore di Servizi multimediali](https://msdn.microsoft.com/library/azure/dn303341.aspx): descrive le convenzioni di denominazione di file usate dal Codificatore multimediale di Azure e la procedura per modificare i nomi di file di output.

[Codifica di file multimediali con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): descrive la procedura per codificare tracce audio usando la codifica Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Flusso di lavoro Premium del codificatore multimediale 

**Nota** Il processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale descritto in questo argomento non è disponibile in Cina.

[Formati supportati dal flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md): vengono illustrati formati file e codec supportati dal **flusso di lavoro Premium del codificatore multimediale**.

### Progettazione flussi di lavoro

Il **flusso di lavoro Premium del codificatore multimediale** viene configurato usando flussi di lavoro complessi. Per creare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md).

È possibile ottenere i file del flusso di lavoro predefiniti [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Nella cartella è presente anche una descrizione dei file.

###Esempio
Codificare con il **flusso di lavoro Premium del codificatore multimediale** usando **.NET**. Per altre informazioni, vedere [Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Confronto tra i codificatori

###<a id="billing"></a>Metro di fatturazione usato da ogni codificatore

Nome del processore multimediale|Prezzi applicabili|Note
---|---|---
**Codificatore multimediale di Windows Azure** |CODIFICATORE LEGACY|Le attività di codifica verranno addebitate in base alla somma delle dimensioni degli asset di input e dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE LEGACY.
**Azure Media Encoder** |CODIFICATORE|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE.
**Codificatore multimediale standard** |CODIFICATORE|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE.
**Flusso di lavoro Premium del codificatore multimediale** |CODIFICATORE PREMIUM|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE PREMIUM.



Questa sezione mette a confronto le funzionalità di codifica del **Codificatore multimediale di Azure**, del **flusso di lavoro Premium del codificatore multimediale** e del **codificatore multimediale standard**.


###Formati di input

Contenitore di input/formati di file

Contenitore di input/formati di file|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
Adobe® Flash® F4V|Sì|No|Sì
MXF/SMPTE 377M|Sì|Limitato|Sì
GXF|Sì|No|Sì
MPEG-2 Transport Stream|Sì|Sì|Sì
MPEG-2 Program Stream|Sì|Sì|Sì
MPEG-4/MP4|Sì|Sì|Sì
Windows Media/ASF|Sì|Sì|Sì
AVI (non compresso 8 bit/10 bit)|Sì|Sì|Sì
3GPP/3GPP2|No|Sì|Sì
Formato di file Smooth Streaming (PIFF 1.3)|No|Sì|Sì
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|No|No|Sì
Matroska/WebM|No|No|Sì

Codec video di input

Codec video di input|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra|Sì|Solo 4:2:0 a 8 bit|4:2:0 e 4:2:2 a 8 bit
Avid DNxHD (in MXF)|Sì|No|Sì
DVCPro/DVCProHD (in MXF)|Sì|No|Sì
JPEG2000|Sì|No|Sì
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Sì|Fino a 422 Profile|Fino a 422 Profile
MPEG-1|Sì|Sì|Sì
Windows Media Video/VC-1|Sì|Sì|Sì
Canopus HQ/HQX|No|Sì|No
MPEG-4 parte 2|No|No|Sì
[Theora](https://en.wikipedia.org/wiki/Theora)|No|No|Sì

Codec audio di input

Codec audio di input|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Sì|No|No
Dolby® E|Sì|No|No
Dolby® Digital (AC3)|Sì|Sì|No
Dolby® Digital Plus (E-AC3)|Sì|No|No
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì|Sì
MPEG Layer 2|Sì|Sì|Sì
MP3 (MPEG-1 Audio Layer 3)|Sì|Sì|Sì
Windows Media Audio|Sì|Sì|Sì
WAV/PCM|Sì|Sì|Sì
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|No|No|Sì
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |No|No|Sì
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|No|No|Sì

###Formati di output

Contenitore di output/formati di file

Contenitore di output/formati di file|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
Adobe® Flash® F4V|Sì|No|No
MXF (OP1a, XDCAM e AS02)|Sì|No|No
DPP (incluso AS11)|Sì|No|No
GXF|Sì|No|No
MPEG-4/MP4|Sì|Sì|Sì
MPEG-TS|Sì|No|Sì
Windows Media/ASF|Sì|Sì|No
AVI (non compresso 8 bit/10 bit)|Sì|No|No
Formato di file Smooth Streaming (PIFF 1.3)|Sì|Sì|No

Codec video di output

Codec video di output|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Sì|Solo 4:2:0 a 8 bit fino a 1080 p|Solo 4:2:0 a 8 bit
Avid DNxHD (in MXF)|Sì|No|No
DVCPro/DVCProHD (in MXF)|Sì|No|No
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Sì|No|No
MPEG-1|Sì|No|No
Windows Media Video/VC-1|Sì|Sì|No
Creazione anteprime JPEG|Sì|Sì|No

Codec audio di output

Codec audio di output|Flusso di lavoro Premium del codificatore multimediale|Azure Media Encoder|Codificatore multimediale standard
---|---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|Sì|No|No
Dolby® Digital (AC3)|Sì|Sì|No
Dolby® Digital Plus (E-AC3) fino a 7.1|Sì|Fino a 5.1|No
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì|Sì
MPEG Layer 2|Sì|No|No
MP3 (MPEG-1 Audio Layer 3)|Sì|No|No
Windows Media Audio|Sì|Sì|No

##Articoli correlati

- [Introduzione alla codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Come usare la codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Quote e limitazioni](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO7-->