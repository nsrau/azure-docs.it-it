---
title: Richieste di aumento della quota di vCPU per Azure Resource Manager | Microsoft Docs
description: Richieste di aumento della quota di vCPU per Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248660"
---
# <a name="quota-increase-requests"></a>Richieste di aumento della quota

Gestione risorse le quote vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali vengono applicati a due livelli per ogni sottoscrizione, in ogni area. 

Il primo livello è il limite di vCPU regionale totale (in tutte le serie di VM) e il secondo livello è il limite di vCPU per serie di VM, ad esempio la serie D vCPU. Ogni volta che viene distribuita una nuova VM, la somma dell'utilizzo di vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di VM specifica. Inoltre, il numero totale di vCPU nuovo ed esistente distribuito in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali da portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità. 

Quando viene creata una nuova sottoscrizione, la vCPU locale totale predefinita potrebbe non essere uguale alla somma delle quote predefinite di vCPU per tutte le singole serie di macchine virtuali. Ciò può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si vuole distribuire, ma non una quota sufficiente per la vCPU regionale totale per tutte le distribuzioni. In questo caso, sarà necessario inviare una richiesta per aumentare in modo esplicito il limite di vCPU regionale totale. Il limite di vCPU regionale totale non può superare la somma della quota approvata in tutte le serie di VM per l'area.

Altre informazioni sulle quote sono disponibili nella pagina delle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e nella pagina relativa ai [limiti di sottoscrizione e servizio di Azure](https://aka.ms/quotalimits) . 

