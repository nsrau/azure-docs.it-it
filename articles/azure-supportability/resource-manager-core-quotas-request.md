---
title: Richieste di aumento della quota di vCPU per Azure Resource Manager | Microsoft Docs
description: Richieste di aumento della quota di vCPU per Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076817"
---
# <a name="quota-increase-requests"></a>Richieste di aumento della quota

Le quote di vCPU per Resource Manager per macchine virtuali e set di scalabilità di macchine virtuali sono imposte a due livelli per ogni sottoscrizione, in ogni area. 

Al primo livello è il limite di Vcpu regionale totale (in tutte le serie di VM) e il secondo livello è il limite di Vcpu serie di VM (ad esempio i Vcpu serie D). Qualsiasi momento che una nuova macchina virtuale deve essere distribuita, la somma dell'utilizzo di CPU virtuali nuove ed esistenti per tale serie di macchine Virtuali non deve superare la quota di vCPU approvata per quel particolare serie di macchine Virtuali. Inoltre, il conteggio totale vCPU nuovi ed esistenti distribuito in tutte le macchine virtuali della serie non deve superare la quota Vcpu regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite della quota di Vcpu per la serie di macchine Virtuali dal portale di Azure. Un aumento della quota di macchine virtuali della serie di aumenta automaticamente il limite di Vcpu regionale totale dello stesso importo. 

Quando viene creata una nuova sottoscrizione, i Vcpu regionale totale predefinito potrebbero non essere uguale alla somma delle quote di vCPU predefinito per tutte le serie di macchine Virtuali singole. Ciò può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si desidera distribuire, ma non sono sufficienti quote di Vcpu regionali totali per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare il limite di Vcpu regionali totali in modo esplicito. Limite di Vcpu regionale totale non può superare la somma della quota approvato in tutte le serie VM per l'area.

Altre informazioni sulle quote nel [pagina delle quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [sottoscrizione di Azure e limiti dei servizi](https://aka.ms/quotalimits) pagina. 

