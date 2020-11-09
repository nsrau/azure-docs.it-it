---
title: Informazioni sull'API Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: L'API Bing Local Business Search è un servizio RESTful che consente all'applicazione di trovare informazioni su luoghi e aziende locali in base alle query di ricerca.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 6b715d4b801159b451957c30ee9ef7b406f3a2f4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095374"
---
# <a name="what-is-bing-local-business-search"></a>Informazioni sull'API di ricerca di attività locali Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).
L'API di ricerca di attività locali Bing è un servizio RESTful che consente all'applicazione di trovare informazioni su aziende locali in base alle query di ricerca. Ad esempio, `q=<business-name> in Redmond, Washington` o `q=Italian restaurants near me`. 

## <a name="features"></a>Funzionalità
| Funzionalità | Descrizione |  
| -- | -- | 
| [Trovare località e aziende locali](quickstarts/local-quickstart.md) | L'API di ricerca di attività locali Bing restituisce risultati localizzati da una query. I risultati includono un URL per il sito Web dell'azienda e il testo visualizzato, il numero di telefono e la posizione geografica, tra cui le coordinate GPS, la città, la via e il numero civico. |  
| [Filtrare i risultati locali in base a limiti geografici](specify-geographic-search.md) | Aggiungere coordinate come parametri di ricerca per limitare i risultati solo a un'area specifica, definita mediante un'area circolare o un rettangolo delimitatore. | 
| [Filtrare i risultati relativi alle aziende locali per categoria](local-categories.md) | Cercare i risultati relativi alle aziende locali per categoria. Questa opzione usa la posizione dell'indirizzo IP inverso o le coordinate GPS del chiamante per restituire risultati localizzati in varie categorie di business.|

## <a name="workflow"></a>Flusso di lavoro
Chiamare l'API di ricerca di attività locali Bing da qualsiasi linguaggio di programmazione in grado di eseguire richieste HTTP e analizzare risposte JSON. Questo servizio è accessibile tramite l'API REST.
 
1. Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/).   
2. Nell'URL vengono codificati i termini di ricerca per il parametro di query `q=""`. Ad esempio, `q=nearby+restaurant` o `q=nearby%20restaurant`. Se necessario, impostare anche la paginazione. 
3. Inviare una [richiesta all'API di ricerca di attività locali Bing](quickstarts/local-quickstart.md). 
4. Analizzare la risposta JSON. 

> [!NOTE]
> Attualmente, Ricerca attività commerciali locali: 
> * Supporta solo il mercato `en-US`. 
> * Non supporta Suggerimenti automatici Bing. 

## <a name="next-steps"></a>Passaggi successivi
- [Query and response](local-search-query-response.md) (Query e risposta)
- [Local Business Search quickstart](quickstarts/local-quickstart.md) (Guida introduttiva alla ricerca di attività locali)
- [Informazioni di riferimento per l'API Local Business Search](local-search-reference.md)
- [Requisiti per l'uso e la visualizzazione](use-display-requirements.md)
