---
title: Richieste di aumento della quota di vCPU per Azure Resource Manager
description: Richieste di aumento della quota di vCPU per Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7fd04428edc28315e714b9e81af0bea0e9344e52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763806"
---
# <a name="quota-increase-requests"></a>Richieste di aumento della quota

Gestione risorse le quote vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali vengono applicati a due livelli per ogni sottoscrizione, in ogni area.

Il primo livello è il limite di vCPU regionale totale per tutte le serie di macchine virtuali. Il secondo livello è il limite di vCPU per serie di macchine virtuali, ad esempio la serie D vCPU. Ogni volta che viene distribuita una nuova macchina virtuale, la somma dell'utilizzo di vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota vCPU approvata per la serie di VM specifica. Inoltre, il numero totale di vCPU nuovo ed esistente distribuito in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà consentita.
È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali da portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità.

Quando viene creata una nuova sottoscrizione, la vCPU locale totale predefinita potrebbe non essere uguale alla somma delle quote predefinite di vCPU per tutte le singole serie di macchine virtuali. Questo fatto può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si vuole distribuire. Potrebbe non essere disponibile una quota sufficiente per la vCPU regionale totale per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare in modo esplicito il limite di vCPU regionale totale. Il limite di vCPU regionale totale non può superare la somma della quota approvata in tutte le serie di VM per l'area.

Per altre informazioni sulle quote, vedere la pagina relativa alle [quote vCPU della macchina virtuale](../../virtual-machines/windows/quotas.md) e alla [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).

