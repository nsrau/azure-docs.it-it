<properties 
	pageTitle="Codec e formati del flusso di lavoro Premium del codificatore multimediale" 
	description="Questo argomento fornisce una panoramica dei codec e dei formati del flusso di lavoro Premium del codificatore multimediale" 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Codec e formati del flusso di lavoro Premium del codificatore multimediale


**Nota** Il processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale descritto in questo argomento non è disponibile in Cina.

Questo documento include un elenco dei codec e dei formati di file di input e output supportati nella versione di anteprima pubblica del codificatore per il **flusso di lavoro Premium del codificatore multimediale**.

[Codec e formati di input del flusso di lavoro Premium del codificatore multimediale](#input_formats)

[Codec e formati di output del flusso di lavoro Premium del codificatore multimediale](#output_formats)

Il **flusso di lavoro Premium del codificatore multimediale** supporta i sottotitoli codificati descritti in [questa](#closed_captioning) sezione.


##<a id="input_formats"></a>Codec e formati di input del flusso di lavoro Premium del codificatore multimediale

La seguente sezione include l'elenco dei codec e dei formati di file supportati come input da questo processore di contenuti multimediali.

###Contenitore di input/formati di file

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 Transport Stream
- MPEG-2 Program Stream
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compresso 8 bit/10 bit)

###Codec video di input

- AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- JPEG2000
- MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1

###Codec audio di input

- AES (SMPTE 331M e 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Codec e formati di output del flusso di lavoro Premium del codificatore multimediale

La seguente sezione include l'elenco dei codec e dei formati di file supportati come output da questo processore di contenuti multimediali.

###Contenitore di output/formati di file

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM e AS02)
- DPP (incluso AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compresso 8 bit/10 bit)
- Formato di file Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###Codec video di output

- AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1
- Creazione anteprime JPEG

###Codec audio di output

- AES (SMPTE 331M e 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) fino a 7.1
- AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Supporto per sottotitoli codificati

In ingresso, il **flusso di lavoro Premium del codificatore multimediale** supporta:

1. File SCC
1. File SMPTE-TT
1. CEA-608/CEA-708 – Trasportato come dati (messaggi SEI di flussi elementari H.264, ATSC/53, SCTE20) o trasportato come dati ausiliari in file MXF/GXF
1. File con sottotitoli STL

In uscita, sono disponibili le seguenti opzioni:

1. Conversione da CEA-608 a CEA-708
1. Pass-through di CEA-608/CEA-708 (incorporato in messaggi SEI di flussi elementari H.264 o trasportato come dati ausiliari in file MXF)
1. SCC
1. SMPTE Timed Text (da origine CEA-608 per SMPTE RP2052; inclusa la creazione di file DFXP)
1. File di sottotitoli SRT
1. Flussi di sottotitoli DVB

Nota: non tutti i formati di output precedenti sono supportati per il recapito tramite streaming in Servizi multimediali di Azure.

##Problemi noti

Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto.


##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->