---
title: Richiedere un aumento dei limiti di quota vCPU regionali di AzureRequest an increase in Azure regional vCPU quota limits
description: Come richiedere un aumento del limite di quota vCPU per un'area nel portale di Azure.How to request an increase in the vCPU quota limit for a region in the Azure portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843685"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota standard: aumentare i limiti per regione

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:Azure Resource Manager supports two types of vCPU quotas for virtual machines:

* Le macchine virtuali con *pagamento in base al consumo* e le *istanze di macchine virtuali riservate* sono soggette a una quota *vCPU standard.*
* *Le macchine virtuali spot* sono soggette a una *quota vCPU spot.*

La quota vCPU standard per le istanze di macchine virtuali con pagamento in base al consumo e riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:The standard vCPU quota for pay-as-you-go and reserved virtual machine instances is enforced at two tiers for each subscription in each region:

* Il primo livello è il *limite totale delle vCPU regionali,* in tutte le serie di macchine virtuali.
* Il secondo livello è il limite di vCPU della *serie per MACCHINA,* ad esempio le vCPU della serie D.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale della vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota vCPU approvata per quella particolare serie di macchine virtuali. Inoltre, il numero totale di VCPU nuove ed esistenti distribuite in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita.

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure.You can request an increase in the vCPU quota limit for the VM series by using the Azure portal. Un aumento della quota della serie VM aumenta automaticamente il limite totale della vCPU regionale della stessa quantità.

Quando si crea una nuova sottoscrizione, il numero totale predefinito di vCPU regionali potrebbe non essere uguale alla quota vCPU predefinita totale per tutte le singole serie di macchine virtuali. Questa discrepanza può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di macchine virtuali che si vuole distribuire. Tuttavia, la quota potrebbe non essere sufficiente per supportare le vCPU regionali totali per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare in modo esplicito il limite del numero totale di vCPU regionali. Il limite totale della vCPU regionale non può superare la quota totale approvata in tutte le serie di macchine virtuali per l'area.

Per altre informazioni sulle quote vCPU standard, vedere [Quote vCPU](../../virtual-machines/windows/quotas.md) della macchina virtuale e [Limiti, quote e vincoli](../../azure-resource-manager/management/azure-subscription-service-limits.md)della sottoscrizione e del servizio di Azure.

Per altre informazioni sull'aumento dei limiti di vCPU spot VM, vedere [Spot quota: Increase limits for all VM series](low-priority-quota.md).

È possibile richiedere un aumento del limite di quota standard vCPU per regione in uno dei due modi.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Richiedere un aumento della quota per regione da parte di Aiuto e supporto

Per richiedere un aumento della quota vCPU per regione da **Aiuto e supporto**:

1. Dal menu del portale di [Azure,](https://portal.azure.com) selezionare **Guida e supporto**.

   ![Il link "Aiuto e supporto"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. In **Guida e supporto**selezionare Nuova richiesta di **supporto**.

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. In **Sottoscrizione**selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare una sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per Il **tipo di quota**, selezionare Altre **richieste**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. In **Descrizione**specificare le informazioni seguenti:

    1. Per **Modello di distribuzione**, specificare Resource **Manager**.  
    1. Per **Regione**, specificare l'area desiderata, ad esempio **Stati Uniti orientali 2**.  
    1. Per **Nuovo limite**, specificare un nuovo limite vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie Di KU per questa sottoscrizione.

    ![Immettere i dettagli per la richiesta di quota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Revisione e creare** per continuare a creare la richiesta di supporto.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Richiedere un aumento della quota per regione da Abbonamenti

Per richiedere un aumento della quota vCPU per regione da **Abbonamenti**:

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Sottoscrizioni**.

   ![Passare a Sottoscrizioni nel portale di AzureGo to Subscriptions in the Azure portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare una sottoscrizione da modificare](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Nel riquadro di sinistra, selezionare **Utilizzo e quote**.

   ![Collegamento Segui utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. In alto a destra, seleziona **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Da **Tipo di quota**selezionare Altre **richieste**.

   ![Selezionare il tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. Nella casella **Descrizione** immettere le informazioni aggiuntive seguenti:

    1. Per **Modello di distribuzione**, specificare Resource **Manager**.  
    1. Per **Regione**, specificare l'area desiderata, ad esempio **Stati Uniti orientali 2**.  
    1. Per **Nuovo limite**, specificare un nuovo limite vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie Di KU per questa sottoscrizione.

    ![Inserisci le informazioni nei dettagli](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Revisione e creare** per continuare a creare la richiesta di supporto.
