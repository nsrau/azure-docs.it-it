---
title: Panoramica e scenari comuni di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento fornisce informazioni generali su Servizi multimediali di Azure
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: f22b87fc5bdfe2db5de39adaafe9c71d8c32b26a


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Panoramica e scenari comuni di Servizi multimediali di Azure

Servizi multimediali di Microsoft Azure costituisce una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni di distribuzione e gestione di contenuti multimediali altamente scalabili. Servizi multimediali si basa su API REST che consentono di caricare, archiviare e codificare in sicurezza contenuti video o audio, nonché creare pacchetti di tali contenuti per la distribuzione su richiesta e in modalità streaming live a vari tipi di client (ad esempio, TV, PC e dispositivi mobili).

È possibile creare flussi di lavoro end-to-end usando unicamente Servizi multimediali. È anche possibile usare componenti di terze parti per alcune parti del flusso di lavoro, ad esempio, la codifica con un codificatore di terze parti. Inoltre, sono possibili operazioni di caricamento, protezione, creazione di pacchetti e invio tramite Servizi multimediali.

È possibile scegliere di riprodurre in streaming i propri contenuti live o distribuire contenuti su richiesta. In questo argomento vengono illustrati scenari comuni per la distribuzione del contenuto [live](media-services-overview.md#live_scenarios) o [su richiesta](media-services-overview.md#vod_scenarios). L'argomento contiene inoltre collegamenti ad altri argomenti rilevanti.

## <a name="sdks-and-tools"></a>SDK e strumenti

Per creare soluzioni di Servizi multimediali, è possibile usare:

* [API REST di Servizi multimediali](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Uno dei client SDK disponibili:
    * [Azure Media Services SDK per .NET](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Servizi multimediali di Azure per Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js). Questa è una versione non Microsoft di Node.js SDK. È gestita da una community e attualmente non copre al 100% le API AMS.
* Strumenti esistenti:
    * [Portale di Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) è un'applicazione Winforms/C# per Windows)

L'immagine seguente illustra alcuni degli oggetti più comuni usati durante lo sviluppo rispetto al modello OData di Servizi multimediali.

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

È possibile visualizzare il modello completo [qui](https://media.windows.net/API/$metadata?api-version=2.15).  


## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
È possibile visualizzare i percorsi di apprendimento AMS qui:

* [Flusso di lavoro AMS Live Streaming](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Flusso di lavoro AMS Streaming su richiesta](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Servizi multimediali di Azure, è necessario disporre di quanto segue:

1. Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com).
2. Un account di Servizi multimediali di Azure. Usare il portale di Azure, .NET o l'API REST per creare un account di Servizi multimediali di Azure. Per altre informazioni, vedere [Creare un account](media-services-portal-create-account.md).
3. (Facoltativo) Configurare l'ambiente di sviluppo. Scegliere .NET o API REST per l'ambiente di sviluppo. Per altre informazioni, vedere [Configurare l'ambiente](media-services-dotnet-how-to-use.md).

    Sono disponibili anche informazioni su come [connettersi a livello di codice](media-services-dotnet-connect-programmatically.md).
4. Un endpoint di streaming Standard o Premium con stato avviato.  Per altre informazioni, vedere [Gestione degli endpoint di streaming](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints).

## <a name="concepts-and-overview"></a>Panoramica e concetti
Per i concetti relativi ai Servizi multimediali di Azure, vedere [Concetti su Servizi multimediali di Azure](media-services-concepts.md).

Per una serie di procedure che illustra tutti i componenti principali di Servizi multimediali di Azure, vedere la pagina relativa alle [esercitazioni dettagliate sui Servizi multimediali di Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Questa serie offre un'ottima panoramica dei concetti e usa lo strumento AMSE per illustrare le attività AMS. Si noti che AMSE è uno strumento di Windows. Supporta la maggior parte delle attività che è possibile eseguire a livello di codice con [AMS SDK per .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java) o [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

## <a name="a-idvodscenariosadelivering-media-on-demand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure: scenari e attività comuni
Questa sezione descrive scenari comuni e offre collegamenti agli argomenti pertinenti. Il seguente diagramma illustra le parti principali della piattaforma di Servizi multimediali che riguardano la fornitura di contenuti su richiesta.

![Flusso di lavoro VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**.

### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protezione dei contenuti nella risorsa di archiviazione e distribuzione dei flussi multimediali in chiaro (non crittografati)
1. Caricare un file in formato intermedio di qualità elevata in un asset.

    Si consiglia di applicare all'asset l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti sia durante il caricamento sia mentre si trovano nella risorsa di archiviazione.
2. Codificare in un set di file MP4 a velocità in bit adattiva.

    Si consiglia di applicare all'asset di output l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti anche quando non vengono usati.
3. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti).

    Se l'asset è protetto con crittografia di archiviazione, è **necessario** configurare i criteri di distribuzione degli asset.
4. Pubblicare l'asset creando un localizzatore OnDemand.
5. Trasmettere in streaming i contenuti pubblicati.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati

1. Caricare un file in formato intermedio di qualità elevata in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
2. Codificare in un set di file MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
3. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
4. Configurare i criteri di autorizzazione della chiave simmetrica.
5. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti e la crittografia dinamica).
6. Pubblicare l'asset creando un localizzatore OnDemand.
7. Trasmettere in streaming i contenuti pubblicati.

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Usare Analisi Servizi multimediali per derivare analisi approfondite di utilità pratica dai propri video
Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai propri file video. Per altre informazioni, vedere [Panoramica di Analisi Servizi multimediali di Azure](media-services-analytics-overview.md).

1. Caricare un file in formato intermedio di qualità elevata in un asset.
2. Usare uno dei servizi di Analisi Servizi multimediali seguenti per elaborare i video:

   * **Indicizzatore** : [Process videos with Azure Media Indicizzatore 2](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** : [file multimediali di Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
   * **Rilevamento movimento** : [rilevamento del movimento per Analisi Servizi multimediali di Azure](media-services-motion-detection.md).
   * **Rilevamento viso ed emozioni** : [rilevamento dei visi e delle emozioni per Analisi Servizi multimediali di Azure](media-services-face-and-emotion-detection.md).
   * **Riepilogo video** : [uso di anteprime video multimediali di Azure per creare un riepilogo video](media-services-video-summarization.md)
3. I processori di contenuti multimediali di Analisi Servizi multimediali producono file MP4 o JSON. Se un processore di contenuti multimediali produce un file MP4, è possibile scaricare progressivamente il file. Se un processore di contenuti multimediali produce un file JSON, è possibile scaricare il file dall'archiviazione BLOB di Azure.

### <a name="deliver-progressive-download"></a>Definizione del download progressivo
1. Caricare un file in formato intermedio di qualità elevata in un asset.
2. Codificare in un singolo file MP4.
3. Pubblicare l'asset creando un localizzatore OnDemand o SAS.

    Se si usa un localizzatore SAS, i contenuti vengono scaricati dall'archiviazione BLOB di Azure. In questo caso, non è necessario avere endpoint di streaming con stato avviato.
4. Eseguire il download progressivo.

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Distribuzione di eventi Live Streaming con Servizi multimediali di Azure
Quando si usa Live Streaming sono generalmente necessari i seguenti componenti:

* Una fotocamera da usare per trasmettere un evento.
* Un codificatore video attivo in grado di convertire i segnali provenienti dalla fotocamera in flussi inviati a un servizio di streaming live.

Facoltativamente, più codificatori live con sincronizzazione dell'ora. In caso di eventi live critici per i quali sono richiesti livelli molto elevati di disponibilità e qualità dell'esperienza, è consigliabile usare codificatori ridondanti di tipo attivo-attivo con sincronizzazione dell'ora per ottenere un failover efficiente senza perdita di dati.

* Un servizio di streaming live che consenta di effettuare le seguenti operazioni:
* inserire contenuti live usando vari protocolli di streaming live (ad esempio RTMP o Smooth Streaming),
* (facoltativamente) codificare il flusso per il flusso a bitrate adattivo,
* visualizzare in anteprima il flusso live,
* registrare e archiviare il contenuto inserito perché possa essere riprodotto in streaming in un secondo momento (video on demand),
* usare protocolli di streaming comuni (ad esempio, MPEG DASH, Smooth, HLS) per trasmettere i contenuti direttamente ai clienti o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per una successiva ridistribuzione.

**Servizi multimediali di Microsoft Azure** (AMS) offre la possibilità di inserire, visualizzare in anteprima, archiviare e fornire il contenuto in streaming live.

Quando si distribuiscono contenuti ai clienti, l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete. Per garantire la qualità e le condizioni di rete, usare codificatori live per codificare il flusso video a più velocità in bit (velocità in bit adattiva).  Per garantire la trasmissione a diversi tipi di dispositivi, usare la funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md) di Servizi multimediali per riorganizzare dinamicamente il flusso in nuovi pacchetti creati con protocolli diversi. Servizi multimediali supporta le tecnologie di streaming a bitrate adattivo seguenti: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

In Servizi multimediali di Azure, la gestione di tutte le funzionalità di live streaming è affidata a entità **Channel**, **Program** e **StreamingEndpoint**, compresi inserimento, formattazione, DVR, sicurezza, scalabilità e ridondanza.

Un **canale** rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Un canale può ricevere i flussi di input live nei modi seguenti:

* Un codificatore live locale invia un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a bitrate multipli al canale configurato per il recapito **pass-through**. Il recapito **pass-through** avviene quando i flussi inseriti passano attraverso i **canali** senza altre transcodifiche o codifiche. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a bitrate multipli: MediaExcel, Imagine Communications, Ateme, Envivio, Cisco ed Elemental. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Live Encoder, Haivision, Telestream Wirecast, Teradek e codificatori Tricaster.  Un codificatore live può anche inviare un flusso a singola velocità in bit a un canale non abilitato per la codifica live, ma questa operazione non è consigliata. Quando richiesto, Servizi multimediali invia il flusso ai clienti.

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per realizzare lo streaming live quando si eseguono più eventi per un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
>
>

* Un codificatore live locale invia un flusso a velocità in bit singola al canale abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei seguenti formati: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 frammentato). Il canale esegue quindi la codifica live del flusso in ingresso a velocità in bit singola in un flusso video a più velocità in bit (adattivo). Quando richiesto, Servizi multimediali invia il flusso ai clienti.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Uso di canali che ricevono il flusso live a velocità in bit multipla da codificatori locali con il metodo pass-through
Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolte nel flusso di lavoro **pass-through** .

![Flusso di lavoro live][live-overview2]

Per altre informazioni, vedere l'articolo relativo all' [uso di canali che ricevono il flusso live a velocità in bit multipla da codificatori locali](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure
Il seguente diagramma mostra i componenti principali della piattaforma AMS interessati dal flusso di lavoro di streaming live dove un canale è abilitato a eseguire la codifica live con Servizi multimediali.

![Flusso di lavoro live][live-overview1]

Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Utilizzo di contenuto
Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box. Il seguente argomento fornisce collegamenti a SDK e Player Framework che è possibile usare per sviluppare le proprie applicazioni client in modo che utilizzino i flussi multimediali da Servizi multimediali.

[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Abilitazione della rete CDN di Azure
Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Ridimensionamento di un account di Servizi multimediali

È possibile ridimensionare **Servizi multimediali** specificando il numero di **unità riservate di streaming** e **unità riservate di codifica** di cui si vuole eseguire il provisioning nell'account in uso.

È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per espandere lo spazio di archiviazione oltre i limiti predefiniti, è possibile scegliere di collegare più account di archiviazione a un singolo account di Servizi Multimediali.
I clienti di Servizi multimediali scelgono un endpoint di streaming **Standard** oppure uno o più endpoint di streaming **Premium**, in base alle esigenze. L'endpoint di streaming Standard è adatto per la maggior parte dei carichi di lavoro di streaming. Include le stesse funzionalità delle unità di streaming Premium.
L'endpoint di streaming Standard è adatto per la maggior parte dei carichi di lavoro di streaming. Se il carico di lavoro è avanzato o i requisiti della capacità di streaming non rispondono alle destinazioni con la velocità effettiva degli endpoint di streaming Standard o si vuole controllare la capacità del servizio StreamingEndpoint per gestire le esigenze in crescita in termini di larghezza di banda regolando le unità di scala (note anche come unità di streaming Premium), è consigliabile allocare unità di scala.

[Questo](media-services-portal-scale-streaming-endpoints.md) argomento include collegamenti agli argomenti rilevanti.

## <a name="support"></a>Supporto
[supporto tecnico di Azure](https://azure.microsoft.com/support/options/) fornisce opzioni di supporto per Azure, compreso Servizi multimediali.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)
* Per il servizio di codifica di Servizi multimediali, è garantita una disponibilità al 99,9% delle transazioni delle API REST.
* Con l'acquisto di un endpoint di streaming Standard o Premium, per il servizio di streaming, è garantita una disponibilità al 99,9% per i contenuti multimediali esistenti.
* Per i canali live, è garantito che i canali in esecuzione avranno connettività esterna per almeno il 99,9% del tempo.
* Per la protezione del contenuto, è garantita l'evasione delle richieste di chiavi per almeno il 99,9% del tempo.
* Per l'indicizzatore, è garantito che verranno soddisfatte le richieste di attività dell'indicizzatore elaborate con un'unità riservata di codifica per il 99,9% del tempo.

Per altre informazioni, vedere [Contratto di servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO2-->


