---
title: Panoramica dello streaming live con Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo offre una panoramica dello streaming live con Servizi multimediali di Azure v3.
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
ms.date: 12/26/2018
ms.author: juliako
ms.openlocfilehash: 3a2b3752926a3a4391ae9479ba636694533c97a8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788209"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming live con Servizi multimediali di Azure v3

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per eseguire lo streaming di eventi live con Servizi multimediali, è necessario quanto segue:  

- Una fotocamera usata per riprendere l'evento live.
- Un codificatore video live in grado di convertire i segnali provenienti dalla fotocamera (o da un altro dispositivo, come un portatile) in un feed di contributi inviato a Servizi multimediali. Il feed di contributi può includere segnali relativi alla pubblicità, ad esempio i marcatori SCTE-35.
- I componenti in Servizi multimediali, che consentono di inserire, visualizzare in anteprima, includere in un pacchetto, registrare, crittografare e trasmettere l'evento live ai clienti o a una rete CDN per un'ulteriore distribuzione.

Questo articolo offre una guida e una panoramica dettagliata, includendo i diagrammi dei principali componenti coinvolti nello streaming live con Servizi multimediali.

## <a name="overview-of-main-components"></a>Panoramica dei componenti principali

Per offrire streaming live oppure on demand con Servizi multimediali, è necessario avere almeno uno [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Quando viene creato l'account di Servizi multimediali, viene aggiunto all'account uno StreamingEndpoint **predefinito** in stato **Stopped**. È necessario avviare l'entità StreamingEndpoint da cui si vuole trasmettere il contenuto agli utenti. È possibile usare il valore **StreamingEndpoint** predefinito, oppure creare un nuovo **StreamingEndpoint** personalizzato con la configurazione desiderata e le impostazioni della rete CDN. È possibile decidere di abilitare più StreamingEndpoint, ciascuno destinato a una rete CDN diversa e fornendo un nome host univoco per la distribuzione dei contenuti. 

In Servizi multimediali, i [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento ed elaborazione dei feed video live. Quando si crea un LiveEvent, si genera un endpoint di input che è possibile usare per inviare un segnale in tempo reale da un codificatore remoto. Il codificatore live remoto invia il feed di contributi al suddetto endpoint di input usando il protocollo [RTMP](https://www.adobe.com/devnet/rtmp.html) o [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (MP4 frammentato). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`. Per il protocollo di inserimento RTMP, gli schemi URL supportati sono `rtmp://` o `rtmps://`. Per altre informazioni, vedere [Recommended live streaming encoders](recommended-on-premises-live-encoders.md) (Codificatori di streaming live consigliati).

Una volta che il **LiveEvent** inizia a ricevere il feed di contributi, è possibile usare l'endpoint di anteprima (URL di anteprima) per visualizzare in anteprima e convalidare la ricezione del flusso live prima di pubblicare nuovamente. Dopo avere verificato che il flusso di anteprima è ottimale, è possibile utilizzare il LiveEvent per rendere il flusso live disponibile per la pubblicazione tramite uno o più **StreamingEndpoint** creati in precedenza. A tale scopo, si crea un nuovo [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) nel **LiveEvent**. 

L'oggetto **LiveOutput** è come un registratore che riceve e registra il flusso live in un Asset nell'account di Servizi multimediali. Il contenuto registrato verrà mantenuto nell'account di Archiviazione di Azure collegato al proprio account, nel contenitore definito dalla risorsa Asset.  Il **LiveOuput** consente inoltre di controllare alcune proprietà del flusso live in uscita, come la quantità del flusso mantenuta nella registrazione di archivio (ad esempio, la capacità del DVR cloud). L'archivio su disco è una "finestra" dell'archivio circolare che contiene solo la quantità di contenuto specificato nella proprietà **archiveWindowLength** del **LiveOutput**. Il contenuto che rientra in questa finestra viene automaticamente rimosso dal contenitore di archiviazione e non è recuperabile. È possibile creare più LiveOutput (tre al massimo) su un LiveEvent con lunghezze e impostazioni di archivio diverse.  

Con Servizi multimediali è possibile sfruttare la **Creazione dinamica dei pacchetti**, che consente di visualizzare in anteprima e trasmettere i flussi live nei [formati MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) dal feed di contributi inviato al servizio. Gli utenti possono riprodurre il flusso live con qualsiasi lettore compatibile per HLS, DASH o Smooth Streaming. È possibile usare [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) nelle applicazioni web o per dispositivi mobili per distribuire lo streaming in uno di questi protocolli.

Servizi multimediali consente di distribuire contenuti crittografati dinamicamente (**Crittografia dinamica**) con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali fornisce inoltre un servizio per la distribuzione di chiavi AES e licenze Digital Rights Management ai client autorizzati. Per altre informazioni su come crittografare i contenuti con Servizi multimediali, consultare la [Panoramica sulla protezione dei contenuti](content-protection-overview.md)

Se si vuole, è anche possibile applicare filtri dinamici, che possono essere usati per controllare il numero di tracce, i formati, i bitrate e gli intervalli di tempo di presentazione che vengono inviati ai lettori. Per altre informazioni, vedere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

### <a name="new-capabilities-for-live-streaming-in-v3"></a>Nuove funzionalità per lo streaming live in v3

Con le API v3 di Servizi multimediali, è possibile sfruttare le seguenti nuove funzionalità:

- Nuova modalità di bassa latenza. Per altre informazioni, vedere [Latenza](live-event-latency.md).
- Supporto RTMP migliorato (maggiore stabilità e più supporto per i codificatori di origine).
- Inserimento RTMPS sicuro.<br/>Quando si crea un LiveEvent, si ottengono 4 URL di inserimento. pressoché identici: hanno lo stesso token di streaming (AppId) e solo la parte del numero di porta è diversa. Due URL sono primari e due sono di backup per RTMPS.   
- È possibile trasmettere eventi live per una durata massima di 24 ore quando si usa Servizi multimediali per la transcodifica di un feed di contributi a bitrate singolo in un flusso di output a bitrate multipli. 

## <a name="liveevent-types"></a>Tipi di LIveEvent

Un [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: pass-through e codifica live. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.png)

Quando si usa il LiveEvent pass-through, al codificatore live locale è affidata la generazione di un flusso video a più bitrate e il suo invio come feed di contributi per il LiveEvent (tramite il protocollo RTMP o MP4 frammentato). Il LiveEvent esegue quindi i flussi video in ingresso senza ulteriori elaborazioni. Questo tipo di LiveEvent pass-through è ottimizzato per eventi in tempo reale di lunga durata o per lo streaming live lineare 24/7. Quando si crea questo tipo di LiveEvent, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2).  Consultare l'articolo [Confronto e limitazioni dei tipi di LiveEvent](live-event-types-comparison.md) per ulteriori dettagli.

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per realizzare lo streaming live quando si eseguono più eventi per un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Vedere un esempio di live in [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codifica live  

![codifica live](./media/live-streaming/live-encoding.png)

Quando si usa la codifica live con Servizi multimediali, è possibile configurare il codificatore live locale per l'invio di un video a bitrate singolo come feed di contributi al LiveEvent (tramite il protocollo RTMP o Mp4 frammentato). Il LiveEvent codifica tale flusso a bitrate singolo in ingresso in un [flusso video a più bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) e lo rende disponibile per la distribuzione e la riproduzione in dispositivi tramite protocolli come MPEG-DASH, HLS e Smooth Streaming. Quando si crea questo tipo di LiveEvent, specificare il tipo di codifica **Standard** (LiveEventEncodingType.Standard).

È possibile inviare il feed di contributi a risoluzioni fino a 1080p e a una frequenza pari a 30 fotogrammi al secondo, con codec video H.264/AVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Consultare l'articolo [Confronto e limitazioni dei tipi di LiveEvent](live-event-types-comparison.md) per ulteriori dettagli.

## <a name="liveevent-types-comparison"></a>Confronto tra tipi di LiveEvent

L'articolo seguente contiene una tabella con un confronto tra le funzionalità dei due tipi di LiveEvent: [Confronto](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

Un [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) consente di regolare le proprietà del Live Stream in uscita, come la parte da registrare (ad esempio, la capacità del DVR cloud) e la possibilità da parte degli utenti di aprire il Live Stream. La relazione tra un **LiveEvent** e il relativo **LiveOutput** è simile alla trasmissione televisiva tradizionale: un canale (**LiveEvent**) rappresenta un flusso costante di video e una registrazione (**LiveOutput**) è limitata a uno specifico segmento di tempo (ad esempio, le notizie della sera dalle 18:30 alle 19:00). È possibile registrare programmi TV tramite un DVR (Digital Video Recorder): la funzionalità equivalente di LiveEvents è gestita dalla proprietà ArchiveWindowLength. È un timespan ISO-8601 (ad esempio, PTHH:MM:SS), il quale specifica la capacità del DVR e può essere impostato da un minimo di 3 minuti fino a un massimo di 25 ore.


> [!NOTE]
> I **LiveOutput** iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina il **LiveOutput**, non si elimina l'**asset** sottostante e il contenuto dell'asset.  

Per altre informazioni, vedere [Uso del DVR cloud](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Dopo l'avvio del flusso nel **LiveEvent**, è possibile iniziare l'evento di streaming creando **Asset**, **LiveOutput** e **StreamingLocator**. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Quando viene creato l'account di Servizi multimediali, viene aggiunto all'account un endpoint di streaming predefinito con stato "Arrestato". Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato In esecuzione.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />Stati e fatturazione di LiveEvent

Un LiveEvent avvia la fatturazione non appena il suo stato viene impostato su **In esecuzione**. Per impedire la fatturazione da parte del LiveEvent, è necessario arrestare il LiveEvent.

Per informazioni dettagliate, consultare [Stati e fatturazione](live-event-states-billing.md).

## <a name="latency"></a>Latenza

Per informazioni dettagliate sulla latenza dei LiveEvents, consultare [Latenza](live-event-latency.md).

## <a name="live-streaming-workflow"></a>Flusso di lavoro dello streaming live

Ecco i passaggi del flusso di lavoro di uno streaming live:

1. Creare un LiveEvent.
2. Creare un nuovo oggetto Asset.
3. Creare un LiveOutput e usare il nome dell'asset creato.
4. Creare un criterio di streaming e una chiave simmetrica se si desidera crittografare il contenuto con DRM.
5. Se non si usa DRM, creare un localizzatore di streaming con i tipi di criterio di streaming predefiniti.
6. Elencare i percorsi nei criteri di streaming per ottenere gli URL da usare (questi sono deterministici).
7. Ottenere il nome host per l'endpoint di streaming da cui si desidera trasmettere. 
8. Combinare l'URL del passaggio 6 con il nome host del passaggio 7 per ottenere l'URL completo.

Per altre informazioni, vedere una [esercitazione di streaming live](stream-live-tutorial-with-api.md) basata sull'esempio [Live .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live).

## <a name="next-steps"></a>Passaggi successivi

- [Confronto tra tipi di LiveEvent](live-event-types-comparison.md)
- [Stati e fatturazione](live-event-states-billing.md)
- [Latency](live-event-latency.md)
