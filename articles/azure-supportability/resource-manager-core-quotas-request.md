---
title: Richieste di aumento della quota di core per Azure Resource Manager | Documentazione Microsoft
description: Richieste di aumento della quota di core per Azure Resource Manager
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.translationtype: Human Translation
ms.sourcegitcommit: 2373cf227cbed4f6657e31088b11dca5223ce5fe
ms.openlocfilehash: cb6c5b3e86f126d4110d1cd29d8c9891e356e414
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---

# <a name="resource-manager-core-quota-increase-requests"></a>Richieste di aumento della quota di core per Resource Manager

Le quote di core per Resource Manager sono imposte a livello di area e a livello di famiglia di SKU.
Per altre informazioni su come vengono imposte le quote, vedere la pagina relativa ai [limiti del servizio e della sottoscrizione di Azure](http://aka.ms/quotalimits).
Per altre informazioni sulle famiglie di SKU e per confrontare costi e prestazioni, vedere la pagina [Prezzi di Macchine virtuali](http://aka.ms/pricingcompute).

Per richiedere un aumento, creare una richiesta di supporto per la quota dei core nel portale di Azure, all'indirizzo [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Informazioni su come [creare una richiesta di supporto](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nel portale di Azure

1. Nella pagina della nuova richiesta di supporto selezionare "Quota" in Tipo di problema e quindi scegliere "Core" in Tipo di quota.

    ![Pannello Informazioni di base della quota](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. In Modello di distribuzione selezionare "Resource Manager" e quindi scegliere una località.

    ![Pannello Problema della quota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Selezionare le famiglie di SKU per cui si richiede l'aumento.

    ![Serie SKU selezionata](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Immettere i nuovi limiti da applicare alla sottoscrizione.

    ![Richiesta di nuova quota per lo SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Per rimuovere una riga, deselezionare lo SKU dall'elenco a discesa delle famiglie di SKU oppure fare clic sull'icona di eliminazione "x".
Dopo aver immesso la quota desiderata per ogni famiglia di SKU, fare clic su "Avanti" nella pagina del passaggio del problema per procedere con la creazione della richiesta di supporto.

