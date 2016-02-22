<properties 
	pageTitle="Formati e codec di Azure Media Encoder" 
	description="Questo argomento fornisce una panoramica dei formati e dei codec del Codificatore multimediale di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Formati e codec di Azure Media Encoder

Questo documento contiene un elenco dei formati di file di input e output e dei codec più comuni che è possibile usare con Azure Media Encoder.


##Formati di file di input (contenitori)
 
Formato di file (estensioni di file)|Supportato
---|---
3GPP, 3GPP2 (.3gp, .3g2, .3gp2) |Sì
Advanced Systems Format (ASF) (.asf) |Sì
Advanced Video Coding High Definition (AVCHD) [ MPEG-2 Transport Stream ] (.mts, .m2ts) |Sì
Audio-Video Interleaved (AVI) (.avi) |Sì
Videocamera digitale MPEG-2 (MOD) (.mod) |Sì
File di flusso di trasporto (TS) DVD (.ts) |Sì
File di oggetto video (VOB) DVD (.vob) |Sì
File di Expression Encoder Screen Capture Codec (.xesc) |Sì
MP4 (MP4, M4A,. m4v) / ISMV (ISMA, con estensione .ismv) |Sì
Flusso di sistema MPEG-1 (.mpeg, .mpg) |Sì
File video MPEG-2 (.m2v) |Sì
Windows Media Video (WMV) (.wmv) |Sì
Audio AC-3 (Dolby Digital) (.ac3)|Sì
Audio Interchange File Format (AIFF) (.aiff)|Sì
Broadcast Wave Format (.bwf)|Sì
MP3 (MPEG-1 Audio Layer 3) (.mp3)|Sì
Audiolibro MPEG-4 (.m4b)|Sì
File WAVE (.wav)|Sì
Windows Media Audio(.wma)|Sì
Adobe® Flash® F4V |No		
MXF/SMPTE 377M |Limitato 
GXF |No		 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|No
Matroska/WebM |No


Sono supportati alcuni formati non compressi. Per altre informazioni, vedere [Formati video non compressi supportati](#uncompressed)

##Codec video di input

Codec video di input|Supportato
---|--- 
H.264 (Baseline, Main e High Profile) |Sì
AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |Solo 4:2:0 a 8 bit
Avid DNxHD (in MXF) |No
DVCPro/DVCProHD (in MXF) |No
JPEG2000 |No
MPEG-2 (Simple e Main Profile e 4:2:2 Profile) |Fino a 4:2:2 Profile
MPEG-1 (incluso MPEG-PS) |Sì
Windows Media Video/VC-1 |Sì
Canopus HQ/HQX |Sì
MPEG-4 v2 (Simple Visual Profile e Advanced Simple Profile) |Sì
[Theora](https://en.wikipedia.org/wiki/Theora) |No
VC-1 (Simple, Main e Advanced Profile) |Sì
Windows Media Video (Simple, Main e Advanced Profile) |Sì
DV (DVC, DVHD, DVSD, DVSL) |Sì
Grass Valley HQ/HQX |Sì
 

##Codec audio di input

Codec audio di input|Supportato
---|---
AES (SMPTE 331M e 302M, AES3-2003) |No
Dolby® E |No
Dolby® Digital (AC3) |Sì
Dolby® Digital Plus (E-AC3) |No
AAC (AAC-LC, HE-AAC v1 con AAC-LC core e HE-AAC v2 con AAC-LC core; fino a 5.1)|Sì
MPEG Layer 2|Sì|Sì|Sì
MP3 (MPEG-1 Audio Layer 3)|Sì
Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional e Windows Media Audio Lossless) |Sì
WAV/PCM|Sì
[FLAC](https://en.wikipedia.org/wiki/FLAC)|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|No


##Formati di file di immagine di input

Formato di file (estensioni di file) | Supportato
---|---
Bitmap (.bmp) | Sì
GIF, GIF animata (.gif)| Sì
JPEG (.jpeg, .jpg)| Sì
PNG (.png)| Sì
TIFF (.tif)| Sì
Canvas WPF XAML (.xaml)| Sì


##Formati e codec di output

La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.

Formato file|Codec video|Codec audio
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (Simple, Main e Advanced Profile)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 e Dolby Digital Plus
Formato di file Smooth Streaming (PIFF 1.1) (*.ismv, *.isma)|VC-1 (profilo avanzato)<p>H. 264 (profili High, Main e Baseline) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Per codec e filtri supportati aggiuntivi in Servizi multimediali, vedere l'articolo relativo ai [filtri di Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formati video non compressi supportati 

Servizi multimediali di Azure fornisce il supporto per l'importazione di dati video non compressi.

Questo è un elenco parziale dei formati non compressi supportati.

Formato video non compresso|Descrizione
---|---
Dati non compressi in formato YVU9 standard|Formato YUV planare. Un campione Y per ciascun pixel, un campione U e V ogni quattro pixel in direzione orizzontale su ogni linea; un campione Y su ciascuna linea verticale e un campione U e V ogni quattro linee verticali. 9 bit per pixel.
Dati in formato YUV 411|Un campione Y per ciascun pixel, un campione U e V ogni quattro pixel in direzione orizzontale su ogni linea; ogni linea verticale campionata. L'ordine dei byte (il più basso per primo) è U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 12 byte corrisponde a 8 pixel immagine.
Dati in formato Y41P|Un campione Y per ciascun pixel, un campione U e V ogni quattro pixel in direzione orizzontale su ogni linea; ogni linea verticale campionata. L'ordine dei byte (il più basso per primo) è U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 12 byte corrisponde a 8 pixel immagine.
Dati in formato YUY2|Uguale al formato UYVY, ma con l'ordine dei pixel differente. L'ordine dei byte (il più basso per primo) è Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 4 byte corrisponde a 2 pixel immagine.
Dati in formato YVYU|Formato YUV compresso. Uguale al formato UYVY, ma con l'ordine dei pixel differente. L'ordine dei byte (il più basso per primo) è Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 4 byte corrisponde a 2 pixel immagine.
Dati in formato UYVY|Formato YUV compresso. Un campione Y per ciascun pixel, un campione U e V ogni due pixel in direzione orizzontale su ogni linea; ogni linea verticale campionata. Più diffuso dei vari formati YUV 4:2:2. L'ordine dei byte (il più basso per primo) è U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 4 byte corrisponde a 2 pixel immagine.
Dati in formato YUV 211|Formato YUV compresso. Un campione Y ogni due pixel, un campione U e V ogni quattro pixel in direzione orizzontale su ogni linea; ogni linea verticale campionata. L'ordine dei byte (il più basso per primo) è Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, dove il suffisso 0 è il pixel più a sinistra e numeri crescenti sono i pixel crescenti da sinistra a destra. Ogni blocco di 4 byte corrisponde a 4 pixel immagine.
Formato Cirrus Logic Jr YUV 411|Formato di Cirrus Logic Jr YUV 411 con meno di 8 bit per campione Y, U e V. Un campione Y per ciascun pixel, un campione U e V ogni quattro pixel in direzione orizzontale su ogni linea; ogni linea verticale campionata.
Formato YVU9 prodotto da Indeo|Formato YVU9 prodotto da Indeo con informazioni aggiuntive sulle differenze rispetto all'ultimo fotogramma. 9,5 bit per pixel, ma rilevati come 9.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->