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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: dccbc6e57e970ec7089f81fccb33b741b9c00e74
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376721"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Domande frequenti su Servizi multimediali di Azure v3

Questo articolo contiene le risposte alle domande frequenti su Servizi multimediali di Azure (AMS) v3.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>È possibile usare il portale di Azure per gestire le risorse della versione v3?

Per il momento no. È possibile usare uno degli SDK supportati. Vedere le esercitazioni e gli esempi in questo set di documenti.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Esiste un'API per la configurazione di unità riservate di codifica?

Il team di Servizi multimediali sta eliminando le unità riservate nella versione v3. Tuttavia le operazioni necessarie non sono state completate. Fino ad allora, i clienti dovranno usare il portale di Azure o le API di AMS v2 per impostare le unità riservate, come in [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md). 

Quando si usa **VideoAnalyzerPreset** e/o **AudioAnalyzerPreset**, impostare l'account dei Servizi multimediali di Azure su 10 unità riservate di codifica S3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Per gli asset nella versione 3 non esiste il concetto di AssetFile?

Le entità AssetFile sono state rimosse dall'API AMS per separare Servizi multimediali dalla dipendenza da Storage SDK. Le informazioni che appartengono a Storage SDK vengono ora conservate in Storage e non in Servizi multimediali. 

## <a name="where-did-client-side-storage-encryption-go"></a>Dove è finita la crittografia di archiviazione lato client?

Ora si consiglia di usare la crittografia di archiviazione lato server, che è attivata per impostazione predefinita. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Qual è il metodo di caricamento consigliato?

È consigliabile usare inserimenti HTTP(s). Per altre informazioni, vedere [Creare un input del processo da un URL HTTP(s)](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Come funziona la paginazione?

Servizi multimediali supporta $top per le risorse che supportano OData, ma il valore passato a $top deve essere minore di 1000 (ad esempio, le dimensioni di pagina per la paginazione).

Questo consente di ottenere un piccolo campione di elementi usando $top (ad esempio, i 100 elementi più recenti) o per scorrere tra tutti gli elementi usando la paginazione. 

Servizi multimediali non supporta il paging dei dati con dimensioni della pagina specificate dall'utente.

Per altre informazioni, vedere [Filtro, ordinamento, paging](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Come si recupera un'entità in Servizi multimediali v3?

La versione v3 si basa su una superficie dell'API unificata, che espone funzionalità operative e di gestione basate su **Azure Resource Manager**. In conformità con **Azure Resource Manager**, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica di Servizi multimediali v3](media-services-overview.md)
