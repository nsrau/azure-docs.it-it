---
title: Creare un piano nello Stack di Azure | Documenti Microsoft
description: Come un amministratore del cloud, creare un piano che consente alle macchine virtuali di provisioning ai sottoscrittori.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Creare un piano in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

I [piani](azure-stack-key-features.md) sono raggruppamenti di uno o più servizi. Come un provider, è possibile creare piani per offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte di utilizzare i piani e i servizi che includono. In questo esempio viene illustrato come creare un piano che include il calcolo, rete e i provider di risorse di archiviazione. Questo piano offre i sottoscrittori la possibilità di eseguire il provisioning di macchine virtuali.

1. Accedere al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external). Immettere le credenziali per l'account creato durante il passaggio 5 del [eseguire lo script PowerShell](azure-stack-run-powershell-script.md) sezione.

2. Per creare un piano e offerta che gli utenti possano sottoscrivere, fare clic su **New** > **Tenant offre + piani** > **piano**.

   ![](media/azure-stack-create-plan/image01.png)
3. Nel **nuovo piano** pannello, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. e può essere visualizzato solo dall'amministratore. Si tratta del nome usato dagli amministratori per poter utilizzare il piano come una risorsa di Gestione risorse di Azure.

   ![](media/azure-stack-create-plan/image02.png)
4. Creare un nuovo **gruppo di risorse**, o selezionarne uno esistente, come un contenitore per il piano.

   ![](media/azure-stack-create-plan/image02a.png)
5. Fare clic su **servizi**selezionare **Microsoft. COMPUTE**, **Network**, e **Microsoft.Storage**, quindi fare clic su **Selezionare**.

   ![](media/azure-stack-create-plan/image03.png)
6. Fare clic su **quote**, fare clic su **Microsoft.Storage (locale)**, quindi selezionare la quota predefinita o fare clic su **Crea nuova quota** per personalizzare la quota.

   ![](media/azure-stack-create-plan/image04.png)
7. Se si sta creando una nuova quota, immettere un nome per la quota di > impostare i valori di quota > fare clic su **OK** > fare clic sul nome della nuova quota.

   ![](media/azure-stack-create-plan/image06.png)
8. Fare clic su **Network (locale)**, quindi selezionare la quota predefinita o fare clic su **Crea nuova quota** per personalizzare la quota.

    ![](media/azure-stack-create-plan/image07.png)
9. Se si sta creando una nuova quota, digitare un nome per la quota di > impostare i valori di quota > fare clic su **OK** > fare clic sul nome della nuova quota.

    ![](media/azure-stack-create-plan/image08.png)
10. Fare clic su **(locale) di Microsoft. COMPUTE**, quindi selezionare la quota predefinita o fare clic su **Crea nuova quota** per personalizzare la quota.

    ![](media/azure-stack-create-plan/image09.png)
11. Se si sta creando una nuova quota, digitare un nome per la quota di > impostare i valori di quota > fare clic su **OK** > fare clic sul nome della nuova quota.

    ![](media/azure-stack-create-plan/image10.png)
12. Nel **quote** pannello, fare clic su **OK**e quindi il **nuovo piano di** pannello, fare clic su **crea** per creare il piano.

    ![](media/azure-stack-create-plan/image11.png)
13. Per visualizzare il nuovo piano, fare clic su **tutte le risorse**, quindi cercare il piano e fare clic sul relativo nome.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Passaggi successivi
[Creare un'offerta](azure-stack-create-offer.md)
