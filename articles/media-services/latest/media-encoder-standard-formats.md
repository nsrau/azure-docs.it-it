---
title: Formati e codec standard del codificatore-Azure
description: Questo articolo contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888071"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formati e codec standard del codificatore

Questo articolo contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Per informazioni su come creare set di impostazioni personalizzati usando **StandardEncoderPreset**, vedere [Creare una trasformazione con un set di impostazioni personalizzato](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Contenitore di input/formati di file

| Formato di file (estensioni di file) | Supportato |
| --- | --- |
| FLV (con codec H. 264 e AAC) (.flv) |SÌ |
| MXF    (.mxf) |SÌ |
| GXF    (.gxf) |SÌ |
| MPEG2 PS, MPEG2-TS, 3GP (TS, PS, 3GP, .3gpp, mpg) |SÌ |
| Windows Media Video (WMV) (.wmv) |SÌ |
| AVI (non compresso 8 bit/10 bit) (.avi) |SÌ |
| MP4 (MP4, M4A,. m4v) / ISMV (ISMA, con estensione .ismv) |SÌ |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |SÌ |
| Matroska/WebM (.mkv) |SÌ |
| WAVE/WAV (.wav) |SÌ |
| QuickTime (.mov) |SÌ |

### <a name="audio-formats-in-input-containers"></a>Formati audio nei contenitori di input

Il codificatore standard supporta il trasporto dei seguenti formati audio nei contenitori di input:

* file MXF, GXF e QuickTime che dispongono di tracce audio con esempi di stereo interleaved o 5.1

Oppure

* File MXF, GXF e QuickTime in cui l'audio viene trasportato in tracce PCM separate, ma il mapping del canale (in stereo o 5.1) può essere dedotto dai metadati del file

## <a name="input-video-codecs"></a>Codec video di input
| Codec video di input | Supportato |
| --- | --- |
| AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit |
| Avid DNxHD (in MXF) |SÌ |
| DVCPro/DVCProHD (in MXF) |SÌ |
| Video digitale (DV) (in file AVI) |SÌ |
| JPEG 2000 |SÌ |
| MPEG-2 (fino a 422 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Fino a 422 Profile |
| MPEG-1 |SÌ |
| FORMATO VC-1/WMV9 |SÌ |
| Canopus HQ/HQX |No |
| MPEG-4 parte 2 |SÌ |
| [Theora](https://en.wikipedia.org/wiki/Theora) |SÌ |
| YUV420 non compressi o mezzanine |SÌ |
| Apple ProRes 422 |SÌ |
| Apple ProRes 422 LT |SÌ |
| Apple ProRes 422 HQ |SÌ |
| Apple ProRes Proxy |SÌ |
| Apple ProRes 4444 |SÌ |
| Apple ProRes 4444 XQ |SÌ |
| HEVC/H.265| Main Profile|

## <a name="input-audio-codecs"></a>Codec audio di input
| Codec audio di input | Supportato |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1) |SÌ |
| MPEG Layer 2 |SÌ |
| MP3 (MPEG-1 Audio Layer 3) |SÌ |
| Windows Media Audio |SÌ |
| WAV/PCM |SÌ |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |SÌ |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |SÌ |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |SÌ |
| AMR (velocità multipla adattiva) |SÌ |
| AES (SMPTE 331M e 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Formati e codec di output
La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.

| Formato file | Codec video | Codec audio |
| --- | --- | --- |
| MP4 <br/><br/>(multi-bitrate MP4 container inclusi) |H.264 (High, Main e Baseline Profile) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (High, Main e Baseline Profile) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Passaggi successivi

[Creare una trasformazione con un set di impostazioni personalizzato](customize-encoder-presets-how-to.md)
