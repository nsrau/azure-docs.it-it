---
title: Codificatori di streaming live consigliati da Servizi multimediali - Azure | Microsoft Docs
description: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 53d68a18c5904b8b7e2f6145ae26221e99395a82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749937"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Codificatori di streaming live locali verificati

In Servizi multimediali di Azure, un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) (canale) rappresenta una pipeline per l'elaborazione di contenuti in streaming live. L'evento live riceve i flussi di input live in uno dei due modi seguenti.

* Un codificatore live locale invia un flusso RTMP o Smooth Streaming (MP4 frammentato) a bitrate multipli all'evento live che non è abilitato per eseguire la codifica live con Servizi multimediali. I flussi inseriti passano attraverso gli eventi live senza altri processi di elaborazione. Questo metodo viene chiamato **pass-through**. È consigliabile che il codificatore Live invii flussi a bitrate multipli anziché un flusso a bitrate singolo a un evento Live pass-through per consentire lo streaming a bitrate adattivo per il client. 

    Se si usano flussi a bitrate multipli per l'evento Live pass-through, le dimensioni del GOP video e i frammenti video in bitrate diverse devono essere sincronizzati per evitare comportamenti imprevisti sul lato riproduzione.

  > [!TIP]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.
 
* Un codificatore Live locale invia un flusso a bitrate singolo all'evento live che è abilitato per l'esecuzione della codifica live con servizi multimediali in uno dei formati seguenti: RTMP o Smooth Streaming (MP4 frammentato). L'evento live esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo).

Questo articolo illustra i codificatori di streaming live locali verificati. La verifica viene eseguita tramite la verifica autonoma del fornitore o la verifica del cliente. Servizi multimediali di Microsoft Azure non esegue test completi o rigorosi di ogni codificatore e non esegue continuamente la verifica degli aggiornamenti. Per istruzioni su come verificare il codificatore Live locale, vedere [verificare il codificatore locale](become-on-premises-encoder-partner.md)

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
- [Blackmagic ATEM mini e ATEM mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versione 2.14.15 e successive)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 e Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (versione 13.0.2 o successiva a causa del requisito TLS 1,2)
- Telestream Wirecast S (è supportato solo RTMP. Nessun supporto per RTMPS a causa della mancanza di TLS 1.2 +)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> L'elenco di codificatori precedente è semplicemente un elenco di raccomandazioni. I codificatori non vengono sottoposti a test o convalidati da Microsoft in modo continuo e gli aggiornamenti o le modifiche di rilievo possono essere introdotti dai fornitori del codificatore o da progetti open source che potrebbero interrompere la compatibilità. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Codificatori Live che restituiscono MP4 frammentato (inserimento Smooth Streaming)

Servizi multimediali consiglia di usare uno dei codificatori live seguenti dotati di Smooth Streaming (MP4 frammentati) a bitrate multipli come output. Gli schemi URL supportati sono `http://` o `https://`.

> [!NOTE]
> I codificatori devono supportare TLS 1,2 quando si usano protocolli HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versione 2.14.15 e successive a causa del requisito TLS 1,2)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Imagine Communications Selenio MCP3
- Media Excel Hero Live e Hero 4K (UHD/HEVC)

> [!TIP]
>  Se si esegue lo streaming di eventi Live in più lingue, ad esempio una traccia audio in lingua inglese e una traccia audio spagnola, è possibile eseguire questa operazione con il codificatore multimediale Excel Live configurato per inviare il feed live a un evento Live pass-through.

> [!WARNING]
> L'elenco di codificatori precedente è semplicemente un elenco di raccomandazioni. I codificatori non vengono sottoposti a test o convalidati da Microsoft in modo continuo e possono essere introdotti dai fornitori del codificatore o da progetti open source che interrompono la compatibilità in qualsiasi momento. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Configurazione delle impostazioni del codificatore live locale

Per informazioni sulle impostazioni valide per il tipo specifico di evento live, vedere [Confronto tra tipi di eventi live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisiti per la riproduzione

Per la riproduzione di contenuti, devono essere presenti flussi audio e video. La riproduzione del flusso solo video non è supportata.

### <a name="configuration-tips"></a>Suggerimenti per la configurazione

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- In fase di determinazione dei requisiti di larghezza di banda, raddoppiare le velocità in bit di streaming. Anche se non è obbligatorio, questa semplice regola consente di attenuare l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.
- Modificare la configurazione del codificatore dopo aver eseguito il push comporta effetti negativi sull'evento. Apportare modifiche alla configurazione potrebbe comportare l’instabilità dell'evento. 
- Verificare e convalidare sempre le versioni più recenti del software codificatore per la compatibilità continua con servizi multimediali di Azure. Microsoft non convalida nuovamente i codificatori in questo elenco e la maggior parte delle convalide viene eseguita dai fornitori di software direttamente come "certificazione automatica".
- Assicurarsi di disporre di un ampio margine di tempo per configurare l'evento. Per gli eventi a scalabilità elevata, è consigliabile iniziare la configurazione un'ora prima dell'evento.
- Usare l'output del codec audio AAC-LC e del video H. 264.
- Attenersi alle risoluzioni supportate e alle frequenze dei fotogrammi per il tipo di evento Live da trasmettere, ad esempio 60fps è attualmente rifiutato.
- Verificare che vi sia un fotogramma chiave o un allineamento temporale GOP tra le qualità del video.
- Verificare che sia presente un nome di flusso univoco per ogni qualità video.
- Usare la codifica CBR rigorosa consigliata per prestazioni ottimali a bitrate adattivo.

> [!IMPORTANT]
> Controllare la condizione fisica del computer (CPU/memoria/ecc.) come il caricamento di frammenti nel cloud implica operazioni di CPU e i/o. Se si modificano le impostazioni del codificatore, è necessario reimpostare l'evento Channels/Live per rendere effettive le modifiche.

## <a name="see-also"></a>Vedere anche

[Streaming live con Servizi multimediali v3](live-streaming-overview.md)

## <a name="next-steps"></a>Passaggi successivi

[Come verificare il codificatore](become-on-premises-encoder-partner.md)
