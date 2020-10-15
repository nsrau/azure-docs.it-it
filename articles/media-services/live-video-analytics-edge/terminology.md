---
title: Analisi video in tempo reale sulla terminologia EDG-Azure
description: Questo articolo fornisce una panoramica dell'analisi dei video in tempo reale sulla terminologia IoT Edge.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88690613"
---
# <a name="terminology"></a>Terminologia

Questo articolo fornisce una panoramica della terminologia relativa all' [analisi dei video in tempo reale su IOT Edge](overview.md).

## <a name="azure-media-services"></a>Servizi multimediali di Azure

Servizi multimediali di Azure è una piattaforma multimediale cloud che consente di creare soluzioni multimediali. Per altre informazioni, vedere la documentazione di [servizi multimediali di Azure](../latest/media-services-overview.md) .

## <a name="asset"></a>Asset

[Asset](../latest/assets-concept.md) è un'entità di servizi multimediali di Azure che esegue il mapping a un contenitore BLOB nell'account di archiviazione di Azure collegato a un account di servizi multimediali. Tutti i file associati a un asset vengono archiviati come BLOB in tale contenitore, mentre servizi multimediali include i metadati, ad esempio un nome, una descrizione e un'ora di creazione, associati all'asset.

Analisi video in tempo reale su IoT Edge può creare asset e/o aggiungere dati agli asset esistenti. Questo consente gli scenari di registrazione e riproduzione video continui e basati su eventi (con acquisizione video sul dispositivo perimetrale, registrazione in servizi multimediali di Azure e riproduzione tramite le funzionalità di streaming di servizi multimediali di Azure esistenti).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) è un Framework RPC (Remote Procedure Call) a prestazioni elevate e indipendente dal linguaggio. USA schemi strutturati basati sulla sessione tramite [buffer del protocollo 3](https://developers.google.com/protocol-buffers/docs/proto3) come formato di interscambio dei messaggi sottostante per la comunicazione.

## <a name="media-graph"></a>Grafico multimediale

Il [grafico multimediale](media-graph-concept.md) consente di definire la posizione in cui devono essere acquisiti i supporti, la modalità di elaborazione e la posizione in cui devono essere recapitati i risultati. Consente di definire un grafo costituito da origini, processori e nodi sink, consentendo quindi di creare applicazioni di analisi video in tempo reale. Il grafico multimediale è illustrato in dettaglio nella pagina del concetto di grafico multimediale.

## <a name="recording"></a>Registrazione

Nel contesto di un sistema di gestione video per le fotocamere per la sicurezza, la registrazione video si riferisce al processo di acquisizione di video dalle fotocamere e all'archiviazione in un file (o file) per la visualizzazione successiva tramite app per dispositivi mobili e browser. La registrazione video può essere suddivisa in una registrazione video [continua](continuous-video-recording-concept.md) e in una [registrazione video basata su eventi](event-based-video-recording-concept.md). Queste informazioni sono descritte più dettagliatamente nella pagina del concetto di [registrazione video](video-recording-concept.md) .

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) si riferisce al protocollo Real-Time streaming. Si tratta di un protocollo a livello di applicazione che controlla il recapito dei dati con proprietà in tempo reale. RTSP fornisce un framework estensibile per consentire la distribuzione su richiesta controllata di dati in tempo reale, ad esempio audio e video. 

## <a name="streaming"></a>Streaming

Se è stato osservato un video su un dispositivo mobile da servizi come Netflix, YouTube e altri, è stato sperimentato lo streaming di video. La riproduzione inizia subito dopo aver raggiunto la "riproduzione" (se si dispone di una larghezza di banda sufficiente) ed è possibile eseguire ricerche avanti e indietro lungo la sequenza temporale del video. Con il flusso, l'idea è fornire solo la parte del video che viene controllata e consentire al visualizzatore di iniziare a riprodurre il video mentre i dati vengono ancora trasferiti da un server al client di riproduzione. Nel contesto di servizi multimediali di Azure, lo [streaming](https://en.wikipedia.org/wiki/Streaming_media) si riferisce al processo di distribuzione di file multimediali da [servizi multimediali di Azure](../azure-media-player/azure-media-player-overview.md) a un client di streaming, ad esempio Azure Media Player. È possibile usare servizi multimediali di Azure per trasmettere video ai client usando protocolli di streaming multimediali basati su HTTP di settore, come [http live streaming (HLS)](https://developer.apple.com/streaming/) e [MPEG-Dash](https://dashif.org/about/). HLS è supportato da Azure Media Player e da Web Player come [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [videojs](https://videojs.com/), [Google Shaka Player](https://github.com/google/shaka-player)oppure è possibile eseguire il rendering in modo nativo nelle app per dispositivi mobili con Android [Exoplayer](https://github.com/google/ExoPlayer) e iOS ' s [AV Foundation](https://developer.apple.com/av-foundation/). MPEG-DASH è supportato in modo analogo da Azure Media Player, [trovare un elenco di client in questa pagina](https://dashif.org/clients/). 

Usando il [grafo multimediale](#media-graph)s per registrare i video in un asset in servizi multimediali di Azure, è possibile usare la funzionalità di streaming di servizi multimediali per distribuire flussi video in HLS e Dash. Per altre informazioni, vedere l'articolo relativo alla [riproduzione video](video-playback-concept.md) .

## <a name="vms"></a>MACCHINE virtuali

Le [macchine virtuali](https://en.wikipedia.org/wiki/Video_management_system) fanno riferimento a un sistema di gestione video. Questi sistemi vengono usati per configurare e controllare le telecamere TVCC, acquisire e registrare video da essi. Questi sistemi forniscono anche alle applicazioni client la riproduzione del video registrato

## <a name="next-steps"></a>Passaggi successivi

[Grafici dei supporti](media-graph-concept.md)
