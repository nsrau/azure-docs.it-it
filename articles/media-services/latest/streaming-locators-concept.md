---
title: Localizzatori di streaming in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra le caratteristiche dei localizzatori di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743481"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea un **localizzatore di streaming**, è necessario specificare il nome dell'[asset](https://docs.microsoft.com/rest/api/media/assets) e il nome dei [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies). È possibile usare i criteri di streaming predefiniti oppure creare criteri personalizzati. I criteri predefiniti attualmente disponibili sono: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'. Se si usano criteri di streaming personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che è necessario usare le stesse opzioni e gli stessi protocolli. 

Se si vogliono specificare opzioni di crittografia nel flusso, creare i [criteri di chiave simmetrica](https://docs.microsoft.com/rest/api/media/contentkeypolicies) che configurano le modalità di recapito della chiave simmetrica ai client finali usando il componente di distribuzione delle chiavi di Servizi multimediali. Associare il localizzatore di streaming ai **criteri di chiave simmetrica** e a tale chiave. È possibile consentire a Servizi multimediali di generare automaticamente la chiave. L'esempio .NET seguente illustra come configurare la crittografia AES con una restrizione relativa ai token in Servizi multimediali v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). I **criteri di chiave simmetrica** possono essere aggiornati. Si potrebbe decidere di aggiornare i criteri se è necessario eseguire una rotazione delle chiavi. Per l'aggiornamento e la selezione dei criteri aggiornati le cache di distribuzione delle chiavi possono impiegare al massimo 15 minuti. È consigliabile non creare nuovi criteri di chiave simmetrica per ogni localizzatore di streaming. Provare invece a riusare i criteri esistenti ogni volta che sono necessarie le stesse opzioni.

> [!IMPORTANT]
> * Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che si devono usare le stesse opzioni. 

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, restituzione di più pagine

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video con .NET](stream-files-tutorial-with-api.md)
* [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
