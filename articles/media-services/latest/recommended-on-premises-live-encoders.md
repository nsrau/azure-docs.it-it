---
title: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali - Azure | Microsoft Docs
description: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4d25e4cd94fec35f31594544b619aa054a35d58d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302341"
---
# <a name="recommended-live-streaming-encoders"></a>Codificatori di streaming live consigliati

In Servizi multimediali un [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (canale) rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Un LiveEvent riceve i flussi di input live in uno dei due modi seguenti:

* Un codificatore live locale invia un flusso RTMP o Smooth Streaming (MP4 frammentato) a bitrate multipli al LiveEvent che non è abilitato per eseguire la codifica live con Servizi multimediali. I flussi inseriti passano attraverso i LiveEvent senza altri processi di elaborazione. Questo metodo viene chiamato **pass-through**. Un codificatore live può inviare un flusso a bitrate singolo a un canale pass-through, ma questa configurazione non è consigliata perché non è consentito per lo streaming a bitrate adattivo nel client.

  > [!NOTE]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.

* Un codificatore live locale invia un flusso a bitrate singolo al LiveEvent abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei formati seguenti: RTMP o Smooth Streaming (MP4 frammentato). Il LiveEvent esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo).

Per informazioni dettagliate sulla codifica live con Servizi multimediali, vedere [Streaming live con Servizi multimediali di Azure v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Codificatori live con output RTMP

Servizi multimediali consiglia di usare uno dei codificatori live seguenti che hanno RTMP come output. Gli schemi URL supportati sono `rtmp://` o `rtmps://`.

> [!NOTE]
 > Durante lo streaming tramite RTMP, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte TCP in uscita 1935 e 1936 siano aperte.<br/>
 Durante lo streaming tramite RTMPS, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte TCP in uscita 2935 e 2936 siano aperte.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificatori live con output MP4 frammentato

Servizi multimediali consiglia di usare uno dei codificatori live seguenti che hanno Smooth Streaming (MP4 frammentato) a bitrate multipli come output. Gli schemi URL supportati sono `http://` o `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live e Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Come diventare un partner di codificatore locale

Servizi multimediali di Azure offre ai partner di codificatore locale il vantaggio di promuovere il loro prodotto consigliandolo ai clienti aziendali. Per diventare un partner di codificatore locale, è necessario verificare la compatibilità del proprio codificatore locale con Servizi multimediali. A questo scopo, completare le verifiche seguenti:

### <a name="pass-through-liveevent-verification"></a>Verifica del LiveEvent pass-through

1. Creare o visitare l'account di Servizi multimediali di Azure.
2. Creare e avviare un LiveEvent **pass-through**.
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato.
5. Eseguire il codificatore live per circa 10 minuti.
6. Arrestare l'evento live.
7. Creare e avviare un endpoint di streaming, usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare l'asset archiviato e verificare che la riproduzione non presenti anomalie visibili a livello di qualità. In alternativa, guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live prima di eseguire il passaggio 6.
8. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live.
9. Reimpostare lo stato del LiveEvent dopo la creazione di ogni esempio.
10. Ripetere i passaggi da 3 a 9 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni annunci/sottotitoli/diverse velocità di codifica).

### <a name="live-encoding-liveevent-verification"></a>Verifica del LiveEvent di codifica live

1. Creare o visitare l'account di Servizi multimediali di Azure.
2. Creare e avviare un LiveEvent di **codifica live**.
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate singolo.
4. Creare un evento live pubblicato.
5. Eseguire il codificatore live per circa 10 minuti.
6. Arrestare l'evento live.
7. Creare e avviare un endpoint di streaming, usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare l'asset archiviato e verificare che la riproduzione non presenti anomalie visibili a livello di qualità. In alternativa, guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live prima di eseguire il passaggio 6.
8. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live.
9. Reimpostare lo stato del LiveEvent dopo la creazione di ogni esempio.
10. Ripetere i passaggi da 3 a 9 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni annunci/sottotitoli/diverse velocità di codifica).

### <a name="longevity-verification"></a>Verifica della durata

1. Creare o visitare l'account di Servizi multimediali di Azure.
2. Creare e avviare un canale **pass-through**.
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato.
5. Eseguire il codificatore live per almeno una settimana.
6. Usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare di volta in volta lo streaming live (o l'asset archiviato) e assicurarsi che la riproduzione non presenti anomalie visibili.
7. Arrestare l'evento live.
8. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live.

Inviare infine un messaggio di posta elettronica con le impostazioni registrate e i parametri di archiviazione live al team di Servizi multimediali di Azure all'indirizzo amsstreaming@microsoft.com per notificare che tutti i test di autoverifica sono stati superati. Includere anche le proprie informazioni di contatto per successive comunicazioni. È possibile contattare il team di Servizi multimediali di Azure per qualsiasi domanda su questo processo.

## <a name="next-steps"></a>Passaggi successivi

[Streaming live con Servizi multimediali v3](live-streaming-overview.md)
