---
title: Ridimensionare i limiti e le quote nel lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come ridimensionare un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Ridimensionare i limiti e le quote in DevTest Labs
Quando si lavora in DevTest Labs, è possibile notare che esistono limiti predefiniti per determinate le risorse di Azure, che possono influenzare il servizio DevTest Labs. Questi limiti sono definiti **quote**.

> [!NOTE]
> Il servizio DevTest Labs non impone le quote. Le quote che gli utenti notano sono vincoli predefiniti di tutta la sottoscrizione di Azure.

È possibile usare ogni risorsa di Azure finché non si raggiunge la quota. Ogni sottoscrizione dispone di quote separate il cui uso viene controllato per ogni sottoscrizione.

Ad esempio, ogni sottoscrizione ha una quota predefinita di 20 core. Pertanto, se si siano creano macchine virtuali nel lab con quattro core, è possibile creare solo cinque macchine virtuali. 

[Sottoscrizione di Azure e limiti dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits) elenca alcune delle quote più comuni per le risorse di Azure. Le risorse più comunemente usate in un lab e per cui è possibile che ci siano quote, includono i core di una macchina virtuale, gli indirizzi IP pubblici, l'interfaccia di rete, i dischi gestiti, l'assegnazione di ruoli controllo degli accessi in base al ruolo e i circuiti ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso
Questa procedura illustra come visualizzare le quote correnti nella sottoscrizione per risorse specifiche di Azure e per visualizzare la percentuale di ogni quota usata.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **Fatturazione** dall'elenco.
1. Selezionare una sottoscrizione nel pannello Fatturazione.
4. Selezionare **Utilizzo e quote**.

   ![Pulsante Utilizzo e quote](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Viene visualizzato il pannello Utilizzo e quote che elenca le varie risorse disponibili nella sottoscrizione e la percentuale della quota usata per ogni risorsa.

   ![Utilizzo e quote](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Richiesta di altre risorse nella sottoscrizione
Se si raggiunge un limite di quota, il limite predefinito di una risorsa in una sottoscrizione può essere aumentato fino a un limite massimo, come descritto in [Sottoscrizione di Azure e limiti dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Questa procedura mostra come richiedere un aumento di quota usando il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi**, quindi **Fatturazione** e **Utilizzo e quote**.
1. Nel pannello Utilizzo + quote selezionare il pulsante **Richiedi aumento**.

   ![Pulsante Richiedi aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Per completare e inviare la richiesta, inserire le informazioni necessarie sulle tre schede del modulo **Nuova richiesta di supporto**.

   ![Modulo Richiedi aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) (Informazioni sui limiti e gli aumenti di Azure) contiene altre informazioni su come contattare il supporto di Azure per richiedere un aumento della quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passaggi successivi
* Esplorare la [raccolta dei modelli di avvio rapido di Azure Resource Manager di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
