---
title: Tipi di LiveEvent Servizi multimediali di Azure | Microsoft Docs
description: In questo articolo è inclusa una tabella dettagliata per confrontare i tipi di LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075014"
---
# <a name="live-event-types-comparison"></a>Confronto tra tipi di eventi live

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: codifica live e pass-through. 

## <a name="types-comparison"></a>Confronto tra tipi 

La tabella seguente confronta le funzionalità dei tipi di eventi in tempo reale.

| Funzionalità | Evento live pass-through | Standard o un evento Live Premium1080p |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No |Yes |
| Risoluzione video massima per feed di contributo |4K (4096 x 2160 a 60 fotogrammi/sec) |1080p (1920 x 1088 a 30 fotogrammi/sec)|
| Livelli massimi consigliati per feed di contributo|Fino a 12|Un audio|
| Livelli massimi nell'output| Uguale all'input|Fino a 6 (vedere il set di impostazioni di sistema riportata di seguito)|
| Larghezza di banda aggregata massima per feed di contributo|60 Mbps|N/D|
| Velocità in bit massima per un singolo livello all'interno del contributo |20 Mbps|20 Mbps|
| Supporto per tracce audio in più lingue|Yes|No|
| Codec video di input supportati |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codec video di output supportati|Uguale all'input|H.264/AVC|
| Profondità bit video di input e output supportata|Fino a 10 bit incluso HDR 10/HLG|8 bit|
| Codec audio di input supportati|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codec audio di output supportati|Uguale all'input|AAC-LC|
| Risoluzione video massima del video di output|Uguale all'input|Standard - 720p, Premium1080p - 1080p|
| Frequenza massima di frame del video di input|60 fotogrammi al secondo|Standard o Premium1080p - 30 fotogrammi al secondo|
| Protocolli di input|RTMP, MP4 frammentato (Smooth Streaming)|RTMP, MP4 frammentato (Smooth Streaming)|
| Prezzo|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|
| Tempo di esecuzione massimo| 24 ore su 24, 365 giorni all'anno, live linear | Fino a 24 ore|
| Possibilità di trasferire dati dei sottotitoli CEA 608/708 integrati|Yes|Yes|
| Supporto per l'inserimento di slate|No|No|
| Supporto per annunci pubblicitari tramite API| No|No|
| Supporto per annunci pubblicitari tramite messaggi in banda SCTE-35|Yes|Yes|
| Possibilità di recuperare brevi fasi di stallo in feed di contributo|Yes|Parziale|
| Supporto per GOP di input non uniformi|Yes|No - La durate GOP dell’input deve essere fissa|
| Supporto per input con frequenza dei fotogrammi variabile|Yes|No: l'input deve essere una frequenza di fotogrammi fissa. Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Ma il feed di contributo non è possibile eliminare la frequenza dei fotogrammi (ad esempio a 15 fotogrammi al secondo).|
| Arresto automatico dell'evento live in caso di perdita del feed di input|No|Dopo 12 ore, se nessun LiveOutput è in esecuzione|

## <a name="system-presets"></a>Set di impostazioni di sistema

Le risoluzioni e velocità in bit contenuti nell'output dal codificatore live è determinato dal [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se si usa un' **Standard** live encoder (LiveEventEncodingType.Standard), il *Default720p* set di impostazioni specifica un set di coppie di velocità risoluzione/bit 6 descritti di seguito. In caso contrario, se si usa un' **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), il *Default1080p* specifica il set di impostazioni set di coppie di risoluzione/bit di output. 

### <a name="output-video-streams-for-default720p"></a>Flussi Video di output per Default720p

**Default720p** codificheranno il video di input in 6 livelli seguenti.

| Velocità in bit | Larghezza | Altezza: | MaxFPS | Profilo | Nome del flusso di output |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alto |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alto |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alto |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alto |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alto |Video_340x192_200kbps |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 720p e un massimo di 6 livelli. Specificare anche che si sta richiedendo un set di impostazioni per un codificatore live Standard.

### <a name="output-video-streams-for-default1080p"></a>Flussi Video di output per Default1080p

**Default1080p** codificheranno il video di input in 6 livelli seguenti.

| Velocità in bit | Larghezza | Altezza: | MaxFPS | Profilo | Nome del flusso di output |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Alto |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Alto |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Alto |Video_640x360_800kbps |
| 400 |480 |270 |30 |Alto |Video_480x270_400kbps |
| 200 |320 |180 |30 |Alto |Video_320x180_200kbps |

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo a 1080p e al massimo 6 livelli. Specificare anche che si sta richiedendo un set di impostazioni per un codificatore live Premium1080p.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Output Audio Stream per Default720p e Default1080p

Per entrambe *Default720p* e *Default1080p* predefiniti, audio viene codificato a stereo AAC-LC a 128 kbps, frequenza di 48 kHz di campionamento.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dello streaming live](live-streaming-overview.md)
