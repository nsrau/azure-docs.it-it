---
title: Richieste di aumento della quota a livello di area di Azure | Microsoft Docs
description: Richieste di aumento della quota a livello di area
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083080"
---
# <a name="total-regional-vcpu-limit-increase"></a>Aumento del limite vCPU regionale totale 

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



