---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836785"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Cosa accade all'app durante la distribuzione?

Tutti i metodi di distribuzione supportate ufficialmente apportano modifiche ai file nei `/home/site/wwwroot` cartella dell'app. Questi file sono gli stessi che vengono eseguiti nell'ambiente di produzione. Pertanto, la distribuzione può non riuscire a causa di file bloccati. L'app nell'ambiente di produzione potrebbe anche si comportano in modo imprevedibile durante la distribuzione, poiché non tutti i file aggiornati nello stesso momento. Per evitare questi problemi sono disponibili varie alternative:

- Arresta l'app o abilitare la modalità offline per l'app durante la distribuzione. Per altre informazioni, vedere [gestire i file bloccati durante la distribuzione](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Eseguire la distribuzione in uno [slot di staging](../articles/app-service/deploy-staging-slots.md) con lo [scambio automatico](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) abilitato. 
- Uso [eseguiti dal pacchetto](https://github.com/Azure/app-service-announcements/issues/84) anziché la distribuzione continua.
