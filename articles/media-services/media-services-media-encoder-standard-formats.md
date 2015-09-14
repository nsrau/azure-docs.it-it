<properties 
	pageTitle="Formati e codec Media Encoder Standard"
	description="Questo argomento fornisce una panoramica dei formati e dei codec di Azure Media Encoder."
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

#Formati e codec Media Encoder Standard


Questo documento contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con Media Encoder Standard.


##Contenitore di input/formati di file

Formato di file (estensioni di file)|Supportato
---|---|---|---
FLV (con codec H. 264 e AAC) (.flv) |Sì 
MXF (.mxf) |Sì 
GXF (.gxf) |Sì 
PS MPEG2, MPEG2-TS, 3GP (.ts, .ps, .3gp) |Sì 
Windows Media Video (WMV) (.wmv) |Sì 
AVI (non compresso 8 bit/10 bit) (.avi)|Sì 
MP4/ISMV (.ismv)|Sì 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sì 
Matroska/WebM (.mkv) |Sì 
WAVE/WAV (.wav) |Sì 
 

##Codec video di input

Codec video di input|Supportato
---|---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit 
Avid DNxHD (in MXF) |Sì 
DVCPro/DVCProHD (in MXF) |Sì 
JPEG 2000 |Sì 
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Fino a 422 Profile 
MPEG-1 |Sì 
FORMATO VC-1/WMV9 |Sì 
Canopus HQ/HQX |No 
MPEG-4 parte 2 |Sì 
[Theora](https://en.wikipedia.org/wiki/Theora) |Sì 
YUV420 non compressi o mezzanine |Sì


##Codec audio di input

Codec audio di input|Supportato
---|---|---|---
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì 
MPEG Layer 2|Sì 
MP3 (MPEG-1 Audio Layer 3)|Sì 
Windows Media Audio|Sì 
WAV/PCM|Sì 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sì 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sì 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sì 
AMR (velocità multipla adattiva)|Sì
AES (SMPTE 331M e 302M, AES3-2003) |No 
Dolby® E |No 
Dolby® Digital (AC3) |No 
Dolby® Digital Plus (E-AC3) |No 


##Formati e codec di output

La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.


Formato file|Codec video|Codec audio
---|---|---
MP4 (* .mp4)<br/><br/> (multi-bitrate MP4 container inclusi) |H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Vedere anche

[Codifica di contenuti su richiesta con Servizi multimediali di Azure](media-services-encode-asset.md)

[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=September15_HO1-->