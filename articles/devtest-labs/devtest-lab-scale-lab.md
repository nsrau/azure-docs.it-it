---
title: Ridimensionare i limiti e le quote nel lab in Azure DevTest Labs | Microsoft Docs
description: Questo articolo descrive come è possibile ridimensionare il Lab in Azure DevTest Labs. Visualizzare le quote e i limiti di utilizzo e richiedere un aumento.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533955"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Ridimensionare i limiti e le quote in DevTest Labs
Quando si lavora in DevTest Labs, è possibile notare che esistono limiti predefiniti per determinate le risorse di Azure, che possono influenzare il servizio DevTest Labs. Questi limiti sono definiti **quote**.

> [!NOTE]
> Il servizio DevTest Labs non impone le quote. Le quote che gli utenti notano sono vincoli predefiniti di tutta la sottoscrizione di Azure.

È possibile usare ogni risorsa di Azure finché non si raggiunge la quota. Ogni sottoscrizione dispone di quote separate il cui uso viene controllato per ogni sottoscrizione.

Ad esempio, ogni sottoscrizione ha una quota predefinita di 20 core. Pertanto, se si siano creano macchine virtuali nel lab con quattro core, è possibile creare solo cinque macchine virtuali.

[Sottoscrizione di Azure e limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md) elenca alcune delle quote più comuni per le risorse di Azure. Le risorse usate più di frequente in un Lab e per le quali è possibile che si verifichino quote, includono Core VM, indirizzi IP pubblici, interfaccia di rete, Managed disks, assegnazione di ruolo di Azure e circuiti ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso
Questa procedura illustra come visualizzare le quote correnti nella sottoscrizione per risorse specifiche di Azure e per visualizzare la percentuale di ogni quota usata.

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **Fatturazione** dall'elenco.
1. Selezionare una sottoscrizione nel pannello Fatturazione.
4. Selezionare **utilizzo e quote**.

   ![Pulsante Utilizzo e quote](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   Viene visualizzato il pannello Utilizzo e quote che elenca le varie risorse disponibili nella sottoscrizione e la percentuale della quota usata per ogni risorsa.

   ![Utilizzo e quote](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Richiesta di altre risorse nella sottoscrizione
Se si raggiunge un limite di quota, il limite predefinito di una risorsa in una sottoscrizione può essere aumentato fino a un limite massimo, come descritto in [Sottoscrizione di Azure e limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

Questa procedura mostra come richiedere un aumento di quota usando il [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi**, quindi **Fatturazione** e **Utilizzo e quote**.
1. Nel pannello Utilizzo + quote selezionare il pulsante **Richiedi aumento**.

   ![Pulsante Richiedi aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Per completare e inviare la richiesta, inserire le informazioni necessarie sulle tre schede del modulo **Nuova richiesta di supporto**.

   ![Modulo Richiedi aumento](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) (Informazioni sui limiti e gli aumenti di Azure) contiene altre informazioni su come contattare il supporto di Azure per richiedere un aumento della quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passaggi successivi
* Esplorare la [raccolta di modelli di avvio rapido di DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
