---
title: Terminologia e concetti di servizi multimediali
titleSuffix: Azure Media Services
description: Informazioni sulla terminologia e i concetti per servizi multimediali di Azure.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500099"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologia e concetti di servizi multimediali

Questo argomento fornisce una breve panoramica dei concetti e della terminologia di servizi multimediali di Azure. Questo articolo fornisce inoltre collegamenti ad articoli con una spiegazione approfondita dei concetti e delle funzionalità di servizi multimediali V3.

I concetti fondamentali descritti in questi argomenti devono essere esaminati prima di avviare lo sviluppo.

> [!NOTE]
> Attualmente, è possibile usare il [portale di Azure](https://portal.azure.com/) per: gestire [gli eventi live](live-events-outputs-concept.md)di servizi multimediali V3, visualizzare (non gestire [) v3](assets-concept.md)e [ottenere informazioni sull'accesso alle API](access-api-portal.md).
> Per tutte le altre attività di gestione (ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md)), usare l' [API REST](https://aka.ms/ams-v3-rest-ref), l' [interfaccia](https://aka.ms/ams-v3-cli-ref)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia di servizi multimediali V3

|Termine|Descrizione|
|---|---|
|Evento Live|Un **evento Live** rappresenta una pipeline per l'inserimento, la transcodifica (facoltativamente) e la creazione di pacchetti di flussi live di video, audio e metadati in tempo reale.<br/><br/>Per i clienti che eseguono la migrazione dalle API di servizi multimediali V2, l' **evento Live** sostituisce l'entità del **canale** nella versione V2. Per ulteriori informazioni, vedere [migrazione da V2 a V3](migrate-from-v2-to-v3.md).|
|Endpoint di streaming/creazione di pacchetti/origine|Un **endpoint di streaming** rappresenta un servizio di creazione e creazione di pacchetti dinamici (just-in-Time) in grado di fornire contenuti live e on demand direttamente a un'applicazione lettore client. Usa uno dei protocolli di streaming media comuni (HLS o DASH). Inoltre, l' **endpoint di streaming** fornisce la crittografia dinamica (just-in-Time) ai sistemi di Digital Rights Management leader del settore (DRM).<br/><br/>Nel settore dei flussi multimediali, questo servizio viene comunemente definito come un **pacchetto** o un' **origine**.  Altri termini comuni nel settore per questa funzionalità includono JITP (just-in-Time-Packager) o JITE (just-in-time-Encryption).

## <a name="media-services-v3-concepts"></a>Concetti su servizi multimediali V3

|Concetti|Descrizione|Collegamenti|
|---|---|---|
|Asset e caricamento di contenuti|Per iniziare a gestire, crittografare, codificare, analizzare e trasmettere contenuti multimediali in Azure, è necessario creare un account di servizi multimediali e caricare i file digitali in **Asset**.|[Caricamento e archiviazione nel cloud](storage-account-concept.md)<br/><br/>[Concetto di asset](assets-concept.md)|
|Codifica del contenuto|Dopo aver caricato i file multimediali digitali di alta qualità in asset, è possibile codificarli in formati che possono essere riprodotti in un'ampia gamma di browser e dispositivi. <br/><br/>Per eseguire la codifica con servizi multimediali V3, è necessario creare **trasformazioni** e **processi**.|[Trasformazioni e processi](transforms-jobs-concept.md)<br/><br/>[Codifica con Servizi multimediali](encoding-concept.md)|
|Analisi del contenuto (Video Indexer)|Media Services v3 consente di estrarre informazioni dettagliate dai file video e audio usando i set di impostazioni di Media Services V3. Per analizzare i contenuti usando i set di impostazioni di Media Services V3, è necessario creare **trasformazioni** e **processi**.<br/><br/>Se si desiderano informazioni dettagliate più dettagliate, utilizzare [video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) direttamente.|[Analisi di file video e audio](analyzing-video-audio-files-concept.md)|
|Creazione di pacchetti e recapito|Una volta codificato il contenuto, è possibile sfruttare i vantaggi della creazione **dinamica dei pacchetti**. In servizi multimediali, un **endpoint di streaming** è il servizio di creazione dinamica dei pacchetti usato per distribuire contenuti multimediali ai lettori client. Per rendere disponibili i video nell'asset di output ai client per la riproduzione, è necessario creare un **localizzatore di streaming** e quindi creare gli URL di streaming. <br/><br/>Quando si crea il **localizzatore di streaming**, oltre al nome dell'asset, è necessario specificare i **criteri di streaming**. I **criteri di streaming** consentono di definire i protocolli di streaming e le opzioni di crittografia (se presenti) per i **localizzatori di streaming**. La creazione dinamica dei pacchetti viene usata se si esegue lo streaming dei contenuti live o on demand. <br/><br/>È possibile usare **manifesti dinamici** di servizi multimediali per trasmettere solo una copia di rendering o clip secondari specifici del video.|[creazione dinamica dei pacchetti](dynamic-packaging-overview.md)<br/><br/>[Endpoint di streaming](streaming-endpoint-concept.md)<br/><br/>[Localizzatori di streaming](streaming-locators-concept.md)<br/><br/>[Criteri di streaming](streaming-policy-concept.md)<br/><br/>[Manifesti dinamici](filters-dynamic-manifest-overview.md)<br/><br/>[Filtri](filters-concept.md)|
|Protezione del contenuto|Con servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con Advanced Encryption Standard (AES-128) o/e uno dei tre principali sistemi DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. <br/><br/>Se si specificano le opzioni di crittografia nel flusso, creare i **criteri della chiave** simmetrica e associarli al **localizzatore di streaming**. I **criteri della chiave** simmetrica consentono di configurare il modo in cui la chiave simmetrica viene recapitata ai client finali.<br/><br/> Provare a riutilizzare i criteri ogni volta che sono necessarie le stesse opzioni.| [Criteri di chiave simmetrica](content-key-policy-concept.md)<br/><br/>[Protezione del contenuto](content-protection-overview.md)|
|Streaming live|Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Gli **eventi live** sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un **evento Live**, viene creato un endpoint di input che può essere usato per inviare un segnale Live da un codificatore remoto. Una volta che il flusso è stato trasmesso nell' **evento Live**, è possibile iniziare l'evento di streaming creando un **Asset**, un **output Live**e un **localizzatore di streaming**. L' **output in tempo reale** archivia il flusso nell' **Asset** e lo rende disponibile ai visualizzatori tramite l' **endpoint di streaming**. Un evento live può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o sulla *codifica live* (un codificatore live locale invia un flusso a bitrate singolo). |[Panoramica dello streaming live](live-streaming-overview.md)<br/><br/>[Eventi live e output live](live-events-outputs-concept.md)|
|Monitoraggio con griglia di eventi|Per visualizzare lo stato di avanzamento del processo, usare **griglia di eventi**. Servizi multimediali genera anche i tipi di evento Live. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. |[Gestione degli eventi di Griglia di eventi](reacting-to-media-services-events.md)<br/><br/>[Schemi](media-services-event-schemas.md)|
|Monitoraggio con monitoraggio di Azure|Monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle app con monitoraggio di Azure.|[Metriche e log di diagnostica](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schemi dei log di diagnostica](media-services-diagnostic-logs-schema.md)|
|Clienti più importanti|È possibile usare Azure Media Player per riprodurre contenuto multimediale trasmesso da servizi multimediali in un'ampia gamma di browser e dispositivi. Azure Media Player usa gli standard del settore, ad esempio HTML5, media source Extensions (MSE) e Encrypted Media Extensions (EME), per offrire un'esperienza di streaming adattivo arricchita. |[Panoramica di Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Codificare il file remoto e trasmettere il video in streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificare il file caricato e trasmettere il video in streaming - .NET](stream-files-tutorial-with-api.md)
* [Streaming live - .NET](stream-live-tutorial-with-api.md)
* [Analizzare i video - .NET](analyze-videos-tutorial-with-api.md)
* [Crittografia dinamica AES-128 - .NET](protect-with-aes128.md)
* [Crittografare in modo dinamico il contenuto con più tecnologie DRM - .NET](protect-with-drm.md)
