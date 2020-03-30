---
title: Concetti relativi a eventi live e output in tempo reale in Servizi multimediali di Azure
titleSuffix: Azure Media Services
description: Questo argomento offre una panoramica degli eventi live e degli output live in Servizi multimediali di Azure versione 3.
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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e6f2ad2c5c30e3c75e8d3588e386ea14e8e3350b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065955"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventi dal vivo e output live nei servizi multimediali

Servizi multimediali di Azure consente di distribuire eventi live ai clienti nel cloud di Azure.Azure Media Services lets you deliver live events to your customers on the Azure cloud. Per configurare gli eventi di streaming live in Servizi multimediali v3, è necessario comprendere i concetti illustrati in questo articolo.

> [!TIP]
> Per i clienti che eseguono la migrazione dalle API di Servizi multimediali v2, l'entità **Evento live** sostituisce **Canale** in v2 e **Live Output** sostituisce **Programma**.

## <a name="live-events"></a>Eventi live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento live, viene creato un endpoint di input primario e secondario che è possibile utilizzare per inviare un segnale live da un codificatore remoto. Il codificatore live remoto invia il feed di contributo all'endpoint di input utilizzando il protocollo di input [RTMP](https://www.adobe.com/devnet/rtmp.html) o [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmented-MP4). Per il protocollo di inserimento RTMP, il`rtmp://`contenuto può essere inviato in`rtmps://`clear ( ) o crittografato in modo sicuro sul wire( ). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`.  

## <a name="live-event-types"></a>Tipi di evento live

Un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o una *codifica live* (un codificatore live locale invia un singolo flusso a bitrate). I tipi vengono impostati durante la creazione utilizzando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None:** un codificatore live locale invia un flusso a bitrate multiplo. Il flusso ingerita passa attraverso l'evento live senza ulteriori elaborazioni. Chiamata anche la modalità pass-through.
* **LiveEventEncodingType.Standard:** un codificatore live locale invia un singolo flusso di bitrate a Live Event e Servizi multimediali crea più flussi di bitrate. Se il feed di contribuzione ha una risoluzione pari o superiore a 720p, il predefinito **Default720p** codificherà un set di 6 coppie risoluzione/bitrates.
* **LiveEventEncodingType.Premium1080p:** un codificatore live locale invia un singolo flusso di bitrate a Live Event e Servizi multimediali crea più flussi di bitrate. Il predefinito Default1080p preimpostato specifica il set di output delle coppie risoluzione/bitrate.

### <a name="pass-through"></a>Pass-through

![Diagramma di esempio pass-through Live Event with Media Services](./media/live-streaming/pass-through.svg)

Quando si usa l'**evento live** pass-through, al codificatore live locale è affidata la generazione di un flusso video con velocità in bit multipla e l'invio come feed di contributi all'evento live (tramite il protocollo RTMP o MP4 frammentato). L'evento live esegue quindi i flussi video in ingresso senza ulteriori elaborazioni. Tale evento live pass-through è ottimizzato per eventi live a esecuzione prolungata o 24x365 live streaming lineare. Quando si crea questo tipo di evento live, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Per ulteriori informazioni, consultate Confronto tra [i tipi di evento live.](live-event-types-comparison.md)

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per eseguire lo streaming live quando si esegue più eventi per un lungo periodo di tempo e sono già stati investiti in codificatori locali. Vedi i dettagli [sui prezzi.](https://azure.microsoft.com/pricing/details/media-services/)
>

Per un esempio di codice .NET, vedere [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codifica live  

![Codifica live con diagramma di esempio di Servizi multimediali](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica live con Servizi multimediali, si configura il codificatore live locale per l'invio di un singolo video in bitrate come feed di contributo all'evento Live (utilizzando il protocollo RTMP o Fragmented-Mp4). È quindi possibile impostare un evento Live in modo che costringa il flusso a bitrate singolo in ingresso in un flusso video a [più bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e renda disponibile l'output per il recapito per la riproduzione di dispositivi tramite protocolli come MPEG-DASH, HLS e Smooth Streaming.

Quando si utilizza la codifica live, è possibile inviare il feed di contribuzione solo con risoluzioni fino a una risoluzione di 1080p a una frequenza fotogrammi di 30 fotogrammi al secondo, con codec audio H.264/AVC e aAC (AAC-LC, HE-AACv1 o HE-AACv2). Si noti che gli eventi live pass-through possono supportare risoluzioni fino a 4K a 60 fotogrammi al secondo. Per ulteriori informazioni, consultate Confronto tra [i tipi di evento live.](live-event-types-comparison.md)

Le risoluzioni e le velocità in bit contenute nell'output del codificatore live sono determinate dal preset. Se si utilizza un codificatore live **Standard** (LiveEventEncodingType.Standard), il preset *Default720p* specifica un set di sei coppie di risoluzione/velocità in bit, passando da 720p a 3,5 Mbps fino a 192p a 200 kbps. In caso contrario, se si utilizza un codificatore live **Premium1080p** (LiveEventEncodingType.Premium1080p), il predefinito *Default1080p* specifica un set di sei coppie di risoluzione/velocità in bit, passando da 1080p a 3,5 Mbps fino a 180p a 200 kbps. Per altre informazioni, vedere [Set di impostazioni di sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Se è necessario personalizzare il predefinito di codifica live, aprire un ticket di supporto tramite il portale di Azure.If you need to customize the live encoding preset, open a support ticket via Azure portal. Specificare la tabella di risoluzione e la velocità in bit desiderata. Verifica che ci sia un solo livello a 720p (se richiedi un preset per un codificatore live Standard) o a 1080p (se richiedi un preset per un codificatore live Premium1080p) e 6 livelli al massimo.

## <a name="creating-live-events"></a>Creazione di eventi live

### <a name="options"></a>Opzioni

Quando si crea un evento live, è possibile specificare le opzioni seguenti:

* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, creare un evento live separato per ogni protocollo di streaming.  
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. In alternativa, puoi avviare l'evento quando sei pronto per iniziare lo streaming.

    Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).

* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei seguenti formati: indirizzo IpV4 con quattro numeri o intervallo di indirizzi CIDR.

    Se si desidera abilitare determinati indirizzi IP nei propri firewall o si desidera vincolare gli input agli eventi live agli indirizzi IP di Azure, scaricare un file JSON dagli intervalli di indirizzi IP del [data center di Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Per informazioni dettagliate su questo file, selezionare la sezione **Dettagli** nella pagina.
    
* Quando crei l'evento, puoi scegliere di attivare le trascrizioni live. <br/> Per impostazione predefinita, la trascrizione live è disabilitata. Non è possibile modificare questa proprietà mentre l'evento live o gli output dinamici associati sono in esecuzione. 

### <a name="naming-rules"></a>Regole di denominazione

* Il nome massimo dell'evento live è di 32 caratteri.
* Il nome deve seguire questo `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`modello di espressione [regolare:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Vedere anche Convenzioni di [denominazione degli endpoint](streaming-endpoint-concept.md#naming-convention)di streaming.

> [!TIP]
> Per garantire l'univocità del nome dell'evento dal vivo, è possibile generare un GUID e quindi rimuovere tutti i trattini e le parentesi graffe (se presenti). La stringa sarà unica in tutti gli eventi live e la sua lunghezza è garantita per essere 32.

## <a name="live-event-ingest-urls"></a>URL di inserimento di eventi live

Una volta creato l'evento live, puoi ottenere gli URL di inserimento che fornirai al codificatore locale in tempo reale. Questi URL vengono usati dal codificatore live per inserire un flusso live. Per altre informazioni, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md).

È possibile usare URL di non reindirizzamento a microsito o URL di reindirizzamento a microsito.

> [!NOTE] 
> Per un URL di inserimento predittivo, impostare la modalità di "reindirizzamento a microsito".

* URL di non reindirizzamento a microsito

    Url non vanity è la modalità predefinita in Servizi multimediali v3. È possibile ottenere l'evento live rapidamente, ma l'URL di inserimento è noto solo all'avvio dell'evento live. L'URL verrà modificato se si arresta/avvia l'evento live. <br/>La non-Vanity è utile negli scenari in cui un utente finale desidera eseguire lo streaming usando un'app in cui l'app vuole ottenere un evento live al più presto e avere un URL di inserimento dinamico non è un problema.

    Se un'app client non deve generare un URL di inserimento prima della creazione dell'evento live, consenti a Servizi multimediali di generare automaticamente il token di accesso per l'evento live.

* URL di reindirizzamento a microsito

    La modalità Vanity è preferita dalle grandi emittenti multimediali che utilizzano encoder di trasmissione hardware e non vogliono riconfigurare i propri encoder quando avviano l'evento live. Vogliono un URL di inserimento predittivo, che non cambia nel tempo.

    Per specificare questa `vanityUrl` modalità, impostare `true` `false`su al momento della creazione (il valore predefinito è ). È inoltre necessario passare il`LiveEventInput.accessToken`proprio token di accesso ( ) al momento della creazione. Specificare il valore del token per evitare un token casuale nell'URL. Il token di accesso deve essere una stringa GUID valida (con o senza trattini). Una volta impostata, la modalità non può essere aggiornata.

    Il token di accesso deve essere univoco nel data center. Se l'app deve usare un URL di vanità, è consigliabile creare sempre una nuova istanza GUID per il token di accesso (anziché riutilizzare qualsiasi GUID esistente).

    Utilizzare le API seguenti per abilitare l'URL Vanity e impostare `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`il token di accesso su un GUID valido, ad esempio .  

    |Linguaggio|Abilita URL vanity|Impostare il token di accesso|
    |---|---|---|
    |REST|[proprietà.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[Evento live.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regole di denominazione degli URL di inserimento live

* La stringa *casuale* sottostante è un numero esadecimale a 128 bit (costituito da 32 caratteri 0-9 a-f).
* *token*di accesso : la stringa GUID valida impostata quando si utilizza la modalità vanity. Ad esempio: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome flusso*: Indica il nome del flusso per una connessione specifica. Il valore del nome del flusso viene in genere aggiunto dal codificatore live utilizzato. È possibile configurare il codificatore live in modo che utilizzi qualsiasi nome per descrivere la connessione, ad esempio: "video1_audio1", "video2_audio1", "stream".

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

Una volta che l'evento live inizia a ricevere il feed di contribuzione, puoi utilizzare il relativo endpoint di anteprima per visualizzare in anteprima e convalidare che stai ricevendo il live streaming prima di un'ulteriore pubblicazione. Dopo aver verificato che il flusso di anteprima è valido, puoi usare l'evento live per rendere il live streaming disponibile per la distribuzione tramite uno o più endpoint di streaming (precreati). A tale scopo, creare un nuovo [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) nell'evento live.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

## <a name="live-event-long-running-operations"></a>Operazioni a esecuzione prolungata di eventi in tempo reale

Per informazioni dettagliate, vedere [Operazioni a esecuzione prolungata.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un [asset](https://docs.microsoft.com/rest/api/media/assets), un [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) e un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Per informazioni dettagliate sugli output in tempo reale, consultate [Utilizzo di un DVR cloud.](live-event-cloud-dvr.md)

## <a name="frequently-asked-questions"></a>Domande frequenti

Vedere l'articolo [Domande frequenti.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Poni domande e ricevi aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
