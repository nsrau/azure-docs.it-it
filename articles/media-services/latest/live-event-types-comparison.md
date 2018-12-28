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
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 2dbc2b4e316155e880d3baa900196d5cdd31be72
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011777"
---
# <a name="liveevent-types-comparison"></a>Confronto tra tipi di LiveEvent

In Servizi multimediali di Azure, un [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: codifica live e pass-through. 

## <a name="types-comparison"></a>Confronto tra tipi 

La tabella seguente mette a confronto le funzionalità dei due tipi di LiveEvent.

| Funzionalità | LiveEvent pass-through | LiveEvent standard (Basic) |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No  |Yes |
| Risoluzione video massima per feed di contributo |4K (4096 x 2160 a 60 fotogrammi/sec) |1080p (1920 x 1088 a 30 fotogrammi/sec)|
| Livelli massimi consigliati per feed di contributo|Fino a 12|Un audio|
| Livelli massimi nell'output| Uguale all'input|Fino a 7|
| Larghezza di banda aggregata massima per feed di contributo|60 Mbps|N/D|
| Velocità in bit massima per un singolo livello all'interno del contributo |20 Mbps|20 Mbps|
| Supporto per tracce audio in più lingue|Yes|No |
| Codec video di input supportati |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codec video di output supportati|Uguale all'input|H.264/AVC|
| Profondità bit video di input e output supportata|Fino a 10 bit incluso HDR 10/HLG|8 bit|
| Codec audio di input supportati|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codec audio di output supportati|Uguale all'input|AAC-LC|
| Risoluzione video massima del video di output|Uguale all'input|720p (a 30 fotogrammi/secondo)|
| Protocolli di input|RTMP, MP4 frammentato (Smooth Streaming)|RTMP, MP4 frammentato (Smooth Streaming)|
| Prezzo|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live"|
| Tempo di esecuzione massimo|24 x 365 live lineare|24 x 7|
| Possibilità di trasferire dati dei sottotitoli CEA 608/708 integrati|Yes|Yes|
| Supporto per l'inserimento di slate|No |No |
| Supporto per annunci pubblicitari tramite API| No |No |
| Supporto per annunci pubblicitari tramite messaggi in banda SCTE-35|Yes|Yes|
| Possibilità di recuperare brevi fasi di stallo in feed di contributo|Yes|No (senza dati di input, il LiveEvent avvierà lo slate dopo 6 secondi)|
| Supporto per GOP di input non uniformi|Yes|No - La durate GOP dell’input deve essere fissa|
| Supporto per input con frequenza dei fotogrammi variabile|Yes|No: l'input deve essere una frequenza di fotogrammi fissa. Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Il feed di contributo non può però diminuire la frequenza dei fotogrammi (ad esempio a 15 fotogrammi/sec).|
| Arresto automatico del LiveEvent in caso di perdita del feed di input|No |Dopo 12 ore, se nessun LiveOutput è in esecuzione|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dello streaming live](live-streaming-overview.md)
