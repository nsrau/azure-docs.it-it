---
title: Eventi live e output Live in servizi multimediali
titleSuffix: Azure Media Services
description: Panoramica degli eventi live e degli output Live in servizi multimediali di Azure V3.
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
ms.date: 09/30/2019
ms.author: juliako
ms.openlocfilehash: d2f0689dd1f1b5fbe349478ad885b76eb79d91a0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569678"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventi live e output Live in servizi multimediali

Servizi multimediali di Azure ti permette di offrire eventi live ai tuoi clienti nel cloud di Azure. Per configurare gli eventi di streaming live in servizi multimediali V3, è necessario comprendere i concetti illustrati in questo articolo.

> [!TIP]
> Per i clienti che eseguono la migrazione dalle API di servizi multimediali V2, l'entità **evento Live** sostituisce il **canale** nella versione V2 e l' **output attivo** sostituisce il **programma**.

## <a name="live-events"></a>Eventi live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento Live, viene creato un endpoint di input primario e secondario che è possibile usare per inviare un segnale Live da un codificatore remoto. Il codificatore Live remoto invia il feed di contributo a tale endpoint di input usando il protocollo di input [RTMP](https://www.adobe.com/devnet/rtmp.html) o [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (frammentato-MP4). Per il protocollo di inserimento RTMP, il contenuto può essere inviato in chiaro (`rtmp://`) o crittografato in modo sicuro in rete (`rtmps://`). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`.  

## <a name="live-event-types"></a>Tipi di evento live

Un [evento Live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di uno dei due tipi seguenti: pass-through o codifica live. I tipi vengono impostati durante la creazione con [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None**: un codificatore Live locale invia un flusso a più velocità in bit. Il flusso inserito passa attraverso l'evento live senza ulteriori elaborazioni. 
* **LiveEventEncodingType. standard**: un codificatore Live locale invia un flusso a bitrate singolo all'evento Live e servizi multimediali crea più flussi a bitrate multipli. Se il feed di contributo è di risoluzione 720p o superiore, il set di impostazioni **Default720p** codifica un set di 6 coppie di risoluzione/velocità in bit.
* **LiveEventEncodingType. Premium1080p**: un codificatore Live locale invia un flusso a bitrate singolo all'evento Live e servizi multimediali crea più flussi a bitrate multipli. Il set di impostazioni Default1080p specifica il set di output di coppie di risoluzione/velocità in bit.

### <a name="pass-through"></a>Pass-through

![Evento Live pass-through con il diagramma di esempio di servizi multimediali](./media/live-streaming/pass-through.svg)

Quando si usa l'**evento live** pass-through, al codificatore live locale è affidata la generazione di un flusso video con velocità in bit multipla e l'invio come feed di contributi all'evento live (tramite il protocollo RTMP o MP4 frammentato). L'evento live esegue quindi i flussi video in ingresso senza ulteriori elaborazioni. Questo evento Live pass-through è ottimizzato per eventi live a esecuzione prolungata o streaming live lineare 24x365. Quando si crea questo tipo di evento live, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Per altre informazioni, vedere [confronto tra tipi di eventi live](live-event-types-comparison.md).

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per eseguire lo streaming live quando si eseguono più eventi in un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
>

Per un esempio di codice .NET, vedere [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codifica live  

![diagramma di esempio di codifica live con servizi multimediali](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica live con servizi multimediali, si configura il codificatore Live locale per inviare un video a bitrate singolo come feed di contributo all'evento Live (usando il protocollo RTMP o fragmented-MP4). Si configura quindi un evento Live per codificare il flusso a bitrate singolo in ingresso in un [flusso video a più velocità in bit](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e rende disponibile l'output per la riproduzione di dispositivi tramite protocolli come MPEG-Dash, HLS e Smooth Streaming.

Quando si usa la codifica live, è possibile inviare il feed di contributo solo alle risoluzioni fino alla risoluzione 1080p a una frequenza di fotogrammi pari a 30 frame al secondo, con codec video H. 264/AVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Si noti che gli eventi live pass-through possono supportare le risoluzioni fino a 4K a 60 frame al secondo. Per altre informazioni, vedere [confronto tra tipi di eventi live](live-event-types-comparison.md).

Le risoluzioni e le velocità in bit contenute nell'output del codificatore Live sono determinate dal set di impostazioni. Se si usa un codificatore Live **standard** (LiveEventEncodingType. standard), il set di impostazioni *Default720p* specifica un set di sei coppie di velocità di risoluzione/bit, passando da 720p a 3,5 Mbps fino a 192p a 200 Kbps. In caso contrario, se si usa un codificatore Live **Premium1080p** (LiveEventEncodingType. Premium1080p), il set di impostazioni *Default1080p* specifica un set di sei coppie di velocità di risoluzione/bit, passando da 1080p a 3,5 Mbps fino a 180p a 200 Kbps. Per altre informazioni, vedere [Set di impostazioni di sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite portale di Azure. Consente di specificare la tabella desiderata per la risoluzione e la velocità in bit. Verificare che sia presente un solo livello a 720p (se si richiede un set di impostazioni per un codificatore Live standard) o 1080p (se si richiede un set di impostazioni per un codificatore Live Premium1080p) e 6 livelli al massimo.

## <a name="creating-live-events"></a>Creazione di eventi Live

### <a name="options"></a>Opzioni

Quando si crea un evento live, è possibile specificare le opzioni seguenti:

* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione del protocollo mentre l'evento Live o gli output Live associati sono in esecuzione. Se sono necessari protocolli diversi, creare un evento Live separato per ogni protocollo di streaming.  
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. In alternativa, è possibile avviare l'evento quando si è pronti per avviare lo streaming.

    Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).

* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: Indirizzo IpV4 con quattro numeri o intervallo di indirizzi CIDR.

    Se si vuole abilitare determinati IP sui firewall o si vuole vincolare gli input agli eventi live agli indirizzi IP di Azure, scaricare un file JSON da intervalli di [indirizzi IP del Data Center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Per informazioni dettagliate su questo file, selezionare la sezione **Dettagli** nella pagina.

### <a name="naming-rules"></a>Regole di denominazione

* Il nome dell'evento Live massimo è di 32 caratteri.
* Il nome deve seguire questo modello [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Vedere anche le [convenzioni di denominazione degli endpoint di streaming](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Per garantire l'univocità del nome dell'evento Live, è possibile generare un GUID, quindi rimuovere tutti i trattini e le parentesi graffe (se presenti). La stringa sarà univoca in tutti gli eventi live e la relativa lunghezza verrà garantita 32.

## <a name="live-event-ingest-urls"></a>URL di inserimento di eventi live

Una volta creato l'evento Live, è possibile ottenere gli URL di inserimento da fornire al codificatore locale. Questi URL vengono usati dal codificatore live per inserire un flusso live. Per altre informazioni, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md).

È possibile usare URL di non reindirizzamento a microsito o URL di reindirizzamento a microsito.

> [!NOTE] 
> Per un URL di inserimento predittivo, impostare la modalità di "reindirizzamento a microsito".

* URL di non reindirizzamento a microsito

    L'URL non-Vanity è la modalità predefinita in servizi multimediali V3. È possibile ottenere l'evento live rapidamente, ma l'URL di inserimento è noto solo all'avvio dell'evento live. L'URL verrà modificato se si arresta/avvia l'evento live. <br/>Non-Vanity è utile negli scenari in cui un utente finale vuole eseguire lo streaming usando un'app in cui l'app vuole ottenere un evento live al più presto e avere un URL di inserimento dinamico non è un problema.

    Se un'app client non deve pre-generare un URL di inserimento prima della creazione dell'evento Live, consentire a servizi multimediali di generare automaticamente il token di accesso per l'evento Live.

* URL di reindirizzamento a microsito

    La modalità Vanity è preferibile ai broadcaster multimediali di grandi dimensioni che usano codificatori broadcast hardware e non vogliono riconfigurare i codificatori quando avviano l'evento Live. Vogliono un URL di inserimento predittivo, che non cambia nel tempo.

    Per specificare questa modalità, impostare `vanityUrl` su `true` in fase di creazione (il valore predefinito è `false`). È anche necessario passare il proprio token di accesso (`LiveEventInput.accessToken`) al momento della creazione. Il valore del token viene specificato per evitare un token casuale nell'URL. Il token di accesso deve essere una stringa GUID valida (con o senza trattini). Una volta impostata la modalità, non è possibile aggiornarla.

    Il token di accesso deve essere univoco nel data center. Se l'app deve usare un URL di Vanity, è consigliabile creare sempre una nuova istanza GUID per il token di accesso, anziché riusare un GUID esistente.

    Usare le API seguenti per abilitare l'URL di Vanity e impostare il token di accesso su un GUID valido, ad esempio `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.  

    |Lingua|Abilita URL Vanity|Impostare il token di accesso|
    |---|---|---|
    |REST|[Properties. vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--Vanity-URL](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--Access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[Live. VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regole di denominazione degli URL di inserimento live

* La stringa *casuale* sottostante è un numero esadecimale a 128 bit (costituito da 32 caratteri 0-9 a-f).
* il *token di accesso*: la stringa GUID valida impostata quando si usa la modalità Vanity. Ad esempio, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Nome flusso*: indica il nome del flusso per una connessione specifica. Il valore del nome del flusso viene in genere aggiunto dal codificatore Live usato. È possibile configurare il codificatore Live in modo che usi qualsiasi nome per descrivere la connessione, ad esempio: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>URL di non reindirizzamento a microsito

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL di reindirizzamento a microsito

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL di anteprima di un evento live

Una volta che l'evento live inizia a ricevere il feed di contributo, è possibile usare l'endpoint di anteprima per visualizzare in anteprima e convalidare la ricezione del flusso live prima dell'ulteriore pubblicazione. Dopo aver verificato che il flusso di anteprima è valido, è possibile usare l'evento Live per rendere il flusso Live disponibile per il recapito tramite uno o più endpoint di streaming (creati in precedenza). A tale scopo, creare un nuovo [output Live](https://docs.microsoft.com/rest/api/media/liveoutputs) nell'evento Live.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

## <a name="live-event-long-running-operations"></a>Operazioni con esecuzione prolungata dell'evento Live

Per informazioni dettagliate, vedere [operazioni con esecuzione prolungata](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un [asset](https://docs.microsoft.com/rest/api/media/assets), un [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) e un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Per informazioni dettagliate sugli output Live, vedere [uso di un DVR cloud](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
