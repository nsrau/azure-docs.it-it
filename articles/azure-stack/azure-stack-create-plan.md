---
title: Creare un piano in Azure Stack | Microsoft Docs
description: Un amministratore del cloud, creare un piano che consenta i sottoscrittori provisioning di macchine virtuali.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: ec8fabfce8c26aef98217947da33a56222288b3c
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077563"
---
# <a name="create-a-plan-in-azure-stack"></a>Creare un piano in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

I [piani](azure-stack-key-features.md) sono raggruppamenti di uno o più servizi. Come provider, è possibile creare i piani da offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte per usare i piani e servizi che inclusi. In questo esempio illustra come creare un piano che include il calcolo, rete e i provider di risorse di archiviazione. Questo piano offre abbonati la possibilità di effettuare il provisioning di macchine virtuali.

1. Accedere al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external).

2. Per creare un piano e offerta che gli utenti possono sottoscrivere, selezionare **+ crea una risorsa** > **offre + piani** > **piano**.
  
   ![Selezionare un piano](media/azure-stack-create-plan/select-plan.png)

3. Sotto **nuovo piano**, immettere una **nome visualizzato** e un **nome risorsa**. Il nome visualizzato è nome descrittivo del piano che gli utenti possono visualizzare. Solo l'amministratore può visualizzare il nome della risorsa, quali gli amministratori usano per lavorare con il piano come una risorsa di Azure Resource Manager.

   ![Specificare i dettagli](media/azure-stack-create-plan/plan-name.png)

4. Creare una nuova **gruppo di risorse**, o selezionarne uno esistente, come un contenitore per il piano.

   ![Specificare il gruppo di risorse](media/azure-stack-create-plan/resource-group.png)

5. Selezionare **Services** e quindi selezionare la casella di controllo **Microsoft. COMPUTE**, **Microsoft. Network**, e **Microsoft. Storage**. Scegliere quindi **seleziona** per salvare la configurazione. Le caselle di controllo vengono visualizzate quando il puntatore del mouse viene posizionato su ogni opzione.
  
   ![Seleziona servizi](media/azure-stack-create-plan/services.png)

6. Selezionare **quote**, **(locale) di Microsoft. Storage**e quindi scegliere la quota predefinita o select **Crea nuova quota** per creare una quota personalizzata.
  
   ![Quote](media/azure-stack-create-plan/quotas.png)

7. Se si crea una nuova quota, immettere un **Name** per la quota > specificare i valori di quota > selezionare **OK**. Il **Crea quota** finestra di dialogo viene chiusa.

   ![Nuova quota](media/azure-stack-create-plan/new-quota.png)

   È quindi possibile selezionare la nuova quota che è stato creato. Selezionando la quota viene assegnato e chiude la finestra di dialogo di selezione.
  
   ![Assegnare la quota](media/azure-stack-create-plan/assign-quota.png)

8. Ripetere i passaggi 6 e 7 per creare e assegnare le quote per **(locale) di Microsoft. Network** e **(locale) di Microsoft. COMPUTE**. Quando tutti e tre i servizi sono state assegnate quote, si sarà simile all'esempio successivo.

   ![Assegnazioni di quota completo](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Sotto **quote**, scegliere **OK**, quindi in **nuovo piano**, scegliere **Create** per creare il piano.

    ![Creare il piano](media/azure-stack-create-plan/create.png)

10. Per visualizzare il nuovo piano, selezionare **tutte le risorse**, quindi cercare il piano e selezionare il relativo nome. Se l'elenco delle risorse è lungo, usare **ricerca** per individuare il piano in base al nome.

   ![Rivedere il piano](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Passaggi successivi

[Creare un'offerta](azure-stack-create-offer.md)
