---
title: Terminologia e concetti di servizi multimediali di Azure-Azure | Microsoft Docs
description: Questo argomento fornisce una breve panoramica dei concetti e della terminologia di servizi multimediali di Azure e fornisce collegamenti per ulteriori dettagli.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910303"
---
# <a name="media-services-concepts"></a>Concetti relativi a servizi multimediali

Questo argomento fornisce una breve panoramica dei concetti e della terminologia di servizi multimediali di Azure. Questo articolo fornisce anche collegamenti ad articoli con una spiegazione approfondita dei concetti e delle funzionalità di servizi multimediali V3. 

Prima di iniziare lo sviluppo, è consigliabile esaminare i concetti fondamentali descritti in questi argomenti.

> [!NOTE]
> Non è attualmente possibile usare il portale di Azure per gestire le risorse v3. Usare l'[API REST](https://aka.ms/ams-v3-rest-ref), l'[interfaccia della riga di comando](https://aka.ms/ams-v3-cli-ref) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

## <a name="terminology"></a>Terminologia

Questa sezione illustra il mapping di alcuni termini comuni di settore all'API di servizi multimediali V3.

### <a name="live-event"></a>Evento live

Un **evento Live** rappresenta una pipeline per l'inserimento, la transcodifica (facoltativamente) e la creazione di pacchetti di flussi live di video, audio e metadati in tempo reale.

Per i clienti che eseguono la migrazione dalle API di servizi multimediali V2, l' **evento Live** sostituisce l'entità del **canale** nella versione V2. Per ulteriori informazioni, vedere [migrazione da V2 a V3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Endpoint di streaming (creazione di pacchetti e origine)

Un **endpoint di streaming** rappresenta un servizio di creazione e creazione di pacchetti dinamici (just-in-Time) in grado di fornire contenuti live e on demand direttamente a un'applicazione lettore client, usando uno dei protocolli di streaming media comuni (HLS o Dash). Inoltre, l' **endpoint di streaming** fornisce la crittografia dinamica (just-in-Time) a DRM leader del settore.

Nel settore dei flussi multimediali, questo servizio viene comunemente definito come un **pacchetto** o un' **origine**.  Altri termini comuni nel settore per questa funzionalità includono JITP (just-in-Time-Packager) o JITE (just-in-time-Encryption). 
 
## <a name="cloud-upload-and-storage"></a>Caricamento e archiviazione nel cloud

Per iniziare a gestire, crittografare, codificare, analizzare e trasmettere contenuti multimediali in Azure, è necessario creare un account di servizi multimediali e caricare i file digitali in **Asset**.

- [Caricamento e archiviazione nel cloud](storage-account-concept.md)
- [Concetto di asset](assets-concept.md)

## <a name="encoding"></a>Codifica

Dopo aver caricato i file multimediali digitali di alta qualità in asset, è possibile codificarli in formati che possono essere riprodotti in un'ampia gamma di browser e dispositivi. 

Per eseguire la codifica con servizi multimediali V3, è necessario creare **trasformazioni** e **processi**.

![Trasformazioni](./media/encoding/transforms-jobs.png)

- [Trasformazioni e processi](transforms-jobs-concept.md)
- [Codifica con servizi multimediali](encoding-concept.md)

## <a name="media-analytics"></a>Analisi Servizi multimediali

Per analizzare i file audio e video, è anche necessario creare **trasformazioni** e **processi**.

- [Analisi dei file audio e video](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Creazione di pacchetti, distribuzione, protezione

Una volta codificato il contenuto, è possibile sfruttare i vantaggi della creazione **dinamica dei pacchetti**. In servizi multimediali, un **endpoint di streaming**/Origin è il servizio di creazione dinamica dei pacchetti usato per distribuire contenuti multimediali ai lettori client. Per rendere disponibili i video nell'asset di output ai client per la riproduzione, è necessario creare un **localizzatore di streaming** e quindi creare gli URL di streaming. 

Quando si crea il **localizzatore di streaming**, oltre al nome dell'asset, è necessario specificare i **criteri di streaming**. I **criteri di streaming** consentono di definire i protocolli di streaming e le opzioni di crittografia (se presenti) per i **localizzatori di streaming**.

La creazione dinamica dei pacchetti viene usata se si esegue lo streaming dei contenuti live o on demand. Il diagramma seguente illustra lo streaming su richiesta con il flusso di lavoro di creazione dinamica dei pacchetti.

![Creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Con servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con Advanced Encryption Standard (AES-128) o/e uno dei tre sistemi principali Digital Rights Management (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

Se si specificano le opzioni di crittografia nel flusso, creare i **criteri della chiave** simmetrica e associarli al **localizzatore di streaming**. I **criteri della chiave** simmetrica consentono di configurare il modo in cui la chiave simmetrica viene recapitata ai client finali.

L'immagine seguente illustra il flusso di lavoro di protezione dei contenuti di Servizi multimediali: 

![Proteggere il contenuto](./media/content-protection/content-protection.svg)

&#42;la crittografia dinamica supporta AES-128 "Clear Key", CBCS e CENC. 

È possibile usare **manifesti dinamici** di servizi multimediali per trasmettere solo una copia di rendering o clip secondari specifici del video. Nell'esempio seguente si usa un codificatore per codificare un asset in formato intermedio in sette rendering video ISO MP4 (da 180p a 1080p). L'asset così codificato può essere quindi riorganizzato dinamicamente in pacchetti creati con uno dei seguenti protocolli di streaming: HLS, MPEG DASH e Smooth.  Nella parte superiore del diagramma è riportato il manifesto HLS per l'asset senza filtri (contiene tutti i sette rendering),  mentre in basso a sinistra è riportato il manifesto HLS con applicato un filtro denominato "ott". Il filtro "ott" indica che devono essere rimosse tutte le velocità in bit inferiori a 1 Mbps. L'applicazione di questo filtro ha generato i due livelli di qualità inferiori rimossi nella risposta. In basso a destra è riportato invece il manifesto HLS con applicato un filtro denominato "mobile". Il filtro "mobile" indica che devono essere rimossi tutti i rendering con risoluzione superiore a 720p. L'applicazione di questo filtro ha generato i due rendering 1080p rimossi.

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

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Gli **eventi live** sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un **evento Live**, viene creato un endpoint di input che può essere usato per inviare un segnale Live da un codificatore remoto. Una volta che il flusso è stato trasmesso nell' **evento Live**, è possibile iniziare l'evento di streaming creando un **Asset**, un **output Live**e un **localizzatore di streaming**. L' **output in tempo reale** archivia il flusso nell' **Asset** e lo rende disponibile ai visualizzatori tramite l' **endpoint di streaming**. Un **evento Live** può essere di uno dei due tipi seguenti: **pass-through** e **codifica live**.

Nell'immagine seguente viene illustrato il flusso di lavoro del tipo pass-through:

![pass-through](./media/live-streaming/pass-through.svg)

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Eventi live e output live](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitoraggio

### <a name="event-grid"></a>Griglia eventi

Per visualizzare lo stato di avanzamento del processo, è necessario usare **griglia di eventi**. Servizi multimediali genera anche i tipi di evento Live. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. 

- [Gestione degli eventi di griglia di eventi](reacting-to-media-services-events.md)
- [Schemi](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Monitoraggio di Azure

Monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni con monitoraggio di Azure.

- [Metriche e log di diagnostica](media-services-metrics-diagnostic-logs.md)
- [Schemi dei log di diagnostica](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clienti più importanti

È possibile usare Azure Media Player per riprodurre contenuto multimediale trasmesso da servizi multimediali in un'ampia gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa. 

- [Panoramica di Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Codificare il file remoto e trasmettere il video in streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificare il file caricato e trasmettere il video in streaming - .NET](stream-files-tutorial-with-api.md)
* [Streaming live - .NET](stream-live-tutorial-with-api.md)
* [Analizzare i video - .NET](analyze-videos-tutorial-with-api.md)
* [Crittografia dinamica AES-128 - .NET](protect-with-aes128.md)
* [Crittografare in modo dinamico il contenuto con più tecnologie DRM - .NET](protect-with-drm.md) 
