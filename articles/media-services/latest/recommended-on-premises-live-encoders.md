---
title: Codificatori di streaming live consigliati da Servizi multimediali - Azure | Microsoft Docs
description: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268185"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>Codificatori di streaming live locali consigliati

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) (canale) rappresenta una pipeline per l'elaborazione di contenuti in streaming live. L'evento live riceve i flussi di input live in uno dei due modi seguenti.

* Un codificatore live locale invia un flusso RTMP o Smooth Streaming (MP4 frammentato) a bitrate multipli all'evento live che non è abilitato per eseguire la codifica live con Servizi multimediali. I flussi inseriti passano attraverso gli eventi live senza altri processi di elaborazione. Questo metodo viene chiamato **pass-through**. È consigliabile che il codificatore Live invii flussi a bitrate multipli anziché un flusso a bitrate singolo a un evento Live pass-through per consentire lo streaming a bitrate adattivo per il client. 

    Se si usano flussi a bitrate multipli per l'evento Live pass-through, le dimensioni del GOP video e i frammenti video in bitrate diverse devono essere sincronizzati per evitare comportamenti imprevisti sul lato riproduzione.

  > [!NOTE]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.
 
* Un codificatore Live locale invia un flusso a bitrate singolo all'evento live che è abilitato per l'esecuzione della codifica live con servizi multimediali in uno dei formati seguenti: RTMP o Smooth Streaming (MP4 frammentato). L'evento live esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo).

Per informazioni dettagliate sulla codifica live con Servizi multimediali, vedere [Streaming live con Servizi multimediali di Azure v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisiti del codificatore

I codificatori devono supportare TLS 1,2 quando si usano protocolli HTTPS o RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificatori live con output RTMP

Servizi multimediali consiglia di usare uno dei codificatori live seguenti che hanno RTMP come output. Gli schemi URL supportati sono `rtmp://` o `rtmps://`.

Durante lo streaming tramite RTMP, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte TCP in uscita 1935 e 1936 siano aperte.<br/><br/>
Durante lo streaming tramite RTMPS, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte TCP in uscita 2935 e 2936 siano aperte.

> [!NOTE]
> I codificatori devono supportare TLS 1,2 quando si usano i protocolli RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versione 2.14.15 e successive)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (versione 13.0.2 o successiva a causa del requisito TLS 1,2)
- Telestream Wirecast S (è supportato solo RTMP)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 e Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificatori live che attivano MP4 frammentati

Servizi multimediali consiglia di usare uno dei codificatori live seguenti dotati di Smooth Streaming (MP4 frammentati) a bitrate multipli come output. Gli schemi URL supportati sono `http://` o `https://`.

> [!NOTE]
> I codificatori devono supportare TLS 1,2 quando si usano protocolli HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versione 2.14.15 e successive a causa del requisito TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live e Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Se si esegue lo streaming di eventi Live in più lingue, ad esempio una traccia audio in lingua inglese e una traccia audio spagnola, è possibile eseguire questa operazione con il codificatore multimediale Excel Live configurato per inviare il feed live a un evento Live pass-through.

## <a name="configuring-on-premises-live-encoder-settings"></a>Configurazione delle impostazioni del codificatore live locale

Per informazioni sulle impostazioni valide per il tipo specifico di evento live, vedere [Confronto tra tipi di eventi live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisiti per la riproduzione

Per la riproduzione di contenuti, devono essere presenti flussi audio e video. La riproduzione del flusso solo video non è supportata.

### <a name="configuration-tips"></a>Suggerimenti per la configurazione

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- In fase di determinazione dei requisiti di larghezza di banda, raddoppiare le velocità in bit di streaming. Anche se non è obbligatorio, questa semplice regola consente di attenuare l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.
- Modificare la configurazione del codificatore dopo aver eseguito il push comporta effetti negativi sull'evento. Apportare modifiche alla configurazione potrebbe comportare l’instabilità dell'evento. 
- Assicurarsi di disporre di un ampio margine di tempo per configurare l'evento. Per gli eventi a scalabilità elevata, è consigliabile iniziare la configurazione un'ora prima dell'evento.

## <a name="see-also"></a>Vedere anche

[Diventare un partner del codificatore locale](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Passaggi successivi

[Streaming live con Servizi multimediali v3](live-streaming-overview.md)
