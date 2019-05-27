---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124345"
---
## <a name="validate-that-a-container-is-running"></a>Verificare che il contenitore sia in esecuzione 

Per verificare se il contenitore è in esecuzione, sono disponibili diverse opzioni. 

|Richiesta|Scopo|
|--|--|
|`http://localhost:5000/`|Il contenitore fornisce una home page.|
|`http://localhost:5000/status`|Viene effettuata una richiesta con GET, per verificare che il contenitore sia in esecuzione senza generare una query dell'endpoint. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes.|
|`http://localhost:5000/swagger`|Il contenitore fornisce una serie completa di documentazione per gli endpoint, oltre a una funzionalità `Try it now`. Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](./media/cognitive-services-containers-api-documentation/container-webpage.png)
