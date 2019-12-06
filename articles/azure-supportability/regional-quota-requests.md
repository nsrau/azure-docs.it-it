---
title: Richieste di aumento della quota a livello di area di Azure | Microsoft Docs
description: Richieste di aumento della quota a livello di area
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 132cf6ccfec5af9951f5dc6d6a3c6d3c81363d81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850007"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Quota standard: aumento del limite di vCPU a livello di area 

Gestione risorse supporta due tipi di quote vCPU per le macchine virtuali. Le **macchine virtuali con pagamento in base al** consumo e le **istanze di VM riservate** usano la quota standard. Le **VM spot** usano la quota spot. 

La quota vCPU standard per le istanze con pagamento in base al consumo e VM riservate viene applicata a due livelli per ogni sottoscrizione in ogni area.
 
Il primo livello è il **limite di vCPU regionale totale** (in tutte le serie di VM) e il secondo livello è il **limite di vCPU per serie di VM** , ad esempio la serie D vCPU. Ogni volta che viene distribuita una nuova VM, la somma dell'utilizzo di vCPU nuovo ed esistente per tale serie di macchine virtuali non deve superare la quota di vCPU approvata per la serie di VM specifica. Inoltre, il numero totale di vCPU nuovo ed esistente distribuito in tutte le serie di macchine virtuali non deve superare la quota vCPU regionale totale approvata per la sottoscrizione. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile. È possibile richiedere un aumento del limite di quota vCPU per la serie di macchine virtuali da portale di Azure. Un aumento della quota della serie di VM aumenta automaticamente il limite di vCPU regionale totale per la stessa quantità.

Quando viene creata una nuova sottoscrizione, la vCPU locale totale predefinita potrebbe non essere uguale alla somma delle quote predefinite di vCPU per tutte le singole serie di macchine virtuali. Ciò può comportare una sottoscrizione con una quota sufficiente per ogni singola serie di VM che si vuole distribuire, ma non una quota sufficiente per la vCPU regionale totale per tutte le distribuzioni. In questo caso, sarà necessario inviare una richiesta per aumentare in modo esplicito il limite di vCPU regionale totale. Il limite di vCPU regionale totale non può superare la somma della quota approvata in tutte le serie di VM per l'area.

Per altre informazioni sulle quote vCPU standard, vedere la pagina delle [quote vCPU della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e la pagina relativa ai [limiti di sottoscrizione e servizio di Azure](https://aka.ms/quotalimits) .

Altre informazioni su come **aumentare i limiti di vCPU della VM spot** [qui](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

È possibile richiedere un aumento del **limite di vCPU regionale totale delle VM standard** tramite il pannello **Guida e supporto** oppure il pannello **utilizzi e quota** nel portale.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Richiedere l'aumento del limite di vCPU Regional per la quota standard a livello di sottoscrizione usando il pannello Guida e supporto

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "Guida e supporto" di Azure disponibile nel portale di Azure. 

1. In https://portal.azure.com selezionare **Guida e supporto**.

![Guida e supporto](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selezionare **Nuova richiesta di supporto**. 

![Nuova richiesta di supporto](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Nell'elenco a discesa tipo di problema scegliere **limiti per servizio e sottoscrizione (quote)** .

![Elenco a discesa tipo di problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Seleziona newS Subscription](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selezionare **altre richieste** nell'elenco a discesa **tipo di quota** .

![Tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Nel riquadro dei **Dettagli** fornire informazioni aggiuntive in base all'esempio riportato di seguito, per elaborare la richiesta e continuare con la creazione del case. 
    1.  **Modello di distribuzione** : specificare ' Gestione risorse '
    2.  **Area richiesta** : specificare l'area richiesta, ad esempio Stati Uniti orientali 2
    3.  **Nuovo valore limite** : specificare il limite per la nuova area. Questo non deve superare la somma della quota approvata per le famiglie di SKU individuali per questa sottoscrizione

![Dettagli quota](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Richiedi un aumento della quota di vCPU regionale totale a livello di sottoscrizione usando il pannello **utilizzi e quote**

Seguire le istruzioni riportate di seguito per creare una richiesta di supporto tramite il pannello "utilizzo e quota" di Azure disponibile nel portale di Azure. 

1. In https://portal.azure.com , selezionare **Sottoscrizioni**.

![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selezionare **altre richieste** nell'elenco a discesa **tipo di quota** .

![Tipo di quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Nel riquadro dei **Dettagli** fornire informazioni aggiuntive in base all'esempio riportato di seguito, per elaborare la richiesta e continuare con la creazione del case. 
    1.  **Modello di distribuzione** : specificare ' Gestione risorse '
    2.  **Area richiesta** : specificare l'area richiesta, ad esempio Stati Uniti orientali 2
    3.  **Nuovo valore limite** : specificare il limite per la nuova area. Questo non deve superare la somma della quota approvata per le famiglie di SKU individuali per questa sottoscrizione

![Dettagli quota](./media/resource-manager-core-quotas-request/regional-details.png)



