---
title: Quota della macchina virtuale spot | Microsoft Docs
description: Aumentare i limiti di quota effettuando richieste di quota spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897299"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota spot: aumenta i limiti per tutte le serie di macchine virtuali

Le macchine virtuali (VM) spot forniscono un modello diverso di utilizzo di Azure. Consentono di presupporre costi inferiori in cambio per consentire ad Azure di rimuovere le VM in base alle esigenze per le distribuzioni con pagamento in base al consumo o per le istanze di VM riservate. Per altre informazioni sulle macchine virtuali spot, vedere [macchine virtuali di Azure spot per i set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:
* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*. 

Per il tipo di *quota vCPU spot* , gestione risorse le quote vCPU vengono applicate a tutte le serie di macchine virtuali disponibili come singolo limite a livello di area.

Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tutte le istanze di VM spot non deve superare il limite di quota vCPU di punti approvati. Se la quota spot viene superata, la distribuzione della macchina virtuale spot non è consentita. 

Questo articolo illustra come richiedere un aumento del limite di quota vCPU per spot usando il portale di Azure. 

Per altre informazioni sulle quote vCPU standard, vedere la pagina relativa alle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e ai [limiti del servizio e della sottoscrizione di Azure](https://aka.ms/quotalimits). 

Per informazioni sull'aumento del limite vCPU in base all'area, vedere [quota standard: aumento del limite di vCPU a livello](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)di area.

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Richiedere un aumento del limite di quota dal riquadro "Guida e supporto" 

Per richiedere un aumento del limite di quota spot per tutte le serie di VM dal riquadro **Guida e supporto** , eseguire le operazioni seguenti:

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

1. Selezionare la scheda **Dettagli** e quindi in **Dettagli problema**Selezionare **specificare i dettagli**e immettere informazioni aggiuntive per elaborare la richiesta.

   ![Collegamento "fornire i dettagli"](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. Nel riquadro **Dettagli quota** in alto a destra, procedere come segue:

   ![Riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/3-7.png)

   a. Nell'elenco a discesa **modello di distribuzione** selezionare il modello appropriato.

   b. Nell'elenco a discesa **percorsi** selezionare una località. Per il percorso selezionato, in **tipi**, nella casella **selezionare un tipo** immettere **spot**. 
   
   ![Scheda Dettagli "nuova richiesta di supporto"](./media/resource-manager-core-quotas-request/3-8.png)

    In **tipi**è possibile richiedere i tipi di quota standard e spot da un singolo caso di supporto tramite supporto per più selezioni. 
    
    Per altre informazioni, vedere la pagina relativa alla [quota standard: aumento del limite di vCPU per serie di macchine virtuali](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Immettere il nuovo limite di quota desiderato per la sottoscrizione. 
 
   ![Casella di testo "limite nuovo vCPU"](./media/resource-manager-core-quotas-request/3-9.png)

1. Per richiedere un aumento della quota per più percorsi, selezionare un percorso aggiuntivo nell'elenco a discesa e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Percorsi aggiuntivi nel riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/3-10.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Richiedere un aumento del limite di quota dal riquadro "sottoscrizioni"

Per richiedere un aumento del limite di quota spot per tutte le serie di macchine virtuali dal riquadro **sottoscrizioni** , eseguire le operazioni seguenti:

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

   ![Riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Nell'elenco a discesa **modello di distribuzione** selezionare il modello appropriato.

   b. Nell'elenco a discesa **percorsi** selezionare una località. 
   
   c. Per il percorso selezionato, in **tipi**, nella casella **selezionare un tipo** immettere **spot**.

   ![Riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/3-2-7.png)

   Per altre informazioni, vedere la pagina relativa alla [quota standard: aumento del limite di vCPU per serie di macchine virtuali](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Immettere il nuovo limite di quota desiderato per la sottoscrizione.

   ![Casella di testo "limite nuovo vCPU"](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Per richiedere un aumento della quota per più percorsi, selezionare un percorso aggiuntivo nell'elenco a discesa e quindi selezionare un tipo di macchina virtuale appropriato. È quindi possibile immettere un limite che si applica alla località aggiuntiva.

   ![Percorsi aggiuntivi nel riquadro "Dettagli quota"](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.


