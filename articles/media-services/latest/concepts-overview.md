---
title: Terminologia di servizi multimediali di Azure e i concetti - Azure | Microsoft Docs
description: In questo argomento offre una breve panoramica dei concetti e terminologia di servizi multimediali di Azure e fornisce collegamenti ad ulteriori dettagli.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8a4ffdc09cb12f7a16173c86bd3d0e3aee78d46d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152637"
---
# <a name="media-services-concepts"></a>Concetti relativi a servizi multimediali

In questo argomento offre una breve panoramica dei concetti e terminologia di servizi multimediali di Azure. L'articolo include anche collegamenti ad articoli con spiegazione approfondita di servizi multimediali v3 concetti e funzionalità. 

Prima di iniziare lo sviluppo, è consigliabile esaminare i concetti fondamentali descritti in questi argomenti.

> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](developers-guide.md) supportati.

## <a name="terminology"></a>Terminologia

In questa sezione illustra alcuni termini di settore common mapping all'API servizi multimediali v3.

### <a name="live-event"></a>Evento live

Oggetto **evento Live** rappresenta una pipeline per l'inserimento, la transcodifica (facoltativamente) e l'assemblaggio di flussi in tempo reale dei metadati video, audio e in tempo reale.

Per i clienti di eseguire la migrazione dalle API di servizi multimediali v2, la **evento Live** sostituisce il **canale** entità nella versione 2. Per altre informazioni, vedere [eseguire la migrazione dalla v2 alla v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Endpoint di streaming (creazione di pacchetti e l'origine)

Oggetto **Endpoint di Streaming** rappresenta un dinamico (just-in-time) creazione di pacchetti e l'origine del servizio che può distribuire contenuti live e on demand direttamente a un'applicazione di lettore client, usando uno dei più comuni supporti protocolli di streaming (HLS o un trattino). Inoltre, il **Endpoint di Streaming** fornisce la crittografia dinamica (just-in-time) per soluzioni DRM leader del settore.

Nel supporto di streaming del settore, questo servizio è noto come un **Packager** oppure **Origin**.  Altri termini comuni nel settore per offrire questa funzionalità includono JITP (Just-in-tempo-packager) o JITE (Just-in-tempo-crittografia). 
 
## <a name="cloud-upload-and-storage"></a>Caricamento e archiviazione nel cloud

Per avviare la gestione, la crittografia, la codifica, l'analisi e lo streaming di contenuti multimediali in Azure, è necessario creare un account servizi multimediali e caricare i file digitali in **asset**.

- [Caricamento e archiviazione nel cloud](storage-account-concept.md)
- [Concetto di asset](assets-concept.md)

## <a name="encoding"></a>Codifica

Dopo aver caricato i file multimediali digitali di alta qualità nell'asset, è possibile codificarli in formati che possono essere riprodotti su un'ampia gamma di browser e dispositivi. 

Per eseguire la codifica con servizi multimediali v3, è necessario creare **trasforma** e **processi**.

![Trasformazioni](./media/encoding/transforms-jobs.png)

- [Trasformazioni e processi](transforms-jobs-concept.md)
- [Codifica con servizi multimediali](encoding-concept.md)

## <a name="media-analytics"></a>Analisi di contenuti multimediali

Per analizzare i file audio e video, è anche necessario creare **trasforma** e **processi**.

- [L'analisi dei file audio e video](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Creazione di pacchetti, distribuzione, protezione

Una volta il contenuto viene codificato, è possibile sfruttare **creazione dinamica dei pacchetti**. In servizi multimediali, una **Endpoint di Streaming**  /origine si trova il servizio di creazione dinamica dei pacchetti utilizzato per recapitare contenuto multimediale agli assegnatari di client. Per rendere i video nell'asset di output disponibile ai client per la riproduzione, è necessario creare un **localizzatore di Streaming** e quindi compilare gli URL di streaming. 

Quando si crea il **localizzatore di Streaming**, oltre al nome dell'asset, è necessario specificare **Streaming criteri**. **I criteri di streaming** consentono di definire i protocolli di streaming e opzioni di crittografia (se presente) per il **localizzatori di Streaming**.

Creazione dinamica dei pacchetti viene usato se si esegue lo streaming di contenuti live o on demand. Il diagramma seguente illustra lo streaming on demand con flusso di lavoro di creazione dinamica dei pacchetti.

![Creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Con servizi multimediali, è possibile distribuire contenuti live e on demand crittografati dinamicamente con Advanced Encryption Standard (AES-128) o / e uno dei tre principali digital rights management (DRM) sistemi: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

Se si specificano le opzioni di crittografia nel flusso, creare il **criteri di chiave simmetrica** e associarlo con i **localizzatore di Streaming**. Il **criteri di contenuto chiave** consente di configurare la modalità la chiave simmetrica venga distribuita per i client finali.

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Proteggere il contenuto](./media/content-protection/content-protection.svg)

&#42;supporta la crittografia dinamica AES-128 "chiave non crittografata", CBCS e CENC. 

È possibile usare servizi multimediali **manifesti dinamici** trasmettere solo un rendering specifico o sottoclip di un video. Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset così codificato può essere quindi riorganizzato dinamicamente in pacchetti creati con uno dei seguenti protocolli di streaming: HLS, MPEG DASH e Smooth.  Nella parte superiore del diagramma è riportato il manifesto HLS per l'asset senza filtri (contiene tutti i sette rendering),  mentre in basso a sinistra è riportato il manifesto HLS con applicato un filtro denominato "ott". Il filtro "ott" indica che devono essere rimosse tutte le velocità in bit inferiori a 1 Mbps. L'applicazione di questo filtro ha generato i due livelli di qualità inferiori rimossi nella risposta. In basso a destra è riportato invece il manifesto HLS con applicato un filtro denominato "mobile". Il filtro "mobile" indica che devono essere rimossi tutti i rendering con risoluzione superiore a 720p. L'applicazione di questo filtro ha generato i due rendering 1080p rimossi.

![Filtro di rendering](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Creazione dinamica dei pacchetti](dynamic-packaging-overview.md)
- [Endpoint di streaming](streaming-endpoint-concept.md)
- [Localizzatori di streaming](streaming-locators-concept.md)
- [Criteri di streaming](streaming-policy-concept.md)
- [Criteri di chiave simmetrica](content-key-policy-concept.md)
- [Protezione del contenuto](content-protection-overview.md)
- [Manifesti dinamici](filters-dynamic-manifest-overview.md)
- [Filtri](filters-concept.md)

## <a name="live-streaming"></a>Streaming live

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Gli **eventi live** sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea una **evento Live**, viene creato un endpoint di input che è possibile usare per inviare un segnale in tempo reale da un codificatore remoto. Dopo aver ottenuto il flusso nel **evento Live**, è possibile iniziare l'evento di streaming creando un **Asset**, **Output Live**, e **localizzatore di Streaming** . **Output Live** archivierà il flusso nel **Asset** e renderla disponibile agli utenti tramite la **Endpoint di Streaming**. Oggetto **evento Live** può essere uno dei due tipi: **pass-through** e **della codifica live**.

L'immagine seguente illustra il flusso di lavoro di tipo pass-through:

![pass-through](./media/live-streaming/pass-through.svg)

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Eventi live e output live](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitoraggio

### <a name="event-grid"></a>Griglia di eventi

Per visualizzare lo stato di avanzamento del processo, è consigliabile usare **griglia di eventi**. Servizi multimediali genera anche i tipi di eventi in tempo reale. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. 

- [Gestione degli eventi di griglia di eventi](reacting-to-media-services-events.md)
- [Schemi](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Monitoraggio di Azure

Monitorare le metriche e log di diagnostica che consentono di comprendere come le applicazioni eseguono con monitoraggio di Azure.

- [Le metriche e log di diagnostica](media-services-metrics-diagnostic-logs.md)
- [Schemi dei log di diagnostica](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clienti più importanti

È possibile usare Azure Media Player per riprodurre contenuti multimediali trasmessi da servizi multimediali in un'ampia gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa. 

- [Panoramica di Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire commenti e suggerimenti, ottenere gli aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Codificare il file remoto e trasmettere il video in streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificare il file caricato e trasmettere il video in streaming - .NET](stream-files-tutorial-with-api.md)
* [Streaming live - .NET](stream-live-tutorial-with-api.md)
* [Analizzare i video - .NET](analyze-videos-tutorial-with-api.md)
* [Crittografia dinamica AES-128 - .NET](protect-with-aes128.md)
* [Crittografare in modo dinamico il contenuto con più tecnologie DRM - .NET](protect-with-drm.md) 
