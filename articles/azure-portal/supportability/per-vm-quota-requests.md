---
title: Richiedere un aumento dei limiti di quota di vCPU per ogni serie di macchine virtuali di Azure
description: Come richiedere un aumento del limite di quota vCPU per una serie di macchine virtuali nella portale di Azure, che aumenta il limite di vCPU regionale totale per la stessa quantità.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843734"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota standard: aumentare i limiti per serie di macchine virtuali

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:

* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*.

La quota di vCPU standard per le istanze con pagamento in base al consumo e le macchine virtuali riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:

* Il primo livello è il *limite di vCPU regionale totale*, in tutte le serie di VM.
* Il secondo livello è il *limite di vCPU serie per macchina virtuale*, ad esempio la serie dv3 vCPU.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di macchine virtuali in questione. Inoltre, il numero totale di vCPU nuovi ed esistenti distribuiti in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita.

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità.

Per altre informazioni sulle quote vCPU standard, vedere la pagina relativa alle [quote vCPU della macchina virtuale](../../virtual-machines/windows/quotas.md) e ai [limiti del servizio e della sottoscrizione di Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Per informazioni su come aumentare il limite di vCPU in base all'area per la quota standard, vedere [quota standard: aumentare i limiti per area](regional-quota-requests.md).

Per altre informazioni su come aumentare i limiti di vCPU per le VM spot, vedere [quota spot: aumentare i limiti per tutte le serie di macchine virtuali](low-priority-quota.md).

È possibile richiedere un aumento del limite di quota di vCPU standard per ogni serie di VM in uno dei due modi, come descritto nelle sezioni riportate di seguito.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Richiedi un aumento della quota standard da guida e supporto tecnico

Per richiedere un aumento della quota di vCPU standard per serie di VM da **Guida e supporto tecnico**:

> [!NOTE]
> È anche possibile richiedere un aumento del limite di quota per più aree tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 8.

1. Scegliere **Guida e supporto**dal menu [portale di Azure](https://portal.azure.com) .

   ![Il collegamento alla guida e al supporto tecnico](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. In **Guida e supporto**selezionare **nuova richiesta di supporto**.

    ![Crea una nuova richiesta di supporto](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.

   ![Seleziona un tipo di problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Selezionare una sottoscrizione per una quota aumentata](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Per **tipo di quota**selezionare **aumenta il limite di sottoscrizione Compute-VM (Cores-vCPU)**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. Selezionare **specificare i dettagli** per immettere informazioni aggiuntive.

   ![Collegamento "fornire i dettagli"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nei **Dettagli della quota**, seguire questa procedura:

   ![Dettagli quota aggiuntiva TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Per **modello di distribuzione**selezionare il modello appropriato.

   1. Per **località**selezionare una località. Per il percorso selezionato, in **selezionare un tipo**in **tipi**scegliere **standard**.

      ![Dettagli quota-tipi di quota](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      In **tipi**è possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni.

      Per altre informazioni su come aumentare i limiti di quota spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/use-spot.md).

   1. In **standard**selezionare la serie SKU per aumentare le quote.

      ![Dettagli quota-serie SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Immettere i nuovi limiti di quota desiderati per questa sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU oppure selezionare l'icona Ignora "X".

      ![Selezionare un nuovo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Per richiedere un aumento della quota per più di un percorso, selezionare un percorso aggiuntivo in **località**, quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Specificare percorsi aggiuntivi in dettagli quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Richiedere un aumento della quota standard dalle sottoscrizioni

Per richiedere un aumento della quota di vCPU standard per serie di VM dalle **sottoscrizioni**:

> [!NOTE]
> È anche possibile richiedere un aumento del limite di quota per più aree tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 7.

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare **sottoscrizioni**.

   ![Sottoscrizioni nella ricerca portale di Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Sottoscrizioni da selezionare per le modifiche](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Nel riquadro sinistro selezionare utilizzo e **quote**.

   ![Collegamento "utilizzo + quote"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. In alto a destra selezionare **Richiedi aumento**.

   ![Selezionare per aumentare la quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Per **tipo di quota**selezionare **aumenta il limite di sottoscrizione Compute-VM (Cores-vCPU)**.

   ![Selezionare un tipo di quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Nei **Dettagli della quota**, seguire questa procedura:

   1. Per **modello di distribuzione**selezionare il modello appropriato e per **percorsi**Selezionare una località.

      ![Specificare i dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Per il percorso selezionato, in **tipi**selezionare **Seleziona un tipo**e quindi selezionare **standard**.

      ![Seleziona tipo standard](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      In **tipi**è possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni.

      Per altre informazioni su come aumentare i limiti di quota spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/use-spot.md).

   1. Per **standard**selezionare la serie di SKU di cui si desidera aumentare le quote.

      ![Dettagli quota-serie SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Immettere i nuovi limiti di quota desiderati per questa sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU oppure selezionare l'icona Ignora "X".

      ![Selezionare un nuovo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Per richiedere un aumento della quota per più di un percorso, selezionare un percorso aggiuntivo in **località**, quindi selezionare un tipo di macchina virtuale appropriato.

   Con questo passaggio viene precaricata la serie di SKU selezionata per le posizioni precedenti. Immettere i limiti di quota che si desidera applicare alla serie aggiuntiva.

   ![Selezionare i percorsi aggiuntivi in dettagli quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.
