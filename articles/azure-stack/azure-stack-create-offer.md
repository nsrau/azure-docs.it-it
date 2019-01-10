---
title: Creare un'offerta in Azure Stack | Microsoft Docs
description: Un amministratore del cloud, informazioni su come creare un'offerta per gli utenti in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 079f45e37bef24ac974a0e2df7b1e81f1002cac0
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159078"
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider propongono agli utenti, che è possano acquistare o la sottoscrizione a tali utenti. Questo articolo descrive come creare un'offerta che include il [piano creato](azure-stack-create-plan.md). Questa offerta fornisce abbonati la possibilità di configurare le macchine virtuali (VM).

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external) e selezionare **+ crea una risorsa**, quindi **Tenant offerte e piani**e quindi **offrono**.

   ![Creare un'offerta](media/azure-stack-create-offer/image01.png)
  
2. Sotto **offrono nuove**, immettere un **nome visualizzato** e un **nome della risorsa**, quindi nella sezione **gruppo di risorse**, selezionare **crea nuove** oppure **Usa esistente**. Il nome visualizzato è il nome descrittivo per l'offerta. Questo nome descrittivo è l'unica informazione sull'offerta, gli utenti visualizzano quando hanno eseguito la sottoscrizione a un'offerta. Usare un nome intuitivo che consente agli utenti di comprendere ciò che viene fornito con l'offerta. Solo l'amministratore può visualizzare il nome della risorsa. È il nome usato dagli amministratori per lavorare con l'offerta come risorsa di Azure Resource Manager.

   ![Nuova offerta](media/azure-stack-create-offer/image01a.png)
  
3. Selezionare **piani di Base** per aprire il **pianificare**. Selezionare i piani a cui si desidera includere nell'offerta e quindi scegliere **seleziona**. Per creare l'istruzione select offerta **Create**.

   ![Seleziona piano](media/azure-stack-create-offer/image02.png)
  
4. Dopo aver creato l'offerta, è possibile modificare il relativo stato. Le offerte devono essere apportate **pubblica** agli utenti di ottenere una visione completa quando hanno eseguito la sottoscrizione. Possono essere offerte:

   - **Pubblica**: Visibile agli utenti.
   - **Privato**: Visibili solo agli amministratori di cloud. Questa impostazione è utile durante la progettazione del piano o offerta, o se l'amministratore cloud desidera [creare tutte le sottoscrizioni per gli utenti](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Rimuovere le autorizzazioni**: Chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile rimuovere le autorizzazioni offerte per evitare future sottoscrizioni, ma lasciare inalterate sottoscrittori correnti.

   > [!TIP]  
   > Modifiche all'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, gli utenti potrebbero essere necessario disconnettersi e accedere nuovamente al portale utenti per visualizzare la nuova offerta.

   Nella schermata di panoramica dell'offerta, selezionare **lo stato di accessibilità**. Scegliere lo stato da usare (ad esempio, **pubbliche**) e quindi selezionare **salvare**.

     ![Scegliere lo stato](media/azure-stack-create-offer/change-stage-1807.png)

     In alternativa, selezionare **modifica stato** e quindi scegliere uno stato.

    ![Selezionare lo stato di accesso facilitato](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > È anche possibile usare PowerShell per creare predefinite offerte, quote e piani. Per altre informazioni, vedere [modulo di PowerShell di Azure Stack 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Passaggi successivi

- [Creare sottoscrizioni](azure-stack-subscribe-plan-provision-vm.md)
- [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
