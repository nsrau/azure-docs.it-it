---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752160"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Cosa accade all'app durante la distribuzione?

Tutti i metodi di distribuzione ufficialmente supportati consentono di apportare modifiche ai file nella cartella `/home/site/wwwroot` dell'app. Questi file vengono usati per eseguire l'app. Pertanto, la distribuzione può avere esito negativo a causa di file bloccati. L'app può anche comportarsi in modo imprevedibile durante la distribuzione, perché non tutti i file vengono aggiornati contemporaneamente. Questa operazione non è auspicabile per un'app rivolte ai clienti. Per evitare questi problemi sono disponibili varie alternative:

- Arresta l'app o abilitare la modalità offline per l'app durante la distribuzione. Per altre informazioni, vedere [gestire i file bloccati durante la distribuzione](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Eseguire la distribuzione in uno [slot di staging](../articles/app-service/deploy-staging-slots.md) con lo [scambio automatico](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) abilitato. 
- Utilizzare [Esegui da pacchetto](https://github.com/Azure/app-service-announcements/issues/84) anziché distribuzione continua.
