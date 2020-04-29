---
title: Quota della macchina virtuale spot-Azure
description: Aumentare i limiti di quota per le VM spot, che offrono un modello di utilizzo di Azure che consente di presupporre costi inferiori in cambio per consentire ad Azure di rimuovere le VM in base alle esigenze.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76842785"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota spot: aumenta i limiti per tutte le serie di macchine virtuali

Le macchine virtuali (VM) spot forniscono un modello diverso di utilizzo di Azure. Consentono di presupporre costi inferiori in cambio per consentire ad Azure di rimuovere le macchine virtuali in base alle esigenze per le distribuzioni con pagamento in base al consumo o per le istanze di VM riservate. Per altre informazioni sulle macchine virtuali spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:

* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*.

Per il tipo di quota vCPU spot, le quote di vCPU Gestione risorse vengono applicate a tutte le serie di macchine virtuali disponibili come singolo limite a livello di area.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tutte le istanze di VM spot non deve superare il limite di quota vCPU di punti approvati. Se la quota spot viene superata, la distribuzione della macchina virtuale spot non è consentita.

Questo articolo illustra come richiedere un aumento del limite di quota vCPU per spot usando il portale di Azure.

Per altre informazioni sulle quote vCPU standard, vedere [quote di vCPU della macchina virtuale](../../virtual-machines/windows/quotas.md) e [sottoscrizione di Azure e limiti, quote e vincoli del servizio](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Per informazioni su come aumentare il limite di vCPU in base all'area, vedere [quota standard: aumentare i limiti per area](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Richiedi un aumento del limite di quota da guida e supporto

Per richiedere un aumento del limite di quota spot per tutte le serie di macchine virtuali usando **Guida e supporto tecnico**:

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

1. In **Dettagli quota**seguire questa procedura:

   1. Per **modello di distribuzione**selezionare il modello appropriato e per **percorsi**Selezionare una località.

      ![Fornire dettagli aggiuntivi sulla quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Per il percorso selezionato, in **selezionare un tipo**in **tipi**scegliere **spot**.

      ![Seleziona tipo di spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

       In **tipi**è possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni.

       Per altre informazioni, vedere [quota standard: aumentare i limiti per serie di macchine virtuali](per-vm-quota-requests.md).

   1. Immettere il nuovo limite di quota desiderato per la sottoscrizione.

      ![Selezionare una nuova quota per la VM spot](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Per richiedere un aumento della quota per più di un percorso, selezionare un percorso aggiuntivo in **località**, quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Specificare percorsi aggiuntivi in dettagli quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Richiedere un aumento del limite di quota dal riquadro sottoscrizioni

Per richiedere un aumento del limite di quota spot per tutte le serie di macchine virtuali dal riquadro **sottoscrizioni** :

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

1. Selezionare **Avanti: soluzioni** per aprire **i dettagli del problema**. Selezionare **specificare i dettagli** per immettere informazioni aggiuntive. In **Dettagli quota**immettere le informazioni seguenti:

   1. Per **modello di distribuzione**selezionare il modello appropriato e per **percorsi**Selezionare una località.

      ![Specificare i dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Per il percorso selezionato, in **selezionare un tipo**in **tipi**scegliere **spot**.

      ![Seleziona tipo di spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Per altre informazioni, vedere [quota standard: aumentare i limiti per serie di macchine virtuali](per-vm-quota-requests.md).

   1. Immettere il nuovo limite di quota desiderato per la sottoscrizione.

      ![Immettere un nuovo valore per il limite vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Per richiedere un aumento della quota per più di un percorso, selezionare un percorso aggiuntivo in **località**, quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Selezionare i percorsi aggiuntivi in dettagli quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.
