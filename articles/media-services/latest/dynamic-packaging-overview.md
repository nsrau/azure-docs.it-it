---
title: Panoramica della creazione dinamica dei pacchetti di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento offre una panoramica della creazione dinamica dei pacchetti in Servizi multimediali.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447923"
---
# <a name="dynamic-packaging"></a>creazione dinamica dei pacchetti

Servizi multimediali di Microsoft Azure può essere usato per distribuire molti formati di file di origine multimediali, formati di streaming multimediali e formati di protezione del contenuto a un'ampia gamma di tecnologie client, ad esempio iOS e Xbox. Questi client supportano tuttavia protocolli diversi. iOS, ad esempio, richiede un formato HTTP Live Streaming (HLS), mentre Xbox richiede Smooth Streaming. Se è presente un set di file MP4 a velocità in bit adattiva, ovvero più velocità in bit, (ISO Base Media 14496-12) o di un set di file Smooth Streaming a velocità in bit adattiva e si vuole renderli disponibili per i client che supportano contenuto HLS, MPEG DASH o Smooth Streaming, è possibile usare la funzionalità di creazione dinamica dei pacchetti di Servizi multimediali.

Con la creazione dinamica dei pacchetti è sufficiente creare un asset che contenga un set di file MP4 a velocità in bit adattiva. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantirà che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

Il diagramma seguente mostra il flusso di lavoro tradizionale di codifica e creazione statica dei pacchetti.

![Codifica statica](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Il diagramma seguente mostra il flusso di lavoro di creazione dinamica dei pacchetti.

![Codifica dinamica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>Flusso di lavoro per la creazione dinamica di pacchetti

1. Caricare un file di input (detto file in formato intermedio). Ad esempio, H.264, MP4 o WMV (per l'elenco dei formati supportati, vedere [Formati e codec Media Encoder Standard](media-encoder-standard-formats.md)).
2. Codificare il file in formato intermedio in set MP4 a velocità in bit adattiva H.264.
3. Pubblicare l'asset contenente il set MP4 a bitrate adattivo.
4. Creare gli URL di streaming per accedere e al contenuto e trasmetterlo in streaming.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codec audio supportati dalla creazione dinamica dei pacchetti

La creazione dinamica dei pacchetti supporta i file MP4 contenenti audio codificato con [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 o E-AC3) o [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless).

> [!NOTE]
> La creazione dinamica dei pacchetti non supporta file contenenti audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) perché si tratta di un codec legacy codec.

## <a name="next-steps"></a>Passaggi successivi

[Caricare, codificare ed eseguire lo streaming dei video](stream-files-tutorial-with-api.md)
