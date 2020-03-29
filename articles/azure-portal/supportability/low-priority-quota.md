---
title: Spot virtual machine quota - Azure
description: Aumentare i limiti di quota per le macchine virtuali a spot, che forniscono un modello di utilizzo di Azure che consente di assumere costi inferiori in cambio della rimozione delle macchine virtuali in base alle esigenze in Azure.Increase quota limits for spot VMs, which provide a model of Azure usage that lets you assume lower costs in exchange for letting Azure remove VMs as needed.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842785"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota spot: aumentare i limiti per tutte le serie di macchine virtualiSpot quota: Increase limits for all VM series

Le macchine virtuali spot (VM) forniscono un modello diverso di utilizzo di Azure.Spot virtual machines (VMs) provide a different model of Azure usage. Consentono di assumere costi inferiori in cambio della rimozione delle macchine virtuali da parte di Azure in base alle esigenze per le distribuzioni di istanze di macchine virtuali riservate o con pagamento in base al costo. Per altre informazioni sulle macchine virtuali con spot, vedere Macchine virtuali di Azure spot per set di scalabilità di [macchine virtuali.](../../virtual-machine-scale-sets/use-spot.md)

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:Azure Resource Manager supports two types of vCPU quotas for virtual machines:

* Le macchine virtuali con *pagamento in base al consumo* e le *istanze di macchine virtuali riservate* sono soggette a una quota *vCPU standard.*
* *Le macchine virtuali spot* sono soggette a una *quota vCPU spot.*

Per il tipo di quota vCPU spot, le quote vCPU di Resource Manager vengono applicate a tutte le serie di macchine virtuali disponibili come limite regionale singolo.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale della vCPU nuovo ed esistente per tutte le istanze di vm spot non deve superare il limite di quota vCPU spot approvato. Se la quota spot viene superata, la distribuzione della macchina virtuale spot non è consentita.

Questo articolo illustra come richiedere un aumento del limite di quota vCPU spot tramite il portale di Azure.This article discusses how to request an increase in the spot vCPU quota limit by using the Azure portal.

Per altre informazioni sulle quote vCPU standard, vedere [Quote vCPU](../../virtual-machines/windows/quotas.md) della macchina virtuale e [Limiti, quote e vincoli](../../azure-resource-manager/management/azure-subscription-service-limits.md)della sottoscrizione e del servizio di Azure.

Per informazioni sull'aumento del limite vCPU per regione, vedere [Quota standard: aumentare i limiti per regione](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Richiedere un aumento del limite di quota da parte di Aiuto - Supporto

Per richiedere un aumento del limite di quota spot per tutte le serie di macchine virtuali utilizzando **Guida e supporto:**

> [!NOTE]
> È inoltre possibile richiedere un aumento del limite di quota per più aree tramite un singolo caso di supporto. Per informazioni dettagliate, vedere il passaggio 8.

1. Dal menu del portale di [Azure,](https://portal.azure.com) selezionare **Guida e supporto**.

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

   1. Per **Modello di distribuzione**, selezionare il modello appropriato e in **Posizioni**selezionare una posizione.

      ![Fornire ulteriori dettagli sulla quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Per la posizione selezionata, in **Tipi**, in **Selezionare un tipo,** scegliere **Spot**.

      ![Selezionare il tipo di punto](./media/resource-manager-core-quotas-request/select-spot-type.png)

       In **Tipi**, è possibile richiedere tipi di quote standard e spot da un singolo caso di supporto tramite il supporto per la selezione multipla.

       Per altre informazioni, vedere [Quota standard: aumentare i limiti in base](per-vm-quota-requests.md)alla serie di macchine virtuali.

   1. Immettere il nuovo limite di quota desiderato per la sottoscrizione.

      ![Selezionare una nuova quota per la macchina virtuale spotSelect a new quota for spot VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Per richiedere un aumento della quota per più di una posizione, selezionare un percorso aggiuntivo in **Percorsi**e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla posizione aggiuntiva.

   ![Specificare posizioni aggiuntive nei dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continuare** a creare la richiesta di supporto.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Richiedere un aumento del limite di quota dal riquadro Sottoscrizioni

Per richiedere un aumento del limite di quota spot per tutte le serie di macchine virtuali dal riquadro **Sottoscrizioni:To request** a spot quota limit increase for all VM series from the Subscriptions pane:

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

1. Selezionare **Avanti: Soluzioni** per aprire **DETTAGLI PROBLEMA**. Selezionare **Fornisci dettagli** per immettere informazioni aggiuntive. In **Dettagli quota**immettere le informazioni seguenti:

   1. Per **Modello di distribuzione**, selezionare il modello appropriato e in **Posizioni**selezionare una posizione.

      ![Fornire i dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Per la posizione selezionata, in **Tipi**, in **Selezionare un tipo,** scegliere **Spot**.

      ![Selezionare Tipo di macchia](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Per altre informazioni, vedere [Quota standard: aumentare i limiti in base](per-vm-quota-requests.md)alla serie di macchine virtuali.

   1. Immettere il nuovo limite di quota desiderato per la sottoscrizione.

      ![Immettere un nuovo valore per il limite vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Per richiedere un aumento della quota per più di una posizione, selezionare un percorso aggiuntivo in **Percorsi**e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla posizione aggiuntiva.

   ![Selezionare altre posizioni nei dettagli della quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selezionare **Salva e continuare** a creare la richiesta di supporto.
