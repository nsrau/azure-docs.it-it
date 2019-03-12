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
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759470"
---
# <a name="create-a-plan-in-azure-stack"></a>Creare un piano in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

[Piani](azure-stack-key-features.md) sono raggruppamenti di uno o più servizi e le rispettive quote. Come provider, è possibile creare i piani da offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte per usare i piani, servizi e le quote che includono. In questo esempio illustra come creare un piano che include il calcolo, rete e i provider di risorse di archiviazione. Questo piano offre abbonati la possibilità di effettuare il provisioning di macchine virtuali.

## <a name="create-a-plan-1902-and-later"></a>Creare un piano (1902 e versioni successive)

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external).

2. Per creare un piano e offerta che gli utenti possono sottoscrivere, selezionare **+ crea una risorsa**, quindi **offre + piani**, quindi **piano**.
  
   ![Selezionare un piano](media/azure-stack-create-plan/select-plan.png)

3. Viene visualizzata un'interfaccia utente a schede che consente di specificare il nome del piano, aggiungere servizi e definire le quote per ogni servizio selezionato. In particolare, è possibile esaminare i dettagli dell'offerta, che si crea, prima di decidere di crearla.

   Sotto il **nozioni di base** scheda della finestra di **nuovo piano di** finestra, immettere un **nome visualizzato** e un **nome della risorsa**. Il nome visualizzato è nome descrittivo del piano che possono visualizzare gli operatori. Si noti che nel portale di amministrazione, i dettagli del piano sono visibili solo agli operatori.

   ![Specificare i dettagli](media/azure-stack-create-plan/plan-name.png)

4. Creare una nuova **gruppo di risorse**, o selezionarne uno esistente, come un contenitore per il piano.

   ![Specificare il gruppo di risorse](media/azure-stack-create-plan/resource-group.png)

5. Selezionare il **Services** scheda e quindi selezionare la casella di controllo **Microsoft. COMPUTE**, **Microsoft. Network**, e **Microsoft. Storage** .
  
   ![Selezionare i servizi](media/azure-stack-create-plan/services.png)

6. Selezionare il **quote** scheda. Accanto a **Microsoft. Storage**, scegliere la quota predefinita di entrambi dalla casella a discesa oppure selezionare **Crea nuovo** per creare una quota personalizzata.
  
   ![Quote](media/azure-stack-create-plan/quotas.png)

7. Se si crea una nuova quota, immettere un **nome** per la quota e quindi specificare i valori di quota. Selezionare **OK** per creare la quota.

   ![Nuova quota](media/azure-stack-create-plan/new-quota.png)

8. Ripetere i passaggi 6 e 7 per creare e assegnare le quote per **Microsoft. Network** e **Microsoft. COMPUTE**. Quando tutti e tre i servizi sono state assegnate quote, si sarà simile all'esempio successivo.

   ![Assegnazioni di quota completo](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Selezionare **esaminare e creare** per esaminare il piano. Esaminare tutti i valori e le quote per assicurarsi che siano corretti. Tenere presente le frecce di espansione a sinistra di ogni coppia/quote del servizio. Una nuova funzionalità consente di espandere le quote in piani selezionati, uno alla volta, per visualizzare i dettagli di ogni quota in un piano e tornare indietro per apportare le modifiche necessarie.

   ![Creare il piano](media/azure-stack-create-plan/create.png)

10. Quando si è pronti, selezionare **Create** per creare il piano.

11. Per visualizzare il nuovo piano, selezionare **piani**, quindi cercare il piano e selezionare il relativo nome. Se l'elenco delle risorse è lungo, usare **ricerca** per individuare il piano in base al nome.

## <a name="create-a-plan-1901-and-earlier"></a>Creare un piano (1901 e precedenti)

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external).

2. Per creare un piano e offerta che gli utenti possono sottoscrivere, selezionare **+ crea una risorsa**, quindi **offre + piani**, quindi **piano**.
  
   ![Selezionare un piano](media/azure-stack-create-plan/select-plan1901.png)

3. Sotto **nuovo piano**, immettere una **nome visualizzato** e un **nome risorsa**. Il nome visualizzato è nome descrittivo del piano che gli utenti possono visualizzare. Solo l'amministratore può visualizzare il nome della risorsa, quali gli amministratori usano per lavorare con il piano come una risorsa di Azure Resource Manager.

   ![Specificare i dettagli](media/azure-stack-create-plan/plan-name1901.png)

4. Creare una nuova **gruppo di risorse**, o selezionarne uno esistente, come un contenitore per il piano.

   ![Specificare il gruppo di risorse](media/azure-stack-create-plan/resource-group1901.png)

5. Selezionare **Services** e quindi selezionare la casella di controllo **Microsoft. COMPUTE**, **Microsoft. Network**, e **Microsoft. Storage**. Scegliere quindi **seleziona** per salvare la configurazione. Le caselle di controllo vengono visualizzate quando il puntatore del mouse viene posizionato su ogni opzione.
  
   ![Selezionare i servizi](media/azure-stack-create-plan/services1901.png)

6. Selezionare **quote**, **(locale) di Microsoft. Storage**e quindi scegliere la quota predefinita o select **Crea nuova quota** per creare una quota personalizzata.
  
   ![Quote](media/azure-stack-create-plan/quotas1901.png)

7. Se si crea una nuova quota, immettere un **Name** per la quota > specificare i valori di quota > selezionare **OK**. Il **Crea quota** finestra di dialogo viene chiusa.

   ![Nuova quota](media/azure-stack-create-plan/new-quota1901.png)

   È quindi possibile selezionare la nuova quota che è stato creato. Selezionando la quota viene assegnato e chiude la finestra di dialogo di selezione.
  
   ![Assegnare la quota](media/azure-stack-create-plan/assign-quota1901.png)

8. Ripetere i passaggi 6 e 7 per creare e assegnare le quote per **(locale) di Microsoft. Network** e **(locale) di Microsoft. COMPUTE**. Quando tutti e tre i servizi sono state assegnate quote, si sarà simile all'esempio successivo.

   ![Assegnazioni di quota completo](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Sotto **quote**, scegliere **OK**, quindi in **nuovo piano**, scegliere **Create** per creare il piano.

    ![Creare il piano](media/azure-stack-create-plan/create1901.png)

10. Per visualizzare il nuovo piano, selezionare **tutte le risorse**, quindi cercare il piano e selezionare il relativo nome. Se l'elenco delle risorse è lungo, usare **ricerca** per individuare il piano in base al nome.

    ![Rivedere il piano](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'offerta](azure-stack-create-offer.md)
