---
title: Richieste di aumento della quota a livello di area di Azure | Microsoft Docs
description: Richieste di aumento della quota a livello di area
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310658"
---
# <a name="total-regional-vcpu-limit-increase"></a>Aumento del limite di vCPU totali di un'area 

Le quote di vCPU per Resource Manager per macchine virtuali e set di scalabilità di macchine virtuali sono imposte a due livelli per ogni sottoscrizione, in ogni area. 

Il primo livello è il **limite di Vcpu regionale totale** (in tutte le serie di VM), e il secondo livello contiene i **per ogni serie di macchine Virtuali Vcpu limitare** (ad esempio i Vcpu serie D). Qualsiasi momento che una nuova macchina virtuale deve essere distribuita, la somma dell'utilizzo di CPU virtuali nuove ed esistenti per tale serie di macchine Virtuali non deve superare la quota di vCPU approvata per quel particolare serie di macchine Virtuali. Inoltre, il conteggio totale vCPU nuovi ed esistenti distribuito in tutte le macchine virtuali della serie non deve superare la quota Vcpu regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile.
È possibile richiedere un aumento del limite della quota di Vcpu per la serie di macchine Virtuali dal portale di Azure. Un aumento della quota di macchine virtuali della serie di aumenta automaticamente il limite di Vcpu regionale totale dello stesso importo. 

Quando viene creata una nuova sottoscrizione, i Vcpu regionale totale predefinito potrebbero non essere uguale alla somma delle quote di vCPU predefinito per tutte le serie di macchine Virtuali singole. Ciò può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si desidera distribuire, ma non sono sufficienti quote di Vcpu regionali totali per tutte le distribuzioni. In questo caso, è necessario inviare una richiesta per aumentare il limite di Vcpu regionali totali in modo esplicito. Limite di Vcpu regionale totale non può superare la somma della quota approvato in tutte le serie VM per l'area.

Altre informazioni sulle quote nel [pagina delle quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [sottoscrizione di Azure e limiti dei servizi](https://aka.ms/quotalimits) pagina. 

È ora possibile richiedere un aumento tramite **Guida e supporto** pannello o il **gli utilizzi e quote** pannello nel portale. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Richiedi aumento della quota Vcpu regionale totale a livello di sottoscrizione, utilizzando il **Guida e supporto** pannello

Seguire le istruzioni seguenti per creare una richiesta di supporto tramite Azure 'Guida e supporto' blade disponibile nel portale di Azure. 

1. Dal https://portal.azure.com, selezionare **Guida e supporto**.

![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema, scegli **limiti di servizio e sottoscrizione (quote)** .

![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Selezionare la sottoscrizione newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **richieste altri** nelle **tipo di quota** elenco a discesa.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Nelle **dettagli** riquadro forniscono informazioni aggiuntive come nell'esempio di seguito, allo scopo di processo di richiesta e continuare con la creazione di case. 
    1.  **Modello di distribuzione** – specificare "Resource Manager"
    2.  **Area richiesta** : specificare l'area necessaria, ad esempio Stati Uniti orientali 2
    3.  **Nuovo valore di limite** – specificare nuovi limiti per area. Questo non deve superare la somma della quota approvato per singoli famiglie di SKU per questa sottoscrizione

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Richiedi aumento della quota Vcpu regionale totale a livello di sottoscrizione, utilizzando il **utilizzi e quote** pannello

Seguire le istruzioni sotto usando per creare una richiesta di supporto tramite di Azure "Utilizzo + quote' blade disponibile nel portale di Azure. 

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selezionare **richieste altri** nelle **tipo di quota** elenco a discesa.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Nelle **dettagli** riquadro forniscono informazioni aggiuntive come nell'esempio di seguito, allo scopo di processo di richiesta e continuare con la creazione di case. 
    1.  **Modello di distribuzione** – specificare "Resource Manager"
    2.  **Area richiesta** : specificare l'area necessaria, ad esempio Stati Uniti orientali 2
    3.  **Nuovo valore di limite** – specificare nuovi limiti per area. Questo non deve superare la somma della quota approvato per singoli famiglie di SKU per questa sottoscrizione

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



