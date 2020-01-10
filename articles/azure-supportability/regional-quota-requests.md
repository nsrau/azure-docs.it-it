---
title: Richiedere un aumento dei limiti di quota di vCPU regionali di Azure | Microsoft Docs
description: Richieste di aumento della quota a livello di area
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750835"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota standard: aumentare i limiti per area 

Azure Resource Manager supporta due tipi di quote vCPU per le macchine virtuali:
* Le *macchine virtuali con pagamento in base* al consumo e le *istanze di VM riservate* sono soggette a una *quota vCPU standard*.
* Le *macchine virtuali spot* sono soggette a una *quota vCPU di spot*. 

La quota di vCPU standard per le istanze con pagamento in base al consumo e per le VM riservate viene applicata a due livelli per ogni sottoscrizione in ogni area:
* Il primo livello è il *limite di vCPU regionale totale* (in tutte le serie di macchine virtuali).
* Il secondo livello è il *limite di vCPU serie per macchina virtuale* (ad esempio, la serie D vCPU).
 
Ogni volta che si distribuisce una nuova macchina virtuale spot, l'utilizzo totale nuovo ed esistente di vCPU per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di macchine virtuali in questione. Inoltre, il numero totale di vCPU nuovi ed esistenti distribuiti in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non è consentita. 

È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali usando il portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità.

Quando si crea una nuova sottoscrizione, il numero totale predefinito di vCPU regionali potrebbe non essere uguale alla quota vCPU predefinita totale per tutte le singole serie di macchine virtuali. Questa discrepanza può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di macchine virtuali che si desidera distribuire. Ma potrebbe non essere disponibile una quota sufficiente a contenere la vCPU regionale totale per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare in modo esplicito il limite del numero totale di vCPU regionali. Il limite di vCPU regionale totale non può superare la quota approvata totale tra tutte le serie di macchine virtuali per l'area.

Per altre informazioni sulle quote vCPU standard, vedere la pagina relativa alle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e ai [limiti del servizio e della sottoscrizione di Azure](https://aka.ms/quotalimits).

Per altre informazioni su come aumentare i limiti di vCPU per le VM spot, vedere [quota spot: aumentare i limiti per tutte le serie di macchine virtuali](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

È possibile richiedere un aumento del limite di quota standard di vCPU in base all'area in uno dei due modi, come descritto nelle sezioni successive.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Richiedere un aumento della quota per area dal riquadro "Guida e supporto"

Per richiedere un aumento della quota di vCPU per area dal riquadro **Guida e supporto** , eseguire le operazioni seguenti: 

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com)selezionare **Guida e supporto**.

   ![Collegamento "Guida e supporto"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. Nel riquadro **Guida e supporto** selezionare **nuova richiesta di supporto**. 

    ![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Nell'elenco a discesa **tipo di problema** selezionare limiti per **servizio e sottoscrizione (quote)** .

   ![Elenco a discesa "tipo di problema"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Nell'elenco a discesa **sottoscrizione** selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Elenco a discesa "sottoscrizione"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Nell'elenco a discesa **tipo di quota** selezionare **altre richieste**.

   ![Elenco a discesa "tipo di quota"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Nella casella **Descrizione** del riquadro **Dettagli problema** specificare le informazioni aggiuntive seguenti: 

    a. Per **modello di distribuzione**specificare **Gestione risorse**.  
    b. Per **Region (area**) specificare l'area obbligatoria (ad esempio, **Stati Uniti orientali 2**).  
    c. Per il **nuovo limite**, specificare un nuovo limite di vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie di SKU per questa sottoscrizione.

    ![Riquadro "Dettagli problema"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Richiedere un aumento della quota per area dal riquadro "sottoscrizioni"

Per richiedere un aumento della quota di vCPU per area dal riquadro **sottoscrizioni** , eseguire le operazioni seguenti: 

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com)selezionare **sottoscrizioni**.

   ![Collegamento "sottoscrizioni"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selezionare la sottoscrizione di cui si vuole aumentare la quota.

   ![Riquadro "sottoscrizioni"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. Nel riquadro sinistro della pagina **\<nome sottoscrizione >** selezionare **utilizzo e quote**.

   ![Collegamento "utilizzo + quote"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. In alto a destra selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

1. Nell'elenco a discesa **tipo di quota** selezionare **altre richieste**.

   ![Elenco a discesa "tipo di quota"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Nella casella **Descrizione** del riquadro **Dettagli problema** specificare le informazioni aggiuntive seguenti: 

    a. Per **modello di distribuzione**specificare **Gestione risorse**.  
    b. Per **Region (area**) specificare l'area obbligatoria (ad esempio, **Stati Uniti orientali 2**).  
    c. Per il **nuovo limite**, specificare un nuovo limite di vCPU per l'area. Questo valore non deve superare la somma delle quote approvate per le singole serie di SKU per questa sottoscrizione.

    ![Riquadro "Dettagli problema"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selezionare **Salva e continua** per continuare a creare la richiesta di supporto.

