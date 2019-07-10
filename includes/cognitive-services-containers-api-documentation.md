---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704244"
---
## <a name="validate-that-a-container-is-running"></a>Verificare che il contenitore sia in esecuzione 

Per verificare se il contenitore è in esecuzione, sono disponibili diverse opzioni. 

|Richiesta|Scopo|
|--|--|
|`http://localhost:5000/`|Il contenitore fornisce una home page.|
|`http://localhost:5000/status`|Viene effettuata una richiesta con GET, per verificare che il contenitore sia in esecuzione senza generare una query dell'endpoint. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes.|
|`http://localhost:5000/swagger`|Il contenitore fornisce una serie completa di documentazione per gli endpoint, oltre a una funzionalità `Try it now`. Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](./media/cognitive-services-containers-api-documentation/container-webpage.png)
