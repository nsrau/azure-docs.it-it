---
title: Informazioni sull'API Bing Local Business Search | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: L'API Bing Local Business Search è un servizio RESTful che consente all'applicazione di trovare informazioni su luoghi e aziende locali in base alle query di ricerca.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fb3b375a66ac9b05ad26287b7c39ce8e748ad699
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780321"
---
# <a name="what-is-bing-local-business-search"></a>Informazioni sull'API di ricerca di attività locali Bing
L'API di ricerca di attività locali Bing è un servizio RESTful che consente all'applicazione di trovare informazioni su aziende locali in base alle query di ricerca. Ad esempio, `q=<business-name> in Redmond, Washington` o `q=Italian restaurants near me`. 

## <a name="features"></a>Funzionalità
| Funzionalità | DESCRIZIONE |  
| -- | -- | 
| [Trovare località e aziende locali](quickstarts/local-quickstart.md) | L'API di ricerca di attività locali Bing restituisce risultati localizzati da una query. I risultati includono un URL per il sito Web dell'azienda e il testo visualizzato, il numero di telefono e la posizione geografica, tra cui le coordinate GPS, la città, la via e il numero civico. |  
| [Filtrare i risultati locali in base a limiti geografici](specify-geographic-search.md) | Aggiungere coordinate come parametri di ricerca per limitare i risultati solo a un'area specifica, definita mediante un'area circolare o un rettangolo delimitatore. | 
| [Filtrare i risultati relativi alle aziende locali per categoria](local-categories.md) | Cercare i risultati relativi alle aziende locali per categoria. Questa opzione usa la posizione dell'indirizzo IP inverso o le coordinate GPS del chiamante per restituire risultati localizzati in varie categorie di business.|

## <a name="workflow"></a>Flusso di lavoro
Chiamare l'API di ricerca di attività locali Bing da qualsiasi linguaggio di programmazione in grado di eseguire richieste HTTP e analizzare risposte JSON. Questo servizio è accessibile tramite l'API REST.
 
1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Nell'URL vengono codificati i termini di ricerca per il parametro di query `q=""`. Ad esempio, `q=nearby+restaurant` o `q=nearby%20restaurant`. Se necessario, impostare anche la paginazione. 
3. Inviare una [richiesta all'API di ricerca di attività locali Bing](quickstarts/local-quickstart.md). 
4. Analizzare la risposta JSON. 

> [!NOTE]
> Attualmente l'API di ricerca di attività locali Bing è supportata solo per il mercato `en-US`. 
> [!NOTE]
> Attualmente l'API di ricerca di attività locali Bing non supporta i suggerimenti automatici. 

## <a name="next-steps"></a>Passaggi successivi
- [Query and response](local-search-query-response.md) (Query e risposta)
- [Local Business Search quickstart](quickstarts/local-quickstart.md) (Guida introduttiva alla ricerca di attività locali)
- [Informazioni di riferimento per l'API Local Business Search](local-search-reference.md)
- [Requisiti per l'uso e la visualizzazione](use-display-requirements.md)
