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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codifica di contenuti su richiesta con Servizi multimediali di Azure

Questo argomento fa parte della serie [Flusso di lavoro Video On Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).

##Panoramica

Servizi multimediali supporta i seguenti codificatori:

- [Azure Media Encoder](#azure_media_encoder)
- [Flusso di lavoro Premium del codificatore multimediale](#media_encoder_premium_workflow) (anteprima pubblica)

La [seguente sezione](#compare_encoders) mette a confronto le funzionalità di codifica dei due codificatori.

Per impostazione predefinita, in ciascun account di Servizi multimediali può essere attiva una sola attività di codifica alla volta. È tuttavia possibile riservare unità di codifica che consentano di eseguire più attività di codifica contemporaneamente, una per ciascuna unità acquistata. Per informazioni sulla scalabilità delle unità di codifica, vedere i seguenti argomenti del **portale** e di **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Codificatore multimediale di Azure

[Formati supportati dal codificatore di Servizi multimediali](media-services-azure-media-encoder-formats.md): illustra i formati di file e flussi supportati dal **Codificatore multimediale di Azure**.

Il **Codificatore multimediale di Azure** viene configurato mediante una delle stringhe del set di impostazioni descritte [qui](https://msdn.microsoft.com/library/azure/dn619392.aspx). In alternativa, è possibile ottenere i file del set di impostazioni del Codificatore multimediale di Azure [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Codificare con **Azure Media Encoder** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Altri argomenti correlati

[Creazione dinamica dei pacchetti](https://msdn.microsoft.com/library/azure/jj889436.aspx): descrive la procedure per eseguire la codifica in MP4 a velocità in bit adattiva e distribuire in modo dinamico file Smooth Streaming, Apple HLS o MPEG-DASH.

[Controllo dei nomi file di output del codificatore di Servizi multimediali](https://msdn.microsoft.com/library/azure/dn303341.aspx): descrive le convenzioni di denominazione di file usate dal Codificatore multimediale di Azure e la procedura per modificare i nomi di file di output.

[Codifica di file multimediali con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): descrive la procedura per codificare tracce audio usando la codifica Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Flusso di lavoro Premium del codificatore multimediale (anteprima pubblica)

**Nota** Il processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale descritto in questo argomento non è disponibile in Cina.

[Formati supportati dal flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md): vengono illustrati formati file e codec supportati dal **flusso di lavoro Premium del codificatore multimediale**.

Il **flusso di lavoro Premium del codificatore multimediale** viene configurato usando flussi di lavoro complessi. Per creare i file di un flusso di lavoro, è possibile usare lo strumento [Progettazione flussi di lavoro](media-services-workflow-designer.md).

È possibile ottenere i file del flusso di lavoro predefiniti [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Nella cartella è presente anche una descrizione dei file.

Codificare con il **flusso di lavoro Premium del codificatore multimediale** usando **.NET**. Per altre informazioni, vedere [Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Confronto tra i codificatori

Questa sezione mette a confronto le funzionalità di codifica del **Codificatore multimediale di Azure** e del **flusso di lavoro Premium del codificatore multimediale**.

###Formati di input

Contenitore di input/formati di file

<table border="1">
<tr><th>Contenitore di input/formati di file</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sì</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Sì</td><td>Limitato</td></tr>
<tr><td>GXF</td><td>Sì</td><td>No</td></tr>
<tr><td>MPEG-2 Transport Stream</td><td>Sì</td><td>Sì</td></tr>
<tr><td>MPEG-2 Program Stream</td><td>Sì</td><td>Sì</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Windows Media/ASF</td><td>Sì</td><td>Sì</td></tr>
<tr><td>AVI (non compresso 8 bit/10 bit)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Sì</td></tr>
<tr><td>Formato di file Smooth Streaming (PIFF 1.3)</td><td>No</td><td>Sì</td></tr>
</table>

Codec video di input

<table border="1">
<tr><th>Codec video di input</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra</td><td>Sì</td><td>Solo 4:2:0 a 8 bit</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Sì</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Sì</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Sì</td><td>No</td></tr>
<tr><td>MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)</td><td>Sì</td><td>Fino a 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Sì</td></tr>
</table>

Codec audio di input

<table border="1">
<tr><th>Codec audio di input</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M e 302M, AES3-2003)</td><td>Sì</td><td>No</td></tr>
<tr><td>Dolby® E</td><td>Sì</td><td>No</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Sì</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>MPEG Layer 2</td><td>Sì</td><td>Sì</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Windows Media Audio</td><td>Sì</td><td>Sì</td></tr>
<tr><td>WAV/PCM</td><td>Sì</td><td>Sì</td></tr>
</table>

###Formati di output

Contenitore di output/formati di file

<table border="1">
<tr><th>Contenitore di output/formati di file</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sì</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM e AS02)</td><td>Sì</td><td>No</td></tr>
<tr><td>DPP (incluso AS11)</td><td>Sì</td><td>No</td></tr>
<tr><td>GXF</td><td>Sì</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Windows Media/ASF</td><td>Sì</td><td>Sì</td></tr>
<tr><td>AVI (non compresso 8 bit/10 bit)</td><td>Sì</td><td>No</td></tr>
<tr><td>Formato di file Smooth Streaming (PIFF 1.3)</td><td>Sì</td><td>Sì</td></tr>
</table>

Codec video di output

<table border="1">
<tr><th>Codec video di output</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Sì</td><td>Solo 4:2:0 a 8 bit fino a 1080 p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Sì</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Sì</td><td>No</td></tr>
<tr><td>MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)</td><td>Sì</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Sì</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Creazione anteprime JPEG</td><td>Sì</td><td>Sì</td></tr>
</table>

Codec audio di output

<table border="1">
<tr><th>Codec audio di output</th><th>Flusso di lavoro Premium del codificatore multimediale</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M e 302M, AES3-2003)</td><td>Sì</td><td>No</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) fino a 7.1</td><td>Sì</td><td>Fino a 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1)</td><td>Sì</td><td>Sì</td></tr>
<tr><td>MPEG Layer 2</td><td>Sì</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sì</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Sì</td><td>Sì</td></tr>
</table>
##Articoli correlati

- [Introduzione alla codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Come usare la codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Quote e limitazioni](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->