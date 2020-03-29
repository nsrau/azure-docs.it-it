---
title: Richieste di aumento della quota di vCPU per Azure Resource Manager
description: Richieste di aumento della quota di vCPU per Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843668"
---
# <a name="quota-increase-requests"></a>Richieste di aumento della quota

Le quote vCPU di Resource Manager per le macchine virtuali e i set di scalabilità delle macchine virtuali vengono applicate a due livelli per ogni sottoscrizione, in ogni area.

Il primo livello è il limite totale delle vCPU regionali in tutte le serie di macchine virtuali. Il secondo livello è il limite di vCPU per serie VM, ad esempio le vCPU della serie D. Ogni volta che deve essere distribuita una nuova macchina virtuale, la somma dell'utilizzo di vCPU nuove ed esistenti per tale serie di macchine virtuali non deve superare la quota vCPU approvata per quella particolare serie di macchine virtuali. Inoltre, il numero totale di vCPU nuove ed esistenti distribuito in tutte le serie di macchine virtuali non deve superare la quota totale di vCPU regionali approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà consentita.
È possibile richiedere un aumento del limite di quota vCPUUs per la serie di macchine virtuali dal portale di Azure.You can request a increase of the vCPUs quota limit for the VM series from Azure portal. Un aumento della quota della serie VM aumenta automaticamente il limite totale delle vCPU regionali dello stesso importo.

Quando viene creata una nuova sottoscrizione, le vCPU regionali totali predefinite potrebbero non essere uguali alla somma delle quote vCPU predefinite per tutte le singole serie di macchine virtuali. Questo fatto può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di macchine virtuali che si vuole distribuire. Potrebbe mancare una quota sufficiente per le vCPU regionali totali per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare in modo esplicito il limite totale delle vCPU regionali. Il limite totale delle vCPU regionali non può superare la somma della quota approvata in tutte le serie di macchine virtuali per l'area.

Per altre informazioni sulle quote, vedere Quote vCPU della macchina virtuale e Limiti, quote e vincoli della sottoscrizione e del servizio di Azure.For more information about quotas, see [Virtual machine vCPU quotas](../../virtual-machines/windows/quotas.md) and [Azure subscription and service limits, quotas, and constraints](../../azure-resource-manager/management/azure-subscription-service-limits.md).

