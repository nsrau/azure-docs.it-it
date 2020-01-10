---
title: Richiedere un aumento dei limiti di quota di vCPU per ogni serie di macchine virtuali di Azure | Microsoft Docs
description: Questo articolo illustra come richiedere un aumento del limite di quota per ogni vCPU di VM.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750202"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota standard: aumentare i limiti per serie di macchine virtuali

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:
* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*. 

La quota di vCPU standard per le istanze con pagamento in base al consumo e per le VM riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:
* Il primo livello è il *limite di vCPU regionale totale* (in tutte le serie di macchine virtuali).
* Il secondo livello è il *limite di vCPU serie per macchina virtuale* (ad esempio, vCPU della serie dv3). 

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di macchine virtuali in questione. Inoltre, il numero totale di vCPU nuovi ed esistenti distribuiti in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita.

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità. 

Per altre informazioni sulle quote vCPU standard, vedere la pagina relativa alle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e ai [limiti del servizio e della sottoscrizione di Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Per informazioni su come aumentare il limite di vCPU in base all'area per la quota standard, vedere [quota standard: aumentare i limiti per area](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Per altre informazioni su come aumentare i limiti di vCPU per le VM spot, vedere [quota spot: aumentare i limiti per tutte le serie di macchine virtuali](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

È possibile richiedere un aumento del limite di quota di vCPU standard per ogni serie di VM in uno dei due modi, come descritto nelle sezioni successive.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Richiedere un aumento della quota standard dal riquadro "Guida e supporto"

Per richiedere un aumento della quota di vCPU standard per ogni serie di VM dal riquadro **Guida e supporto** , eseguire le operazioni seguenti: 

> [!NOTE]
> È anche possibile richiedere un aumento del limite di quota per più aree tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 8.

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com)selezionare **Guida e supporto**.

   ![Collegamento "Guida e supporto"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Nel riquadro **Guida e supporto** selezionare **nuova richiesta di supporto**. 

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Nell'elenco a discesa **tipo di problema** selezionare limiti per **servizio e sottoscrizione (quote)** .

   ![Elenco a discesa "tipo di problema"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Nell'elenco a discesa **sottoscrizione** selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Elenco a discesa "sottoscrizione"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Nell'elenco a discesa **tipo di quota** selezionare aumenta il **limite di sottoscrizione Compute-VM (Cores-vCPU)** . 

   ![Elenco a discesa "tipo di quota"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. In **Dettagli problema**nella scheda **Dettagli** selezionare **specificare i dettagli**, quindi immettere informazioni aggiuntive per elaborare la richiesta.

   ![Collegamento "fornire i dettagli"](./media/resource-manager-core-quotas-request/provide-details.png)

1. Nel riquadro **Dettagli quota** in alto a destra, procedere come segue:

   ![Riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/1-7.png)

   a. Nell'elenco a discesa **modello di distribuzione** selezionare il modello appropriato.

   b. Nell'elenco a discesa **percorsi** selezionare una località. Per il percorso selezionato, in **tipi**, nella casella **selezionare un tipo** immettere **standard**.

   ![Riquadro "Dettagli quota"-tipi di quota](./media/resource-manager-core-quotas-request/1-8.png)

   In **tipi**è possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni.
   
   Per altre informazioni su come aumentare i limiti di quota spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. Sotto l'elenco a discesa **standard** selezionare la serie di SKU di cui si desidera aumentare le quote.

   ![Riquadro "Dettagli quota"-serie SKU](./media/resource-manager-core-quotas-request/1-9.png)

   d. Immettere i nuovi limiti di quota desiderati per questa sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU oppure selezionare l'icona **Elimina** (X). 

   ![Casella di testo "limite nuovo vCPU"](./media/resource-manager-core-quotas-request/1-10.png)

1. Per richiedere un aumento della quota per più percorsi, selezionare un percorso aggiuntivo nell'elenco a discesa e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Percorsi aggiuntivi nel riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Richiedere un aumento della quota standard dal riquadro "sottoscrizioni"

Per richiedere un aumento della quota di vCPU standard per ogni serie di VM dal riquadro **sottoscrizioni** , eseguire le operazioni seguenti:

> [!NOTE]
> È anche possibile richiedere un aumento del limite di quota per più aree tramite un unico caso di supporto. Per informazioni dettagliate, vedere il passaggio 7.

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com)selezionare **sottoscrizioni**.

   ![Collegamento "sottoscrizioni"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Riquadro "sottoscrizioni"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Nel riquadro sinistro della pagina **\<nome sottoscrizione >** selezionare **utilizzo e quote**.

   ![Collegamento "utilizzo + quote"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. In alto a destra selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

1. Nell'elenco a discesa **tipo di quota** selezionare aumenta il **limite di sottoscrizione Compute-VM (Cores-vCPU)** .

   ![Elenco a discesa "tipo di quota"](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. Nel riquadro **Dettagli quota** in alto a destra, procedere come segue:

   ![Riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Nell'elenco a discesa **modello di distribuzione** selezionare il modello appropriato.

   b. Nell'elenco a discesa **percorsi** selezionare una località. 
   
   c. Per il percorso selezionato, in **tipi**selezionare **selezionare un tipo**, quindi selezionare la casella di controllo **standard** .

   ![Casella di controllo "standard"](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   In **tipi**è possibile richiedere sia i tipi di quota standard che quelli con priorità bassa da un singolo caso di supporto tramite supporto per più selezioni.
   
   Per altre informazioni su come aumentare i limiti di quota spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Sotto l'elenco a discesa **standard** selezionare la serie di SKU di cui si desidera aumentare le quote.

   ![Riquadro "Dettagli quota"-serie SKU](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Immettere i nuovi limiti di quota desiderati per questa sottoscrizione. Per rimuovere uno SKU dall'elenco, deselezionare la casella di controllo accanto allo SKU oppure selezionare **Elimina** (X). 

   ![Casella di testo "limite nuovo vCPU"](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Per richiedere un aumento della quota per più percorsi, selezionare un percorso aggiuntivo nell'elenco a discesa e quindi selezionare un tipo di macchina virtuale appropriato. 

   Con questo passaggio viene precaricata la serie di SKU selezionata per le posizioni precedenti. Immettere i limiti di quota che si desidera applicare alla serie aggiuntiva.
   
   ![Percorsi aggiuntivi nel riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.
