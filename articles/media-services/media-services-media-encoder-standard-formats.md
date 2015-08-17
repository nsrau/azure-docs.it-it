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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Formati e codec Media Encoder Standard


Questo documento contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con Media Encoder Standard.


[Formati di importazione Media Encoder](#import_formats)

[Formati di esportazione del codificatore multimediale](#export_formats)


##<a id="import_formats"></a>Formati di importazione Media Encounter 

La sezione seguente include l'elenco dei codec e dei formati di file supportati per l'importazione.


##Codec video:

- MPEG-2
- H. 264
- YUV420 non compressi o mezzanine
- DNxHD
- FORMATO VC-1/WMV9
- MPEG-4 parte 2
- JPEG 2000
- Theora

###Codec audio

- PCM
- AAC (AAC-LC, AAC-HE e AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Formati

Formato file|Estensioni file
---|---
FLV (con codec H. 264 e AAC) |. FLV
MP4/ISMV|*.ismv
3GP PS MPEG2, MPEG2-TS|TS,. PS, 3GP
MXF|.MXF
ASF/WMV|.mwv, ASF
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Formati di esportazione Media Encoder

La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.


Formato file|Codec video|Codec audio
---|---|---
MP4 (* .mp4)<br/><br/> (multi-bitrate MP4 container inclusi) |H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Vedere anche

[Codifica di contenuti su richiesta con Servizi multimediali di Azure](media-services-encode-asset.md)

<!---HONumber=August15_HO6-->