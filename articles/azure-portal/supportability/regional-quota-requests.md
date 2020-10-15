---
title: Richiedere un aumento dei limiti di quota di vCPU regionali di Azure
description: Come richiedere un aumento del limite di quota vCPU per un'area nel portale di Azure.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: bd4a770aee43709c46ab4c1baf965941d8d2c3ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763857"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota standard: aumentare i limiti per area

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:

* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*.

La quota di vCPU standard per le istanze con pagamento in base al consumo e le macchine virtuali riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:

* Il primo livello è il *limite di vCPU regionale totale*, in tutte le serie di VM.
* Il secondo livello è il *limite di vCPU serie per macchina virtuale*, ad esempio la serie D vCPU.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di macchine virtuali in questione. Inoltre, il numero totale di vCPU nuovi ed esistenti distribuiti in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita.

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità.

Quando si crea una nuova sottoscrizione, il numero totale predefinito di vCPU regionali potrebbe non essere uguale alla quota vCPU predefinita totale per tutte le singole serie di macchine virtuali. Questa discrepanza può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di macchine virtuali che si desidera distribuire. Ma potrebbe non essere disponibile una quota sufficiente a contenere la vCPU regionale totale per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare in modo esplicito il limite del numero totale di vCPU regionali. Il limite di vCPU regionale totale non può superare la quota approvata totale tra tutte le serie di macchine virtuali per l'area.

Per altre informazioni sulle quote vCPU standard, vedere [quote di vCPU della macchina virtuale](../../virtual-machines/windows/quotas.md) e [sottoscrizione di Azure e limiti, quote e vincoli del servizio](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Per altre informazioni su come aumentare i limiti di vCPU per le VM spot, vedere [quota spot: aumentare i limiti per tutte le serie di macchine virtuali](low-priority-quota.md).

È possibile richiedere un aumento del limite di quota standard di vCPU in base all'area in uno dei due modi.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Richiedi un aumento della quota per area da guida e supporto

Per richiedere un aumento della quota di vCPU per area da **Guida e supporto tecnico**:

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Guida e supporto**.

   ![Collegamento "Guida e supporto"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. In **Guida e supporto** selezionare **Nuova richiesta di supporto**.

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Per **tipo di problema**selezionare **limiti per servizio e sottoscrizione (quote)**.

   ![Selezionare un tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare una sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per il **tipo di quota**selezionare **altre richieste**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. In **Descrizione**specificare le informazioni seguenti:

    1. Per **modello di distribuzione**specificare **Gestione risorse**.  
    1. Per **Region (area**) specificare l'area obbligatoria, ad esempio **Stati Uniti orientali 2**.  
    1. Per il **nuovo limite**, specificare un nuovo limite di vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie di SKU per questa sottoscrizione.

    ![Immettere i dettagli per la richiesta di quota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Verifica + crea** per continuare a creare la richiesta di supporto.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Richiedere un aumento della quota per area dalle sottoscrizioni

Per richiedere un aumento della quota di vCPU per area dalle **sottoscrizioni**:

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **sottoscrizioni**.

   ![Passare a sottoscrizioni nella portale di Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare una sottoscrizione da modificare](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Nel riquadro sinistro selezionare utilizzo e **quote**.

   ![Segui il collegamento a utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. In alto a destra selezionare **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. In **tipo di quota**selezionare **altre richieste**.

   ![Selezionare il tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. Nella casella **Descrizione** specificare le informazioni aggiuntive seguenti:

    1. Per **modello di distribuzione**specificare **Gestione risorse**.  
    1. Per **Region (area**) specificare l'area obbligatoria, ad esempio **Stati Uniti orientali 2**.  
    1. Per il **nuovo limite**, specificare un nuovo limite di vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie di SKU per questa sottoscrizione.

    ![Immettere le informazioni in dettagli](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Verifica + crea** per continuare a creare la richiesta di supporto.
