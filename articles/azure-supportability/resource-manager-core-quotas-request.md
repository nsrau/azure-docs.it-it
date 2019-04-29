---
title: Richieste di aumento della quota di vCPU per Azure Resource Manager | Microsoft Docs
description: Richieste di aumento della quota di vCPU per Azure Resource Manager
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649293"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Richieste di aumento della quota di vCPU per Resource Manager

Le quote di vCPU per Resource Manager sono imposte a livello di area e a livello di famiglia di SKU.
Per altre informazioni su come vengono imposte le quote, vedere la pagina relativa ai [limiti del servizio e della sottoscrizione di Azure](https://aka.ms/quotalimits).
Per altre informazioni sulle famiglie di SKU e per confrontare costi e prestazioni, vedere la pagina [Prezzi di Macchine virtuali](https://aka.ms/pricingcompute).

Per richiedere un aumento, seguire le istruzioni seguenti per creare una richiesta di supporto tramite il blade "Utilizzo e quote" di Azure disponibile nel portale di Azure. 

## <a name="request-quota-increase-at-subscription-level"></a>Richiedi aumento di quota a livello di sottoscrizione

1. In https://portal.azure.com, selezionare **Sottoscrizioni**.

   ![Sottoscrizioni](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Selezionare la sottoscrizione](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

   ![Selezionare Utilizzo e quote](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Passaggio: 1 - Selezionare **Core** come tipo di quota. 

   ![Compilare i moduli](./media/resource-manager-core-quotas-request/forms.png)
   
6. Passaggio:2 - In Modello di distribuzione selezionare "Resource Manager" e quindi scegliere una località.

    ![Pannello Problema della quota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Selezionare le famiglie di SKU per cui si richiede l'aumento.

    ![Serie SKU selezionata](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Immettere i nuovi limiti da applicare alla sottoscrizione.

    ![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x".
Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su "Avanti" nella pagina del passaggio del problema per procedere con la creazione della richiesta di supporto.

