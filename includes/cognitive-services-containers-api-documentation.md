---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034436"
---
## <a name="validate-that-a-container-is-running"></a>Verificare che il contenitore sia in esecuzione 

Per verificare se il contenitore è in esecuzione, sono disponibili diverse opzioni. Individuare l'indirizzo *IP esterno* e la porta esposta del contenitore in questione e aprire il browser Web preferito. Utilizzare i vari URL di richiesta riportati di seguito per verificare che il contenitore sia in esecuzione. Gli URL di richiesta di `http://localhost:5000`esempio elencati di seguito sono , ma il contenitore specifico può variare. Tenere presente che si deve fare affidamento sull'indirizzo *IP esterno* del contenitore e sulla porta esposta.

| URL richiesta | Scopo |
|--|--|
| `http://localhost:5000/` | Il contenitore fornisce una home page. |
| `http://localhost:5000/status` | Richiesto con un HTTP GET, per convalidare che il contenitore sia in esecuzione senza causare una query dell'endpoint. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes. |
| `http://localhost:5000/swagger` | Il contenitore fornisce un set completo di documentazione per gli endpoint e una funzionalità **Prova.** Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](./media/cognitive-services-containers-api-documentation/container-webpage.png)
