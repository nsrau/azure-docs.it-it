---
title: Architetture di servizi multimediali
titleSuffix: Azure Media Services
description: Questo articolo descrive le architetture per servizi multimediali.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: c14dbe4253696e01e99111a24319e455c1d551e9
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533996"
---
# <a name="media-services-architectures"></a>Architetture di servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Servizi multimediali digitali in streaming live

Una soluzione di streaming live ti permette di acquisire video in tempo reale e di trasmetterli ai consumatori in tempo reale, ad esempio interviste di streaming, conferenze e eventi sportivi online. In questa soluzione, i video vengono acquisiti da una videocamera e inviati a un endpoint di input del canale. Il canale riceve il flusso di input Live e lo rende disponibile per lo streaming tramite un endpoint di streaming a un Web browser o a un'app per dispositivi mobili. Il canale fornisce anche un endpoint di monitoraggio dell'anteprima per visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e recapito. Il canale può anche registrare e archiviare il contenuto inserito per poter essere trasmesso in un secondo momento (video on demand).

Questa soluzione si basa sui servizi gestiti di Azure: Servizi multimediali e rete per la distribuzione di contenuti. Questi servizi vengono eseguiti in un ambiente a disponibilità elevata, con patch e supportati, consentendo di concentrarsi sulla soluzione anziché sull'ambiente in cui vengono eseguiti.

Vedere [Live streaming Digital Media](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) nel centro architetture di Azure.

## <a name="video-on-demand-digital-media"></a>Servizi multimediali digitali di video su richiesta

Una soluzione video on demand di base che ti offre la possibilità di trasmettere contenuti video registrati, come filmati, notizie, clip, segmenti sportivi, video di formazione ed esercitazioni sul supporto tecnico per qualsiasi dispositivo endpoint con supporto per video, applicazione per dispositivi mobili o browser desktop. I file video vengono caricati nell'archiviazione BLOB di Azure, codificati in un formato standard a più velocità in bit e quindi distribuiti tramite tutti i principali protocolli di streaming con velocità in bit adattiva (HLS, MPEG-DASH, Smooth) al client di Azure Media Player.

Questa soluzione si basa sui servizi gestiti di Azure: archiviazione BLOB, rete per la distribuzione di contenuti e Azure Media Player. Questi servizi vengono eseguiti in un ambiente a disponibilità elevata, con patch e supportati, consentendo di concentrarsi sulla soluzione anziché sull'ambiente in cui vengono eseguiti.

Vedere i [supporti digitali video on demand](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) nel centro architetture di Azure.

## <a name="gridwich-media-processing-system"></a>Sistema di elaborazione multimediale Gridwich

Il sistema Gridwich incorpora le procedure consigliate per l'elaborazione e la distribuzione di asset multimediali in Azure. Sebbene il sistema Gridwich sia specifico del supporto, il Framework di elaborazione dei messaggi e di eventi può essere applicato a qualsiasi flusso di lavoro di elaborazione di eventi senza stato.

Vedere [Gridwich Media Processing System](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) nel centro architetture di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [Panoramica di Servizi multimediali di Azure](media-services-overview.md)