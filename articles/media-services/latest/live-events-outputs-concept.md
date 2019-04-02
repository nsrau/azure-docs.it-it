---
title: Concetti relativi allo streaming live in Servizi multimediali di Azure - Eventi live e output live | Microsoft Docs
description: Questo articolo offre una panoramica dei concetti dello streaming live in Servizi multimediali di Azure v3.
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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 1d28701dd35b9d80fd52a1f102c53f2d59d63b09
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762355"
---
# <a name="live-events-and-live-outputs"></a>Eventi live e output live

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per configurare gli eventi di streaming live in servizi multimediali v3, è necessario comprendere i concetti illustrati in questo articolo. <br/>L'elenco delle sezioni è disponibile a destra della pagina.

## <a name="live-events"></a>Eventi live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento live, si genera un endpoint di input che è possibile usare per inviare un segnale in tempo reale da un codificatore remoto. Il codificatore live remoto invia il feed di contributi al suddetto endpoint di input usando il protocollo [RTMP](https://www.adobe.com/devnet/rtmp.html) o [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (MP4 frammentato). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`. Per il protocollo di inserimento RTMP, gli schemi URL supportati sono `rtmp://` o `rtmps://`. 

## <a name="live-event-types"></a>Tipi di evento live

Un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: pass-through e codifica live. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando si usa l'**evento live** pass-through, al codificatore live locale è affidata la generazione di un flusso video con velocità in bit multipla e l'invio come feed di contributi all'evento live (tramite il protocollo RTMP o MP4 frammentato). L'evento live esegue quindi i flussi video in ingresso senza ulteriori elaborazioni. Questo tipo di LiveEvent pass-through è ottimizzato per eventi in tempo reale di lunga durata o per lo streaming live lineare 24/7. Quando si crea questo tipo di evento live, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2).  Vedere l'articolo [Confronto tra tipi di eventi live](live-event-types-comparison.md) per altri dettagli.

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per realizzare lo streaming live quando si eseguono più eventi per un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Per un esempio di codice .NET, vedere [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codifica live  

![codifica live](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica live con Servizi multimediali, è possibile configurare il codificatore live locale per l'invio di un video a bitrate singolo come feed di contributi all'evento live (tramite il protocollo RTMP o Mp4 frammentato). L'evento live codifica tale flusso a bitrate singolo in ingresso in un [flusso video con velocità in bit multipla](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) e lo rende disponibile per la distribuzione e la riproduzione in dispositivi tramite protocolli come MPEG-DASH, HLS e Smooth Streaming. Quando si crea questo tipo di evento live, specificare il tipo di codifica **Standard** (LiveEventEncodingType.Standard).

È possibile inviare il feed di contributi a risoluzioni fino a 1080p e a una frequenza pari a 30 fotogrammi al secondo, con codec video H.264/AVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Vedere l'articolo [Confronto tra tipi di eventi live](live-event-types-comparison.md) per altri dettagli.

Quando si usa la codifica live (evento Live impostato su **Standard**), il set di impostazioni di codifica definisce come viene codificato il flusso in ingresso con velocità in bit o livelli diversi. Per altre informazioni, vedere [Set di impostazioni di sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Attualmente, l'unico set di impostazioni consentito per il tipo di evento Live Standard ha valore *Default720p*. Se è necessario usare un set di impostazioni di codifica live personalizzato, contattare amshelp@microsoft.com. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo livello a 720p e un massimo di 6 livelli.

## <a name="live-event-creation-options"></a>Opzioni di creazione di eventi live

Quando si crea un evento live, è possibile specificare le opzioni seguenti:

* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: Indirizzo IpV4 con 4 numeri, intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. In alternativa, è possibile avviare l'evento quando si è pronti ad avviare lo streaming. 

    Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URL di inserimento di eventi live

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore locale live. Questi URL vengono usati dal codificatore live per inserire un flusso live. Per altre informazioni, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md). 

È possibile usare URL di non reindirizzamento a microsito o URL di reindirizzamento a microsito. 

* URL di non reindirizzamento a microsito

    L'URL di non reindirizzamento a microsito è la modalità predefinita in AMS v3. È possibile ottenere l'evento live rapidamente, ma l'URL di inserimento è noto solo all'avvio dell'evento live. L'URL verrà modificato se si arresta/avvia l'evento live. <br/>Il non reindirizzamento a microsito è utile negli scenari in cui un utente finale vuole trasmettere usando un'app che vuole ottenere un evento live il prima possibile e la presenza di un URL di inserimento dinamico non costituisce un problema.
* URL di reindirizzamento a microsito

    La modalità di reindirizzamento a microsito è preferita dai responsabili della trasmissione di file multimediali di grandi dimensioni, che usano codificatori di trasmissione hardware e non vogliono riconfigurare i codificatori quando avviano l'evento live, ma vogliono un URL di inserimento predittivo, che non cambia nel tempo.

> [!NOTE] 
> Perché un URL di inserimento sia predittivo, è necessario usare la modalità di reindirizzamento a microsito e passare il proprio token di accesso (per evitare un token casuale nell'URL).

### <a name="live-ingest-url-naming-rules"></a>Regole di denominazione degli URL di inserimento live

La stringa *casuale* sottostante è un numero esadecimale a 128 bit (costituito da 32 caratteri 0-9 a-f).<br/>
Il *token di accesso* sottostante è quello da specificare per l'URL fisso ed è ancora un numero esadecimale a 128 bit.

#### <a name="non-vanity-url"></a>URL di non reindirizzamento a microsito

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>URL di reindirizzamento a microsito

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>URL di anteprima di un evento live

Una volta che l'**evento live** inizia a ricevere il feed di contributi, è possibile usare l'endpoint di anteprima per visualizzare in anteprima e convalidare la ricezione del flusso live prima di pubblicare nuovamente. Dopo avere verificato che il flusso di anteprima sia ottimale, è possibile usare l'evento live per rendere il flusso live disponibile per la pubblicazione tramite uno o più **endpoint di streaming** creati in precedenza. A tale scopo, si crea un nuovo [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) nell'**evento live**. 

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

## <a name="live-event-long-running-operations"></a>Operazioni con esecuzione prolungata eventi in tempo reale

Per informazioni dettagliate, vedere [operazioni a esecuzione prolungata](entities-overview.md#long-running-operations)

## <a name="live-outputs"></a>Output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un [asset](https://docs.microsoft.com/rest/api/media/assets), un [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) e un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'output live, non si elimina l'asset sottostante e il contenuto dell'asset. 

La relazione tra un **evento live** e il relativo **output live** è simile alla trasmissione televisiva tradizionale: un canale (**evento live**) rappresenta un flusso costante di video e una registrazione (**output live**) è limitata a uno specifico segmento di tempo (ad esempio, le notizie della sera dalle 18:30 alle 19:00). È possibile registrare programmi TV tramite un DVR (Digital Video Recorder): la funzionalità equivalente di eventi live è gestita dalla proprietà **ArchiveWindowLength**. È un timespan ISO-8601 (ad esempio, PTHH:MM:SS), il quale specifica la capacità del DVR e può essere impostato da un minimo di 3 minuti fino a un massimo di 25 ore.

L'oggetto **output live** è come un registratore che riceve e registra il flusso live in un asset nell'account di Servizi multimediali. Il contenuto registrato verrà mantenuto nell'account di Archiviazione di Azure collegato al proprio account, nel contenitore definito dalla risorsa Asset. L'**output live** consente anche di regolare alcune proprietà del Live Stream in uscita, come la parte da conservare nelle registrazioni di archivio (ad esempio, la capacità del DVR cloud) e la possibilità da parte degli utenti di aprire il Live Stream. L'archivio su disco è una "finestra" dell'archivio circolare che contiene solo la quantità di contenuto specificato nella proprietà **archiveWindowLength** dell'**output live**. Il contenuto che rientra in questa finestra viene automaticamente rimosso dal contenitore di archiviazione e non è recuperabile. È possibile creare più **output live** (tre al massimo) su un **evento live** con lunghezze e impostazioni di archivio diverse.  

Se l'**asset** dell'**output live** è stato pubblicato usando un **localizzatore di streaming**, l'**evento live** (fino alla lunghezza dell'intervallo DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda del valore raggiunto per primo.

Per altre informazioni, vedere [Uso di un DVR cloud](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
