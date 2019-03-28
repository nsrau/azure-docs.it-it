---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522518"
---
## <a name="validate-container-is-running"></a>Convalidare contenitore è in esecuzione 

Esistono diversi modi per convalidare il contenitore è in esecuzione: 

|Richiesta|Scopo|
|--|--|
|`http://localhost:5000/`|Il contenitore fornisce una home page.|
|`http://localhost:5000/status`|Richiesta con GET, per convalidare il contenitore è in esecuzione senza causare una query di endpoint. Può essere utilizzato per Kubernetes [probe di attività e verificare la preparazione](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Il contenitore fornisce documentazione completa per gli endpoint, oltre a una funzionalità `Try it now`. Questa funzionalità consente di immettere le impostazioni personalizzate in un modulo HTML basato sul Web ed eseguire la query senza dover scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](./media/cognitive-services-containers-api-documentation/container-webpage.png)
