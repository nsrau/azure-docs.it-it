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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605660"
---
# <a name="live-events-and-live-outputs"></a>Eventi live e output live

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per configurare gli eventi di streaming live in servizi multimediali v3, è necessario comprendere i concetti illustrati in questo articolo.

> [!TIP]
> Per i clienti di eseguire la migrazione dalle API di servizi multimediali v2, il **evento Live** entità sostituisce **canale** v2 e **Output Live** sostituisce **programma**.

## <a name="live-events"></a>Eventi live

Gli [eventi live](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento Live, viene creato un endpoint di input primario e secondario che è possibile usare per inviare un segnale in tempo reale da un codificatore remoto. Il codificatore live remoto invia il contributo feed a tale input endpoint usando il [RTMP](https://www.adobe.com/devnet/rtmp.html) oppure [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protocollo di input (MP4 frammentato). Per il protocollo di inserimento RTMP, il contenuto può essere inviato in chiaro (`rtmp://`) o in modo sicuro crittografati in transito (`rtmps://`). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`.  

## <a name="live-event-types"></a>Tipi di evento live

Un [evento live](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: pass-through e codifica live. I tipi vengono impostati durante la creazione usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -un codificatore live locale invia un flusso a velocità in bit più. I flussi inseriti passano attraverso l'evento Live senza altre elaborazioni. 
* **LiveEventEncodingType.Standard** : un codificatore live invia un flusso a bitrate singolo con l'evento Live e servizi multimediali consente di creare flussi a bitrate multipli on-premise. Se il feed di contributo per 720p o risoluzione superiore, il **Default720p** preimpostato codificheranno un set di coppie di risoluzione/velocità in bit 6.
* **LiveEventEncodingType.Premium1080p** : un codificatore live invia un flusso a bitrate singolo con l'evento Live e servizi multimediali consente di creare flussi a bitrate multipli on-premise. Il set di impostazioni Default1080p specifica il set di output di coppie di risoluzione/velocità in bit. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando si usa l'**evento live** pass-through, al codificatore live locale è affidata la generazione di un flusso video con velocità in bit multipla e l'invio come feed di contributi all'evento live (tramite il protocollo RTMP o MP4 frammentato). L'evento live esegue quindi i flussi video in ingresso senza ulteriori elaborazioni. Questo tipo un pass-through evento Live è ottimizzato per gli eventi in tempo reale con esecuzione prolungata o lo streaming live 24 x 365 lineare. Quando si crea questo tipo di evento live, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2).  Vedere l'articolo [Confronto tra tipi di eventi live](live-event-types-comparison.md) per altri dettagli.

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per realizzare lo streaming live quando si eseguono più eventi per un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Per un esempio di codice .NET, vedere [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codifica live  

![codifica live](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica live con Servizi multimediali, è possibile configurare il codificatore live locale per l'invio di un video a bitrate singolo come feed di contributi all'evento live (tramite il protocollo RTMP o Mp4 frammentato). Impostare quindi un evento Live, in modo che la codifica a bitrate singolo con tale in ingresso streaming in un [flusso video a velocità in bit più](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e rende l'output disponibile per il recapito di riprodurre i dispositivi tramite i protocolli, ad esempio MPEG-DASH, HLS e Smooth Lo streaming.

Quando si usa la codifica live, è possibile inviare il contributo feed solo con risoluzioni risoluzione con risoluzione 1080p e una frequenza dei fotogrammi di 30 fotogrammi/secondo, con codec video video H.264/AVC e AAC (AAC-LC, HE-AACv1 o HE-AACv2) codec audio. Si noti che gli eventi Live di pass-through può supportare le risoluzioni fino a 4 KB su 60 fotogrammi al secondo. Vedere l'articolo [Confronto tra tipi di eventi live](live-event-types-comparison.md) per altri dettagli.

Le risoluzioni e velocità in bit contenuti nell'output dal codificatore live è determinato dal set di impostazioni. Se si usa un' **Standard** live encoder (LiveEventEncodingType.Standard), il *Default720p* set di impostazioni specifica un set di coppie di velocità risoluzione/bit 6, passando da 720p a 3.5Mbps down 192 p a 200 kbps. In caso contrario, se si usa un' **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), il *Default1080p* set di impostazioni specifica un set di coppie di velocità risoluzione/bit 6, passando da 1080p in 3.5Mbps fino a 180p a 200 kbps. Per altre informazioni, vedere [Set di impostazioni di sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite il portale di Azure. È necessario specificare la tabella di risoluzione e la velocità in bit desiderata. Verificare che vi sia un solo a 720p (se la richiesta di un set di impostazioni per un codificatore live Standard) o a 1080p (se la richiesta di un set di impostazioni per un codificatore live Premium1080p) e al massimo 6 livelli.

## <a name="live-event-creation-options"></a>Opzioni di creazione di eventi live

Quando si crea un evento live, è possibile specificare le opzioni seguenti:

* Protocollo di streaming per l'evento live (attualmente, sono supportati i protocolli RTMP e Smooth Streaming).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: Indirizzo IpV4 con 4 numeri, intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. In alternativa, è possibile avviare l'evento quando si è pronti ad avviare lo streaming. 

    Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URL di inserimento di eventi live

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore locale live. Questi URL vengono usati dal codificatore live per inserire un flusso live. Per altre informazioni, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md). 

È possibile usare URL di non reindirizzamento a microsito o URL di reindirizzamento a microsito. 

> [!NOTE] 
> Per un URL di inserimento predittivo, impostare la modalità di "reindirizzamento a microsito".

* URL di non reindirizzamento a microsito

    URL non-personale è la modalità predefinita in servizi multimediali v3. È possibile ottenere l'evento live rapidamente, ma l'URL di inserimento è noto solo all'avvio dell'evento live. L'URL verrà modificato se si arresta/avvia l'evento live. <br/>Il non reindirizzamento a microsito è utile negli scenari in cui un utente finale vuole trasmettere usando un'app che vuole ottenere un evento live il prima possibile e la presenza di un URL di inserimento dinamico non costituisce un problema.
    
    Se non è necessaria un'applicazione client di pre-generare un URL di inserimento prima dell'evento Live viene creato, consentire solo a servizi multimediali per generare automaticamente il Token di accesso per l'evento live.
* URL di reindirizzamento a microsito

    La modalità di reindirizzamento a microsito è preferita dai responsabili della trasmissione di file multimediali di grandi dimensioni, che usano codificatori di trasmissione hardware e non vogliono riconfigurare i codificatori quando avviano l'evento live, ma vogliono un URL di inserimento predittivo, che non cambia nel tempo.
    
    Per specificare questa modalità, impostare `vanityUrl` al `true` al momento della creazione (valore predefinito è `false`). È necessario passare anche il proprio token di accesso (`LiveEventInput.accessToken`) al momento della creazione. Specificare il valore del token per evitare un token casuale nell'URL. Il token di accesso deve essere una stringa GUID valida (con o senza trattini). Dopo aver impostata la modalità non può essere aggiornata.

    Il token di accesso deve essere univoco nel data center. Se l'applicazione deve usare un URL personalizzato, è consigliabile creare sempre una nuova istanza GUID per il token di accesso (anziché riutilizzare qualsiasi GUID esistente). 

    Usare le API seguenti per abilitare l'URL personale e impostare il token di accesso su un GUID valido (ad esempio `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Linguaggio|Abilitare l'URL personale|Impostare il token di accesso|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regole di denominazione degli URL di inserimento live

* La stringa *casuale* sottostante è un numero esadecimale a 128 bit (costituito da 32 caratteri 0-9 a-f).
* *il token di accesso* -la stringa del GUID valida è impostato quando si usa la modalità personale. Ad esempio `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome del flusso* -indica il nome del flusso per una connessione specifica. Il valore del nome di flusso viene in genere aggiunto dal codificatore live che è utilizzare. È possibile configurare il codificatore live per utilizzare qualsiasi nome per descrivere la connessione, ad esempio: "video1_audio1", "video2_audio1", "flusso".

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

Dopo l'evento Live inizia a ricevere i feed di contributo, è possibile utilizzare l'endpoint di anteprima per visualizzare in anteprima e convalidare che si ricevano il flusso live prima di pubblicare ulteriormente. Dopo avere verificato che il flusso di anteprima è consigliabile, è possibile usare l'evento Live per rendere disponibili per il recapito tramite uno o più (creato in precedenza) endpoint di Streaming il flusso live. A tale scopo, si crea un nuovo [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) dell'evento Live. 

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

## <a name="live-event-long-running-operations"></a>Operazioni con esecuzione prolungata eventi in tempo reale

Per informazioni dettagliate, vedere [operazioni a esecuzione prolungata](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un [asset](https://docs.microsoft.com/rest/api/media/assets), un [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) e un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). L'output live archivierà il flusso e lo renderà disponibile agli utenti tramite l'[endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'output live, non si elimina l'asset sottostante e il contenuto dell'asset. 

La relazione tra un **evento Live** e il relativo **Live output** è simile al tradizionale televisione broadcast, in cui un canale (evento Live) rappresenta un flusso costante di una registrazione (dal vivo e video Output) ha come ambito un segmento di tempo specifico (ad esempio, sera notizie da 6:30 PM alle 19:00:00). È possibile registrare programmi TV tramite una registrazione di Video digitali (DVR): la funzionalità equivalente in eventi Live viene gestita tramite il **archiveWindowLength** proprietà. È un timespan ISO-8601 (ad esempio, PTHH:MM:SS), il quale specifica la capacità del DVR e può essere impostato da un minimo di 3 minuti fino a un massimo di 25 ore.

L'oggetto di Output in tempo reale è simile a un registratore che rileverà e registra il flusso live in un Asset nell'account di servizi multimediali. Il contenuto registrato verrà mantenuto nell'account di Archiviazione di Azure collegato al proprio account, nel contenitore definito dalla risorsa Asset. L'Output Live consente anche di controllare alcune proprietà del flusso in tempo reale in uscita, ad esempio quantità del flusso viene mantenuta nella registrazione archivio (ad esempio, la capacità del cloud DVR) e se gli utenti possono avviare guardano lo streaming live. L'archivio su disco non è un archivio circolare la "finestra" che contiene solo la quantità di contenuto specificato nella proprietà archiveWindowLength dell'Output in tempo reale. Il contenuto che rientra in questa finestra viene automaticamente rimosso dal contenitore di archiviazione e non è recuperabile. È possibile creare più output in tempo reale (massimo fino a tre) su un evento Live con le impostazioni e le lunghezze di archiviazione differente.  

Se è stata pubblicata l'Output Live **bene** usando un **localizzatore di Streaming**, l'evento Live (fino alla lunghezza dell'intervallo DVR) continueranno a essere visualizzato fino alla scadenza o eliminazione, il localizzatore di Streaming a seconda del valore raggiunto per primo.

Per altre informazioni, vedere [Uso di un DVR cloud](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
