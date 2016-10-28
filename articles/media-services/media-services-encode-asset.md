<properties 
	pageTitle="Panoramica e confronto dei codificatori multimediali su richiesta di Azure | Microsoft Azure" 
	description="Questo argomento offre una panoramica e un confronto tra i codificatori multimediali su richiesta di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/> 

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="juliako"/> 

#Panoramica e confronto dei codificatori multimediali su richiesta di Azure

##Panoramica della codifica

Servizi multimediali di Azure offre diverse opzioni per la codifica di servizi multimediali nel cloud.

Quando si iniziano ad utilizzare i servizi multimediali, è importante comprendere la differenza tra codec e formati di file. I codec sono costituiti da software che implementa gli algoritmi di compressione/decompressione, mentre i formati di file sono contenitori che includono il video compresso.

Servizi multimediali fornisce il servizio di creazione dinamica dei pacchetti, che consente di distribuire i contenuti codificati in formato MP4 o Smooth Streaming con velocità in bit adattiva in formati di streaming supportati da Servizi multimediali (MPEG DASH, HLS, Smooth Streaming, HDS), senza dover ricreare i pacchetti con questi formati di streaming.

Per sfruttare i vantaggi del servizio di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md), è necessario seguire questa procedura:

- Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti più avanti in questa esercitazione).
- Ottenere almeno un'unità di streaming on demand per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere l'articolo sulla [procedura per scalare unità riservate di streaming on demand](media-services-portal-manage-streaming-endpoints.md).

Servizi multimediali supporta i seguenti codificatori su richiesta descritti in questo articolo:

- [Codificatore multimediale standard](media-services-encode-asset.md#media-encoder-standard)
- [Flusso di lavoro Premium del codificatore multimediale](media-services-encode-asset.md#media-encoder-premium-workflow)

In questo articolo è fornita una breve panoramica dei codificatori multimediali su richiesta e sono presenti collegamenti ad articoli che contengono informazioni più dettagliate. L'argomento fornisce inoltre il confronto dei codificatori.

Si noti che per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni, vedere [Scalabilità dell’unità di codifica](media-services-scale-media-processing-overview.md).

##Codificatore multimediale standard

###Utilizzo

[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formati

[Codec e formati](media-services-media-encoder-standard-formats.md)

###Impostazioni predefinite

Media Encoder Standard viene configurato mediante un set di impostazioni descritto [qui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadati di input e output

I metadati di input dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

I metadati di output dei codificatori sono descritti [qui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Generare anteprime

Per informazioni, vedere l'argomento [Come generare anteprime utilizzando Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###Tagliare video (ritaglio)

Per informazioni, vedere [Come tagliare video usando Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###Creare sovrimpressioni

Per informazioni, vedere [Come creare sovrimpressioni usando Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###Vedere anche

[Blog di Servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Flusso di lavoro Premium del codificatore multimediale

###Overview

[Introduzione alla codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###Utilizzo

Il flusso di lavoro Premium del codificatore multimediale viene configurato usando flussi di lavoro complessi. Per creare e aggiornare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md).

[Come usare la codifica Premium in Servizi multimediali di Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###Problemi noti

Se il video di input non contiene i sottotitoli codificati, l'asset di output conterrà comunque un file TTML vuoto.


##<a id="compare_encoders"></a>Confronto tra i codificatori

###<a id="billing"></a>Metro di fatturazione usato da ogni codificatore

Nome del processore multimediale|Prezzi applicabili|Note
---|---|---
**Codificatore multimediale standard** |CODIFICATORE|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE.
**Flusso di lavoro Premium del codificatore multimediale** |CODIFICATORE PREMIUM|Le attività di codifica verranno addebitate in base alla dimensione dell'asset di output, in gigabyte, alla tariffa specificata [qui][1], nella colonna CODIFICATORE PREMIUM.


Questa sezione mette a confronto le funzionalità di codifica di **Media Encoder Standard** e di **Flusso di lavoro Premium del codificatore multimediale**.


###Contenitore di input/formati di file

Contenitore di input/formati di file|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
Adobe® Flash® F4V |Sì|Sì
MXF/SMPTE 377M |Sì|Sì
GXF |Sì|Sì
MPEG-2 Transport Stream |Sì|Sì
MPEG-2 Program Stream |Sì|Sì
MPEG-4/MP4 |Sì|Sì
Windows Media/ASF |Sì|Sì
AVI (non compresso 8 bit/10 bit)|Sì|Sì
3GPP/3GPP2 |Sì|No
Formato di file Smooth Streaming (PIFF 1.3)|Sì|No
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sì|No
Matroska/WebM |Sì|No
QuickTime (.mov) |Sì|No

###Codec video di input

Codec video di input|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit|Sì
Avid DNxHD (in MXF) |Sì|Sì
DVCPro/DVCProHD (in MXF) |Sì|Sì
JPEG2000 |Sì|Sì
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Fino a 422 Profile|Sì
MPEG-1 |Sì|Sì
Windows Media Video/VC-1 |Sì|Sì
Canopus HQ/HQX |No|No
MPEG-4 parte 2 |Sì|No
[Theora](https://en.wikipedia.org/wiki/Theora) |Sì|No
Apple ProRes 422 |Sì|No
Apple ProRes 422 LT |Sì|No
Apple ProRes 422 HQ |Sì|No
Apple ProRes Proxy|Sì|No
Apple ProRes 4444 |Sì|No
Apple ProRes 4444 XQ |Sì|No

###Codec audio di input

Codec audio di input|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
AES (SMPTE 331M e 302M, AES3-2003) |No|Sì
Dolby® E |No|Sì
Dolby® Digital (AC3) |No|Sì
Dolby® Digital Plus (E-AC3) |No|Sì
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì
MPEG Layer 2|Sì|Sì
MP3 (MPEG-1 Audio Layer 3)|Sì|Sì
Windows Media Audio|Sì|Sì
WAV/PCM|Sì|Sì
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sì|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sì|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sì|No


###Contenitore di output/formati di file

Contenitore di output/formati di file|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
Adobe® Flash® F4V|No|Sì
MXF (OP1a, XDCAM e AS02)|No|Sì
DPP (incluso AS11)|No|Sì
GXF|No|Sì
MPEG-4/MP4|Sì|Sì
MPEG-TS|Sì|Sì
Windows Media/ASF|No|Sì
AVI (non compresso 8 bit/10 bit)|No|Sì
Formato di file Smooth Streaming (PIFF 1.3)|No|Sì

###Codec video di output

Codec video di output|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Solo 4:2:0 a 8 bit|Sì
Avid DNxHD (in MXF)|No|Sì
DVCPro/DVCProHD (in MXF)|No|Sì
MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|No|Sì
MPEG-1|No|Sì
Windows Media Video/VC-1|No|Sì
Creazione anteprime JPEG|No|Sì

###Codec audio di output

Codec audio di output|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale
---|---|---
AES (SMPTE 331M e 302M, AES3-2003)|No|Sì
Dolby® Digital (AC3)|No|Sì
Dolby® Digital Plus (E-AC3) fino a 7.1|No|Sì
AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)|Sì|Sì
MPEG Layer 2|No|Sì
MP3 (MPEG-1 Audio Layer 3)|No|Sì
Windows Media Audio|No|Sì


##Codici di errore  

Nella tabella seguente sono elencati i codici di errore che potrebbero essere restituiti quando si verifica un errore durante l'esecuzione di attività di codifica. Per ottenere i dettagli dell'errore nel codice .NET, usare la classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx). Per ottenere i dettagli dell'errore nel codice REST, usare l'API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx).

ErrorDetail.Code|Le possibili cause dell'errore
-----|-----------------------
Sconosciuto| Errore sconosciuto durante l'esecuzione dell'attività
ErrorDownloadingInputAssetMalformedContent|Categoria di errori relativa agli errori durante il download di asset di input, ad esempio nomi di file non validi, file di lunghezza zero, formati errati e così via.
ErrorDownloadingInputAssetServiceFailure|Categoria di errori relativa a problemi sul lato del servizio, ad esempio errori di rete o archiviazione durante il download.
ErrorParsingConfiguration|Categoria di errori in cui l'attività <see cref="MediaTask.PrivateData"/> (configurazione) non è valida, ad esempio la configurazione non è un set di impostazioni di sistema valido o contiene XML non valido.
ErrorExecutingTaskMalformedContent|Categoria di errori durante l'esecuzione dell'attività in cui i problemi nei file multimediali di input causano un errore.
ErrorExecutingTaskUnsupportedFormat|Categoria di errori in cui il processore di contenuti multimediali non è in grado di elaborare i file forniti: formato di file multimediale non supportato o non corrispondente alla configurazione. Ad esempio, si tenta di produrre un output solo audio da un asset che ha il solo video
ErrorProcessingTask|Categoria di altri errori che il processore di contenuti multimediali rileva durante l'elaborazione dell'attività non correlati al contenuto.
ErrorUploadingOutputAsset|Categoria di errori durante il caricamento di asset di output
ErrorCancelingTask|Categoria di errori per coprire gli errori durante il tentativo di annullare l'attività
TransientError|Categoria di errori per coprire i problemi transitori (ad esempio, problemi di rete temporanei con Archiviazione di Azure)


Per ottenere assistenza dal team di **Servizi multimediali**, aprire un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Articoli correlati

- [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
- [Quote e limitazioni](media-services-quotas-and-limitations.md)

 
<!--Reference links in article--> 
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0921_2016-->