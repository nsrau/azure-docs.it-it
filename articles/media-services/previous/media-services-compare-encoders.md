---
title: Confronto tra i codificatori multimediali su richiesta di Azure | Documentazione Microsoft
description: Questo argomento mette a confronto le funzionalità di codifica di **Media Encoder Standard** e di **Flusso di lavoro Premium del codificatore multimediale**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465712"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Confronto tra i codificatori multimediali su richiesta di Azure  

Questo argomento mette a confronto le funzionalità di codifica di **Media Encoder Standard** e di **Flusso di lavoro Premium del codificatore multimediale**.

## <a name="video-and-audio-processing-capabilities"></a>Funzionalità di elaborazione audio e video

La tabella seguente mette a confronto le funzionalità di Media Encoder Standard (MES) e Media Encoder Premium Workflow (MEPW). 

|Funzionalità|Codificatore multimediale standard|Flusso di lavoro Premium del codificatore multimediale|
|---|---|---|
|Applicazione della logica condizionale durante la codifica<br/>(ad esempio, se l'input è HD viene usata la codifica audio 5.1)|No|Yes|
|Sottotitoli|No|[Sì](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> con Dialogue Intelligence™|No|Yes|
|Deinterlacciamento, telecine inverso|Basic|Qualità broadcast|
|Rilevamento e rimozione dei bordi neri <br/>(formati 4:3, formati 16:9)|No|Yes|
|Generazione di anteprime|[Sì](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sì](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Ritaglio/rifilatura e pinzatura di video|[Sì](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Sovrapposizioni di audio o video|[Sì](media-services-advanced-encoding-with-mes.md#overlay)|[Sì](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sovrapposizioni di elementi grafici|Da origini di immagini|Da origini di immagini e testo|
|Tracce audio in più lingue|Limitato|[Sì](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Metro di fatturazione usato da ogni codificatore
| Nome del processore multimediale | Prezzi applicabili | Note |
| --- | --- | --- |
| **Codificatore multimediale standard** |CODIFICATORE |Le attività di codifica verranno addebitate le attività in base alla durata totale, espressa in minuti, di tutti i file multimediali generati come output, alla tariffa specificata [qui][1], nella colonna CODIFICATORE. |
| **Flusso di lavoro Premium del codificatore multimediale** |CODIFICATORE PREMIUM |Le attività di codifica verranno addebitate le attività in base alla durata totale, espressa in minuti, di tutti i file multimediali generati come output, alla tariffa specificata [qui][1], nella colonna CODIFICATORE PREMIUM. |

## <a name="input-containerfile-formats"></a>Contenitore di input/formati di file
| Contenitore di input/formati di file | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| Adobe® Flash® F4V |Yes |Yes |
| MXF/SMPTE 377M |Yes |Yes |
| GXF |Yes |Yes |
| MPEG-2 Transport Stream |Yes |Yes |
| MPEG-2 Program Stream |Yes |Yes |
| MPEG-4/MP4 |Yes |Yes |
| Windows Media/ASF |Yes |Yes |
| AVI (non compresso 8 bit/10 bit) |Yes |Yes |
| 3GPP/3GPP2 |Yes |No |
| Formato di file Smooth Streaming (PIFF 1.3) |Yes |No |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |No |
| Matroska/WebM |Yes |No |
| QuickTime (.mov) |Yes |No |

## <a name="input-video-codecs"></a>Codec video di input
| Codec video di input | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit |Yes |
| Avid DNxHD (in MXF) |Yes |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |Yes |
| JPEG2000 |Yes |Yes |
| MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Fino a 422 Profile |Yes |
| MPEG-1 |Yes |Yes |
| Windows Media Video/VC-1 |Yes |Yes |
| Canopus HQ/HQX |No |No |
| MPEG-4 parte 2 |Yes |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |No |
| Apple ProRes 422 |Yes |No |
| Apple ProRes 422 LT |Yes |No |
| Apple ProRes 422 HQ |Yes |No |
| Apple ProRes Proxy |Yes |No |
| Apple ProRes 4444 |Yes |No |
| Apple ProRes 4444 XQ |Yes |No |
| HEVC/H.265|Main Profile|Main Profile e Main 10 Profile|

## <a name="input-audio-codecs"></a>Codec audio di input
| Codec audio di input | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |No |Yes |
| Dolby® E |No |Yes |
| Dolby® Digital (AC3) |No |Yes |
| Dolby® Digital Plus (E-AC3) |No |Yes |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1) |Yes |Yes |
| MPEG Layer 2 |Yes |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |Yes |
| Windows Media Audio |Yes |Yes |
| WAV/PCM |Yes |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |No |

## <a name="output-containerfile-formats"></a>Contenitore di output/formati di file
| Contenitore di output/formati di file | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| Adobe® Flash® F4V |No |Yes |
| MXF (OP1a, XDCAM e AS02) |No |Yes |
| DPP (incluso AS11) |No |Yes |
| GXF |No |Yes |
| MPEG-4/MP4 |Yes |Yes |
| MPEG-TS |Yes |Yes |
| Windows Media/ASF |No |Yes |
| AVI (non compresso 8 bit/10 bit) |No |Yes |
| Formato di file Smooth Streaming (PIFF 1.3) |No |Yes |

## <a name="output-video-codecs"></a>Codec video di output
| Codec video di output | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| AVC (H.264; 8 bit; fino a High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Solo 4:2:0 a 8 bit |Yes |
| HEVC (H.265; 8 bit e 10 bit;)  |No |Yes |
| Avid DNxHD (in MXF) |No |Yes |
| MPEG-2 (fino a 4:2:2 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |No |Yes |
| MPEG-1 |No |Yes |
| Windows Media Video/VC-1 |No |Yes |
| Creazione anteprime JPEG |Yes |Yes |
| Creazione di anteprime PNG |Yes |Yes |
| Creazione di anteprime BMP |Yes |No |

## <a name="output-audio-codecs"></a>Codec audio di output
| Codec audio di output | Codificatore multimediale standard | Flusso di lavoro Premium del codificatore multimediale |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |No |Yes |
| Dolby® Digital (AC3) |No |Yes |
| Dolby® Digital Plus (E-AC3) fino a 7.1 |No |Yes |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1) |Yes |Yes |
| MPEG Layer 2 |No |Yes |
| MP3 (MPEG-1 Audio Layer 3) |No |Yes |
| Windows Media Audio |No |Yes |

>[!NOTE]
>Se si codifica per Dolby® Digital (AC3), l'output può essere scritto solo in un file ISO MP4.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
* [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quote e limitazioni](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
