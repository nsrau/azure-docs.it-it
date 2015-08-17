<properties 
	pageTitle="Formati e codec di Azure Media Encoder" 
	description="Questo argomento fornisce una panoramica dei formati e dei codec del Codificatore multimediale di Azure." 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>


#Formati e codec di Azure Media Encoder

I codificatori comprimono i file multimediali digitali mediante l'uso di codec. In genere nei codificatori sono disponibili diverse impostazioni, che permettono di specificare le proprietà dei file multimediali generati, ad esempio i codec usati, il formato dei file, la risoluzione e la velocità in bit. I formati di file sono contenitori che includono il video compresso, oltre a informazioni sui codec usati per la compressione del video.

I codec consistono in due componenti: un componente per la compressione dei file multimediali digitali per la trasmissione e un altro per la decompressione dei file multimediali digitali per la riproduzione. Sono disponibili codec audio per la compressione e la decompressione di audio e codec video per la compressione e decompressione di video. I codec possono usare la compressione con o senza perdita di dati. I codec senza perdita di dati conservano tutte le informazioni durante la compressione. Quando il file è decompresso, si ottiene un file identico al file multimediale di input. I codec senza perdita di dati sono quindi ideali per l'archiviazione. I codec con perdita di dati perdono alcune informazioni durante la codifica e producono file di dimensioni minori rispetto all'originale, ma con una riduzione della qualità del video. Sono ideali per la trasmissione in streaming su Internet. I due principali codec usati da Azure Media Encoder per la codifica sono h. 264 e VC-1. Altri codec possono essere disponibili nell'ecosistema dei codificatori dei partner.

È importante comprendere la differenza tra codec e formati di file. I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso. Per altre informazioni, vedere il post di blog relativo al [confronto tra le funzionalità di codifica e di creazione pacchetti](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Questo documento contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con Azure Media Encoder.


[Formati di importazione del Codificatore multimediale di Azure](#import_formats)

[Formati di esportazione del Codificatore multimediale di Azure](#export_formats)


##<a id="import_formats"></a>Formati di importazione del Codificatore multimediale di Azure 

La sezione seguente include l'elenco dei codec e dei formati di file supportati per l'importazione.

###Codec video

- H.264 (Baseline, Main e High Profile)
- MPEG-1 (incluso MPEG-PS)
- MPEG-2 (Simple e Main Profile e 4:2:2 Profile)
- MPEG-4 v2 (Simple Visual Profile e Advanced Simple Profile)
- VC-1 (Simple, Main e Advanced Profile)
- Windows Media Video (Simple, Main e Advanced Profile)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###Codec audio

- AC-3 (audio Dolby Digital)
- AAC (AAC-LC, HE-AAC v1 con AAC-LC core e HE-AAC v2 con AAC-LC core)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional e Windows Media Audio Lossless)

###Formati di file video
 
Formato file|Estensioni file
---|---
3GPP, 3GPP2|.3gp, .3g2, .3gp2
Advanced Systems Format (ASF)|.asf
Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream]|.mts, .m2ts
Audio-Video Interleaved (AVI)|.avi
Videocamera digitale MPEG-2 (MOD)|.mod
File di flusso di trasporto (TS) DVD|.ts
File di oggetto video (VOB) DVD|.vob
File di Expression Encoder Screen Capture Codec|.xesc
MP4|.mp4
Flusso di sistema MPEG-1|.mpeg, .mpg
File video MPEG-2|.m2v
Formato di file Smooth Streaming (PIFF 1.3)|.ismv
Windows Media Video (WMV)|.wmv


Sono supportati alcuni formati non compressi. Per altre informazioni, vedere [Formati video non compressi supportati](#uncompressed)

###Formati di file audio

Formato file|Estensioni file
---|---
Audio AC-3 (Dolby Digital)|.ac3
Audio Interchange File Format (AIFF)|.aiff
Broadcast Wave Format|.bwf
MP3 (MPEG-1 Audio Layer 3)|.mp3
Audio MP4|.m4A
Audiolibro MPEG-4|.m4b
File WAVE|.wav
Windows Media Audio|.wma

###Formati di file di immagine

Formato file|Estensioni file
---|---
Bitmap|.bmp
GIF, GIF animata|.gif
JPEG|.jpeg, .jpg
PNG|.png
TIFF|.tif
Canvas WPF XAML|.xaml


##<a id="export_formats"></a>Formati di esportazione del Codificatore multimediale di Azure

La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.


Formato file|Codec video|Codec audio
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (Simple, Main e Advanced Profile)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (High, Main e Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 e Dolby Digital Plus
Formato di file Smooth Streaming (PIFF 1.1) (*.ismv, *.isma)|VC-1 (profilo avanzato)<p>h. 264 (alta, Main e Baseline Profile) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Per codec e filtri aggiuntivi in Servizi multimediali, vedere l'articolo relativo ai [filtri di Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

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

<!---HONumber=August15_HO6-->