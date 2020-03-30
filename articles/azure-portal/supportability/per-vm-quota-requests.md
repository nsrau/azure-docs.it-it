---
title: Richiedere un aumento dei limiti di quota vCPU per ogni serie di macchine virtuali di AzureRequest an increase in vCPU quota limits per Azure VM series
description: Come richiedere un aumento del limite di quota vCPU per una serie di macchine virtuali nel portale di Azure, che aumenta il limite totale della vCPU regionale della stessa quantità.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843734"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota standard: aumentare i limiti per serie di macchine virtualiStandard quota: Increase limits by VM series

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:Azure Resource Manager supports two types of vCPU quotas for virtual machines:

* Le macchine virtuali con *pagamento in base al consumo* e le *istanze di macchine virtuali riservate* sono soggette a una quota *vCPU standard.*
* *Le macchine virtuali spot* sono soggette a una *quota vCPU spot.*

La quota vCPU standard per le istanze di macchine virtuali con pagamento in base al consumo e riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:The standard vCPU quota for pay-as-you-go and reserved virtual machine instances is enforced at two tiers for each subscription in each region:

* Il primo livello è il *limite totale delle vCPU regionali,* in tutte le serie di macchine virtuali.
* Il secondo livello è il limite di vCPU della *serie per MACCHINA,* ad esempio le vCPU della serie Dv3.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale della vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota vCPU approvata per quella particolare serie di macchine virtuali. Inoltre, il numero totale di VCPU nuove ed esistenti distribuite in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita.

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure.You can request an increase in the vCPU quota limit for the VM series by using the Azure portal. Un aumento della quota della serie VM aumenta automaticamente il limite totale della vCPU regionale della stessa quantità.

Per altre informazioni sulle quote vCPU standard, vedere Quote vCPU della macchina virtuale e Limiti della sottoscrizione e del servizio di sottoscrizione e servizio di Azure.To learn more about standard vCPU quotas, see [Virtual machine vCPU quotas](../../virtual-machines/windows/quotas.md) and [Azure subscription and service limits](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Per informazioni sull'aumento del limite vCPU per regione per la quota standard, vedere [Quota standard: aumentare i limiti per regione](regional-quota-requests.md).

Per altre informazioni sull'aumento dei limiti di vCPU spot VM, vedere [Spot quota: Increase limits for all VM series](low-priority-quota.md).

È possibile richiedere un aumento dei limiti di quota vCPU standard per ogni serie di macchine virtuali in due modi, come descritto nelle sezioni seguenti.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Richiedere un aumento della quota standard da parte di Aiuto e supporto

Per richiedere un aumento della quota vCPU standard per ogni serie di macchine virtuali da **Parte di Guida e supporto:**

> [!NOTE]
> È inoltre possibile richiedere un aumento del limite di quota per più aree tramite un singolo caso di supporto. Per informazioni dettagliate, vedere il passaggio 8.

1. Nel menu [del portale](https://portal.azure.com) di Azure selezionare Guida **e supporto**.

   ![Il link Aiuto e supporto](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. In **Guida e supporto**selezionare Nuova richiesta di **supporto**.

    ![Creare una nuova richiesta di supportoCreate a new support request](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. In **Sottoscrizione**selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Selezionare una sottoscrizione per una quota maggiore](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per **Tipo di quota**, selezionare Aumenta il limite di sottoscrizione di VM di **calcolo (core-vCPU)**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. Selezionare **Fornisci dettagli** per immettere informazioni aggiuntive.

   ![Il link "Fornisci dettagli"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. In **Dettagli quota**eseguire la procedura seguente:

   ![TFornire ulteriori dettagli sulla quota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Per **Modello di distribuzione**, selezionare il modello appropriato.

   1. In **Posizioni**, selezionare una posizione. Per la posizione selezionata, in **Tipi**, in **Selezionare un tipo**scegliere **Standard**.

      ![Dettagli quota - tipi di quota](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      In **Tipi**, è possibile richiedere tipi di quote standard e spot da un singolo caso di supporto tramite il supporto per la selezione multipla.

      Per altre informazioni sull'aumento dei limiti di quota spot, vedere Macchine virtuali con spot di Azure per set di scalabilità di [macchine virtuali.](../../virtual-machine-scale-sets/use-spot.md)

   1. In **Standard**selezionare la serie SKU per aumentare le quote.

      ![Dettagli quota - Serie SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Immettere i nuovi limiti di quota desiderati per la sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU o selezionare l'icona di eliminazione "X".

      ![Selezionare un nuovo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Per richiedere un aumento della quota per più di una posizione, selezionare un percorso aggiuntivo in **Percorsi**e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla posizione aggiuntiva.

   ![Specificare posizioni aggiuntive nei dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continuare** a creare la richiesta di supporto.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Richiedere un aumento della quota standard da Abbonamenti

Per richiedere un aumento della quota vCPU standard per serie di macchine virtuali da **Abbonamenti:**

> [!NOTE]
> È inoltre possibile richiedere un aumento del limite di quota per più aree tramite un singolo caso di supporto. Per informazioni dettagliate, vedere il passaggio 7.

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Sottoscrizioni**.

   ![Sottoscrizioni nella ricerca nel portale di AzureSubscriptions in the Azure portal search](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si desidera aumentare la quota.

   ![Sottoscrizioni da selezionare per le modifiche](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Nel riquadro di sinistra, selezionare **Utilizzo e quote**.

   ![Il collegamento "Utilizzo e quote"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. In alto a destra, seleziona **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Per **Tipo di quota**, selezionare Aumenta il limite di sottoscrizione di VM di **calcolo (core-vCPU)**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. In **Dettagli quota**eseguire la procedura seguente:

   1. Per **Modello di distribuzione**, selezionare il modello appropriato e in **Posizioni**selezionare una posizione.

      ![Fornire i dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Per la posizione selezionata, in **Tipi**selezionare **Seleziona un tipo**, quindi **Standard**.

      ![Seleziona tipo standard](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      In **Tipi**, è possibile richiedere tipi di quote standard e spot da un singolo caso di supporto tramite il supporto per la selezione multipla.

      Per altre informazioni sull'aumento dei limiti di quota spot, vedere Macchine virtuali con spot di Azure per set di scalabilità di [macchine virtuali.](../../virtual-machine-scale-sets/use-spot.md)

   1. Per **Standard**, selezionare la serie di SKU di cui si desidera aumentare le quote.

      ![Dettagli quota - Serie SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Immettere i nuovi limiti di quota desiderati per la sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU o selezionare l'icona di eliminazione "X".

      ![Selezionare un nuovo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Per richiedere un aumento della quota per più di una posizione, selezionare un percorso aggiuntivo in **Percorsi**e quindi selezionare un tipo di macchina virtuale appropriato.

   Questo passaggio precarica la serie di SKU selezionata per le ubicazioni precedenti. Immettere i limiti di quota che si desidera applicare alla serie aggiuntiva.

   ![Selezionare altre posizioni nei dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continuare** a creare la richiesta di supporto.
