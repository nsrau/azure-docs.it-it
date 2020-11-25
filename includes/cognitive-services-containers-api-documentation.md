---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996439"
---
## <a name="validate-that-a-container-is-running"></a>Verificare che il contenitore sia in esecuzione 

Per verificare se il contenitore è in esecuzione, sono disponibili diverse opzioni. Individuare l'indirizzo *IP esterno* e la porta esposta del contenitore in questione e aprire il Web browser preferito. Usare i vari URL di richiesta indicati di seguito per verificare che il contenitore sia in esecuzione. Gli URL di richiesta di esempio elencati `http://localhost:5000` di seguito sono, ma il contenitore specifico può variare. Tenere presente che si sta per fare affidamento sull'indirizzo *IP esterno* del contenitore e sulla porta esposta.

| URL della richiesta | Scopo |
|--|--|
| `http://localhost:5000/` | Il contenitore fornisce un home page. |
| `http://localhost:5000/ready` | Richiesto con GET, in questo modo viene verificata la disponibilità del contenitore per l'accettazione di una query sul modello.  Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes. |
| `http://localhost:5000/status` | Richiesto anche con GET, verifica se la chiave API usata per avviare il contenitore è valida senza causare una query dell'endpoint. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes. |
| `http://localhost:5000/swagger` | Il contenitore fornisce un set completo di documentazione per gli endpoint e una funzionalità **Prova**. Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](./media/cognitive-services-containers-api-documentation/container-webpage.png)
