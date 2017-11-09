---
title: Sottoscrivere un'offerta | Documenti Microsoft
description: Un utente di imparare a sottoscrivere un'offerta.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.openlocfilehash: f70815b5e89753a4b0083ffbe10d9920062d1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="subscribe-to-an-offer"></a>Sottoscrivere un'offerta

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Adesso che è stato [creato un'offerta](azure-stack-create-offer.md), verificare che gli utenti possono creare una sottoscrizione.

1. [Accedi](azure-stack-connect-azure-stack.md) il portale utenti Azure Stack (https://portal.local.azurestack.external) e fare clic su **ottenere una sottoscrizione**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. Nel **nome visualizzato** campo, digitare un nome per la sottoscrizione, fare clic su **offrono**, fare clic su una delle offerte nel **scegliere un'offerta** blade e quindi fare clic su  **Creare**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Per visualizzare la sottoscrizione è stato creato, fare clic su **più servizi**, fare clic su **sottoscrizioni**, quindi fare clic sulla nuova sottoscrizione.  

Dopo la sottoscrizione a un'offerta, aggiornare il portale di servizi che fanno parte della nuova sottoscrizione.

## <a name="subscribe-to-an-add-on-plan"></a>Sottoscrivere un piano del componente aggiuntivo
Se l'offerta include un piano del componente aggiuntivo, gli utenti possono aggiungerle alla propria sottoscrizione in qualsiasi momento.  

1. Nel portale per gli utenti, selezionare **più servizi** > **sottoscrizioni**.

2. Fare clic sulla sottoscrizione > **Aggiungi piano** e selezionare il piano del componente aggiuntivo.



## <a name="next-steps"></a>Passaggi successivi
[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
