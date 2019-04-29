---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765663"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Cosa accade all'app durante la distribuzione?

Tutti i metodi di distribuzione supportati ufficialmente hanno una cosa in comune: apportano modifiche ai file nella cartella `/home/site/wwwroot` dell'app. Questi sono gli stessi file che vengono eseguiti nell'ambiente di produzione. Pertanto, la distribuzione può non riuscire a causa di file bloccati o l'app nell'ambiente di produzione può avere un comportamento imprevedibile durante la distribuzione perché non tutti i file vengono aggiornati contemporaneamente. Per evitare questi problemi sono disponibili varie alternative:

- Arresta l'app o abilitare la modalità offline per l'app durante la distribuzione. Per altre informazioni, vedere [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Gestione dei file bloccati durante la distribuzione).
- Eseguire la distribuzione in uno [slot di staging](../articles/app-service/deploy-staging-slots.md) con lo [scambio automatico](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) abilitato. 
- Usare invece [Run From Package](https://github.com/Azure/app-service-announcements/issues/84) (Esegui dal pacchetto).
