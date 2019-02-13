---
title: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali - Azure | Microsoft Docs
description: Informazioni sui codificatori locali di streaming live consigliati da Servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c3e42ba9fe84ded8c60fc71f19de785945852116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656669"
---
# <a name="recommended-live-streaming-encoders"></a>Codificatori di streaming live consigliati

In Servizi multimediali un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) (canale) rappresenta una pipeline per l'elaborazione di contenuto in streaming live. L'evento live riceve i flussi di input live in uno dei due modi seguenti:

* Un codificatore live locale invia un flusso RTMP o Smooth Streaming (MP4 frammentato) a bitrate multipli all'evento live che non è abilitato per eseguire la codifica live con Servizi multimediali. I flussi inseriti passano attraverso gli eventi live senza altri processi di elaborazione. Questo metodo viene chiamato **pass-through**. Un codificatore live può inviare un flusso a bitrate singolo a un canale pass-through, ma questa configurazione non è consigliata perché non è consentito per lo streaming a bitrate adattivo nel client.

  > [!NOTE]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.

* Un codificatore live locale invia un flusso a bitrate singolo all'evento live abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei formati seguenti: RTMP o Smooth Streaming (MP4 frammentato). L'evento live esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo).

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

## <a name="configuring-on-premises-live-encoder-settings"></a>Configurazione delle impostazioni del codificatore live locale

Per informazioni sulle impostazioni valide per il tipo specifico di evento live, vedere [Confronto tra tipi di eventi live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisiti per la riproduzione

Per la riproduzione di contenuto devono essere presenti sia un flusso audio sia un flusso video. La riproduzione del flusso solo video non è supportata.

### <a name="configuration-tips"></a>Suggerimenti per la configurazione

- Se possibile, usare una connessione a Internet con cablaggio fisico.
- È buona norma raddoppiare le velocità in bit di streaming in fase di determinazione dei requisiti di larghezza di banda. Anche se non si tratta di un requisito obbligatorio, contribuirà a ridurre l'impatto della congestione della rete.
- Se si usano codificatori basati su software, chiudere tutti i programmi non necessari.
- Non modificare la configurazione del codificatore dopo che è stato avviato il processo di push. Una modifica di questo tipo ha effetti negativi sull'evento e può renderlo instabile. 
- Assicurarsi di avere a disposizione un ampio margine di tempo per configurare l'evento. Per gli eventi di ampia portata, è consigliabile iniziare la configurazione un'ora prima dell'evento.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Come diventare un partner di codificatore locale

Servizi multimediali di Azure offre ai partner di codificatore locale il vantaggio di promuovere il loro prodotto consigliandolo ai clienti aziendali. Per diventare un partner di codificatore locale, è necessario verificare la compatibilità del proprio codificatore locale con Servizi multimediali. A questo scopo, completare le verifiche seguenti:

### <a name="pass-through-live-event-verification"></a>Verifica dell'evento live pass-through

1. Nell'account di Servizi multimediali assicurarsi che l'**endpoint di streaming** sia in esecuzione. 
2. Creare e avviare l'evento live **pass-through**. <br/> Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore locale per usare l'URL per l'invio del flusso live a bitrate multipli a Servizi multimediali.
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**.
6. Creare un **LiveOutput** e usare il nome dell'asset creato.
7. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare.
9. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Eseguire il codificatore live per circa 10 minuti.
12. Arrestare l'evento live. 
13. Usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare l'asset archiviato e verificare che la riproduzione non presenti anomalie visibili a tutti i livelli di qualità. In alternativa, guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live.
14. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live.
15. Reimpostare lo stato dell'evento live dopo la creazione di ogni esempio.
16. Ripetere i passaggi da 5 a 15 per tutte le configurazioni supportate dal codificatore (con e senza annunci pubblicitari/sottotitoli/diverse velocità di codifica).

### <a name="live-encoding-live-event-verification"></a>Verifica dell'evento live di codifica live

1. Nell'account di Servizi multimediali assicurarsi che l'**endpoint di streaming** sia in esecuzione. 
2. Creare e avviare l'evento live di **codifica live**. <br/> Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore per eseguire il push di un flusso live a bitrate singolo a Servizi multimediali.
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**.
6. Creare un **LiveOutput** e usare il nome dell'asset creato.
7. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare.
9. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Eseguire il codificatore live per circa 10 minuti.
12. Arrestare l'evento live.
13. Usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare l'asset archiviato e verificare che la riproduzione non presenti anomalie visibili a tutti i livelli di qualità. In alternativa, guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live.
14. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live.
15. Reimpostare lo stato dell'evento live dopo la creazione di ogni esempio.
16. Ripetere i passaggi da 5 a 15 per tutte le configurazioni supportate dal codificatore (con e senza annunci pubblicitari/sottotitoli/diverse velocità di codifica).

### <a name="longevity-verification"></a>Verifica della durata

Seguire gli stessi passaggi di [Verifica dell'evento live pass-through](#pass-through-live-event-verification) tranne il passaggio 11. <br/>Invece che per 10 minuti, eseguire il codificatore live per almeno una settimana. Usare un lettore, ad esempio [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html), per guardare di volta in volta lo streaming live (o l'asset archiviato) e assicurarsi che la riproduzione non presenti anomalie visibili.

### <a name="email-your-recorded-settings"></a>Inviare tramite posta elettronica le impostazioni registrate

Inviare infine un messaggio di posta elettronica con le impostazioni registrate e i parametri di archiviazione live al team di Servizi multimediali di Azure all'indirizzo amsstreaming@microsoft.com per notificare che tutti i test di autoverifica sono stati superati. Includere anche le proprie informazioni di contatto per successive comunicazioni. È possibile contattare il team di Servizi multimediali di Azure per qualsiasi domanda su questo processo.

## <a name="next-steps"></a>Passaggi successivi

[Streaming live con Servizi multimediali v3](live-streaming-overview.md)
