---
title: Terminologia e concetti di Servizi multimediali
titleSuffix: Azure Media Services
description: Informazioni sulla terminologia e sui concetti per Servizi multimediali di Azure.Learn about terminology and concepts for Azure Media Services.
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
# <a name="media-services-terminology-and-concepts"></a>Terminologia e concetti di Servizi multimediali

In questo argomento viene fornita una breve panoramica della terminologia e dei concetti di Servizi multimediali di Azure.This topic gives a brief overview of Azure Media Services terminology and concepts. L'articolo fornisce inoltre collegamenti ad articoli con una spiegazione approfondita dei concetti e delle funzionalità di Servizi multimediali v3.

I concetti fondamentali descritti in questi argomenti devono essere rivisti prima di iniziare lo sviluppo.

> [!NOTE]
> Attualmente, è possibile utilizzare il portale di [Azure](https://portal.azure.com/) per gestire gli eventi [live](live-events-outputs-concept.md)di Servizi multimediali v3 , visualizzare (non gestire) [le risorse](assets-concept.md)v3 e ottenere informazioni [sull'accesso](access-api-portal.md)alle API.
> Per tutte le altre attività di gestione, ad esempio [Trasformazioni e processi](transforms-jobs-concept.md) e [Protezione contenuto,](content-protection-overview.md)utilizzare l'API [REST](https://aka.ms/ams-v3-rest-ref), l'interfaccia della riga [di comando](https://aka.ms/ams-v3-cli-ref)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia di Media Services v3

|Termine|Descrizione|
|---|---|
|Evento dal vivo|Un **evento live** rappresenta una pipeline per l'inserimento, la transcodifica (facoltativa) e il pacchetto di flussi live di metadati video, audio e in tempo reale.<br/><br/>Per i clienti che eseguono la migrazione dalle API di Servizi multimediali v2, **l'evento live** sostituisce l'entità **Canale** nella versione 2. Per ulteriori informazioni, vedere [Migrazione dalla v2 alla v3.](migrate-from-v2-to-v3.md)|
|Endpoint di streaming/Packaging/Origin|Un endpoint di **streaming** rappresenta un servizio dinamico (just-in-time) di creazione di pacchetti e origini in grado di distribuire i contenuti live e on-demand direttamente a un'applicazione lettore client. Utilizza uno dei protocolli multimediali di streaming comuni (HLS o DASH). Inoltre, **l'endpoint** di streaming fornisce la crittografia dinamica (just-in-time) ai sistemi d'amministrazione che leader del settore per la gestione dei diritti digitali (DMM).<br/><br/>Nel settore dello streaming multimediale, questo servizio è comunemente indicato come **Packager** o **Origin**.  Altri termini comuni nel settore per questa funzionalità includono JITP (just-in-time-packager) o JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>Concetti di Servizi multimediali v3

|Concetti|Descrizione|Collegamenti|
|---|---|---|
|Risorse e caricamento di contenuti|Per iniziare a gestire, crittografare, codificare, analizzare e trasmettere contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali e caricare i file digitali in **Assets**.|[Caricamento e archiviazione nel cloud](storage-account-concept.md)<br/><br/>[Concetto di asset](assets-concept.md)|
|Codifica del contenuto|Una volta caricati i file multimediali digitali di alta qualità in Risorse, è possibile codificarli in formati che possono essere riprodotti su una vasta gamma di browser e dispositivi. <br/><br/>Per codificare con Servizi multimediali v3, è necessario creare **trasformazioni** e **processi**.|[Trasformazioni e processi](transforms-jobs-concept.md)<br/><br/>[Codifica con Servizi multimediali](encoding-concept.md)|
|Analisi del contenuto (Video Indexer)|Servizi multimediali v3 consente di estrarre informazioni dettagliate dai file video e audio utilizzando i predefiniti di Servizi multimediali v3. Per analizzare il contenuto utilizzando i predefiniti di Servizi multimediali v3, è necessario creare **trasformazioni** e **processi**.<br/><br/>Se desideri ottenere informazioni più dettagliate, usa direttamente [L'indicizzatore video.](https://docs.microsoft.com/azure/media-services/video-indexer/)|[Analisi di file video e audio](analyzing-video-audio-files-concept.md)|
|Creazione di pacchetti e recapito|Una volta codificati i contenuti, puoi sfruttare **Dynamic Packaging.** In Servizi multimediali, un endpoint di **streaming** è il servizio di creazione dinamica di pacchetti utilizzato per distribuire contenuti multimediali ai lettori client. Per rendere disponibili i video nell'asset di output ai client per la riproduzione, devi creare un **localizzatore** di streaming e quindi creare URL di streaming. <br/><br/>Quando si crea **Streaming Locator**, oltre al nome dell'asset, è necessario specificare Criteri di **streaming**. **I criteri** di streaming consentono di definire i protocolli di streaming e le opzioni di crittografia (se presenti) per **i localizzatori**di streaming. Il packaging dinamico viene usato sia che si traslocano in streaming i contenuti in diretta o su richiesta. <br/><br/>È possibile utilizzare **i manifesti dinamici** di Servizi multimediali per trasmettere solo una copia trasformata o clip secondarie specifiche del video.|[creazione dinamica dei pacchetti](dynamic-packaging-overview.md)<br/><br/>[Endpoint di streaming](streaming-endpoint-concept.md)<br/><br/>[Localizzatori di streaming](streaming-locators-concept.md)<br/><br/>[Criteri di streaming](streaming-policy-concept.md)<br/><br/>[Manifesti dinamici](filters-dynamic-manifest-overview.md)<br/><br/>[Filtri](filters-concept.md)|
|Protezione del contenuto|Con Servizi multimediali, puoi distribuire i tuoi contenuti live e on-demand crittografati dinamicamente con Advanced Encryption Standard (AES-128) o/e uno dei tre principali sistemi DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. <br/><br/>Se si specificano opzioni di crittografia nel flusso, creare i **criteri chiave simmetrica** e associarli al **localizzatore**di flusso . I **criteri chiave simmetrica** consentono di configurare la modalità di recapito della chiave simmetrica ai client finali.<br/><br/> Provare a riutilizzare i criteri ogni volta che sono necessarie le stesse opzioni.| [Criteri di chiave simmetrica](content-key-policy-concept.md)<br/><br/>[Protezione del contenuto](content-protection-overview.md)|
|Streaming live|Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Gli **eventi live** sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento **live**, viene creato un endpoint di input che è possibile utilizzare per inviare un segnale live da un codificatore remoto. Una volta che hai il flusso **nell'evento Live,** puoi iniziare l'evento di streaming creando **un Asset,** **un Live Output**e un **Streaming Locator**. **Live Output** archivierà lo streaming nell'asset e lo renderà disponibile ai visualizzatori tramite l'endpoint di **streaming.** **Asset** Un evento live può essere impostato su un *pass-through* (un codificatore live locale invia un flusso a bitrate multiplo) o una *codifica live* (un codificatore live locale invia un singolo flusso a bitrate). |[Panoramica dello streaming live](live-streaming-overview.md)<br/><br/>[Eventi live e output live](live-events-outputs-concept.md)|
|Monitoraggio con griglia di eventiMonitoring with Event Grid|Per visualizzare lo stato del processo, utilizzare **Griglia di eventi**. Servizi multimediali genera anche i tipi di eventi live. Con Griglia di eventi, le app possono rimanere in ascolto e reagire agli eventi praticamente da tutti i servizi di Azure, oltre che da origini personalizzate. |[Gestione degli eventi di Griglia di eventi](reacting-to-media-services-events.md)<br/><br/>[Schemi](media-services-event-schemas.md)|
|Monitoraggio con Monitoraggio di AzureMonitoring with Azure Monitor|Monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle app con Monitoraggio di Azure.Monitor metrics and diagnostic logs that help you understand how your apps are performing with Azure Monitor.|[Metriche e log di diagnostica](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schemi dei log di diagnostica](media-services-diagnostic-logs-schema.md)|
|Clienti più importanti|È possibile usare Azure Media Player per riprodurre il contenuto multimediale trasmesso da Servizi multimediali in un'ampia gamma di browser e dispositivi. Azure Media Player usa gli standard del settore, ad esempio HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per offrire un'esperienza di streaming adattivo avanzata. |[Panoramica di Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Codificare il file remoto e trasmettere il video in streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificare il file caricato e trasmettere il video in streaming - .NET](stream-files-tutorial-with-api.md)
* [Streaming live - .NET](stream-live-tutorial-with-api.md)
* [Analizzare i video - .NET](analyze-videos-tutorial-with-api.md)
* [Crittografia dinamica AES-128 - .NET](protect-with-aes128.md)
* [Crittografare in modo dinamico il contenuto con più tecnologie DRM - .NET](protect-with-drm.md)
