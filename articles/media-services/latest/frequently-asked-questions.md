---
title: Domande frequenti su Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756581"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Domande frequenti su Servizi multimediali di Azure v3

Questo articolo contiene le risposte alle domande frequenti su Servizi multimediali di Azure (AMS) v3.

## <a name="v3-apis"></a>API v3

### <a name="how-do-i-configure-media-reserved-units"></a>Come si esegue la configurazione di Media Reserved Units?

Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual è il metodo consigliato per l'elaborazione dei processi?

È consigliabile inviare i processi tramite un URL HTTP(s) che punta al video. Per altre informazioni, vedere [Inserimento HTTP(s)](job-input-from-http-how-to.md). Per l'elaborazione non è necessario creare un asset con il video di input.

### <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Servizi multimediali supporta $top per le risorse che supportano OData, ma il valore passato a $top deve essere minore di 1000 (ad esempio, le dimensioni di pagina per la paginazione).

Questo consente di ottenere un piccolo campione di elementi usando $top (ad esempio, i 100 elementi più recenti) o per scorrere tra tutti gli elementi usando la paginazione. 

Servizi multimediali non supporta la restituzione di pagine di dati con dimensioni di pagina specificate dall'utente.

Per altre informazioni, vedere [Filtraggio, ordinamento, restituzione di più pagine](entities-overview.md).

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Come si recupera un'entità in Servizi multimediali v3?

La versione v3 si basa su una superficie dell'API unificata, che espone funzionalità operative e di gestione basate su **Azure Resource Manager**. In conformità con **Azure Resource Manager**, i nomi delle risorse sono sempre univoci. Di conseguenza, per la risorsa è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID).

## <a name="live-streaming"></a>Streaming live 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Come è possibile inserire interruzioni/video e slate immagine durante lo streaming live?

La codifica live di Servizi multimediali v3 non supporta ancora l'inserimento di slate immagine o video durante lo streaming live. 

È possibile usare un [codificatore locale live](recommended-on-premises-live-encoders.md) per commutare il video di origine. Molte app offrono la possibilità di commutare le origini, tra cui Telestream Wirecast, Switcher Studio (in iOS), OBS Studio (app gratuita) e molte altre.

## <a name="media-services-v2-vs-v3"></a>Servizi multimediali v2 e v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Per il momento no. È possibile usare uno degli SDK supportati. Vedere le esercitazioni e gli esempi in questo set di documenti.

### <a name="is-there-an-assetfile-concept-in-v3"></a>È presente un concetto AssetFile nella versione v3?

Le entità AssetFile sono state rimosse dall'API AMS per separare Servizi multimediali dalla dipendenza da Storage SDK. Le informazioni che appartengono a Storage SDK vengono ora conservate in Storage e non in Servizi multimediali. 

Per altre informazioni, vedere [Eseguire la migrazione a Servizi multimediali v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

È ora consigliabile usare la crittografia di archiviazione lato server (che è attivata per impostazione predefinita). Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica di Servizi multimediali v3](media-services-overview.md)
