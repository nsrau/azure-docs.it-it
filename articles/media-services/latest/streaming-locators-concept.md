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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120344"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, il **localizzatore di streaming** è valido immediatamente dopo l'esecuzione delle chiamate API e rimane tale finché non viene eliminato, a meno che non si configurino le ore di inizio e fine facoltative. 

Quando si crea una **localizzatore di Streaming**, è necessario specificare un' **Asset** nome e un **Streaming criteri** nome. Per altre informazioni, vedere i seguenti argomenti:

* [Asset](assets-concept.md)
* [Criteri di streaming](streaming-policy-concept.md)
* [Criteri di chiave simmetrica](content-key-policy-concept.md)

> [!IMPORTANT]
> * Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limitazioni](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Associare i filtri con i localizzatori di Streaming

Visualizzare [filtri: associare con i localizzatori di Streaming](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtro, ordine, le entità del localizzatore di Streaming di pagina

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video con .NET](stream-files-tutorial-with-api.md)
