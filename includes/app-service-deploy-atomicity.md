---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 91a4a9ae1d3d84f1396adad07d1cda73ee3747c9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312465"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Cosa accade all'app durante la distribuzione?

Tutti i metodi di distribuzione supportati ufficialmente hanno una cosa in comune: apportano modifiche ai file nella cartella `/site/home/wwwroot` dell'app. Questi sono gli stessi file che vengono eseguiti nell'ambiente di produzione. Pertanto, la distribuzione può non riuscire a causa di file bloccati o l'app nell'ambiente di produzione può avere un comportamento imprevedibile durante la distribuzione perché non tutti i file vengono aggiornati contemporaneamente. Per evitare questi problemi sono disponibili varie alternative:

- Arresta l'app o abilitare la modalità offline per l'app durante la distribuzione. Per altre informazioni, vedere [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Gestione dei file bloccati durante la distribuzione).
- Eseguire la distribuzione in uno [slot di staging](../articles/app-service/web-sites-staged-publishing.md) con lo [scambio automatico](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) abilitato. 
- Usare invece [Run From Package](https://github.com/Azure/app-service-announcements/issues/84) (Esegui dal pacchetto).
