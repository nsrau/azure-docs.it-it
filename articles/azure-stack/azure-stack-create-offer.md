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
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 59c69477e120facec1fbf132ce7017ca21aa8748
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092617"
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider propongono agli utenti, che è possano acquistare o la sottoscrizione a tali utenti. Questo articolo descrive come creare un'offerta che include il [piano creato](azure-stack-create-plan.md). Questa offerta fornisce abbonati la possibilità di configurare le macchine virtuali (VM).

## <a name="create-an-offer-1902-and-later"></a>Creare un'offerta (1902 e versioni successive)

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external) e selezionare **+ crea una risorsa**, quindi **offerte e piani**e quindi **offrono**.

   ![Creare un'offerta](media/azure-stack-create-offer/offers.png)

2. Viene visualizzata un'interfaccia utente a schede che consente di definire il nome dell'offerta e Aggiungi esistenti o creare nuovi piani di base e i piani aggiuntivi. In particolare, è possibile esaminare i dettagli dell'offerta, che si crea, prima di decidere di crearla.

   Nel **nozioni di base** nella scheda **offrono nuove**, immettere un **nome visualizzato** e un **nome risorsa**, quindi in **risorsa Gruppo**, selezionare **Crea nuovo** oppure **Usa esistente**. Il nome visualizzato è il nome descrittivo per l'offerta. Questo nome descrittivo è l'unica informazione sull'offerta, gli utenti visualizzano quando hanno eseguito la sottoscrizione a un'offerta nel portale per gli utenti. Usare un nome intuitivo che consente agli utenti di comprendere ciò che viene fornito con l'offerta. Solo l'amministratore può visualizzare il nome della risorsa. È il nome usato dagli amministratori per lavorare con l'offerta come risorsa di Azure Resource Manager. In questa scheda, è possibile anche scegliere di rendere pubblico l'offerta o mantenere privato, ovvero l'impostazione predefinita. È possibile [modificare lo stato pubblico o privato dell'offerta](#change-the-state-of-an-offer) in un secondo momento, nonché.

   ![Nuova offerta](media/azure-stack-create-offer/new-offer.png)
  
3. Selezionare il **piani di Base** scheda. Selezionare i piani da includere nell'offerta.

   ![Seleziona piano](media/azure-stack-create-offer/select-plan.png)

4. A questo punto è possibile creare un piano aggiuntivo per modificare il piano di base, ma questa operazione è facoltativa. Nel prossimo articolo, verrà creato un piano del componente aggiuntivo [piani aggiuntivi di Azure Stack](create-add-on-plan.md).

5. Selezionare il **revisione + Crea** scheda. Esaminare il riepilogo dell'offerta per assicurarsi che tutti i valori siano corretti. L'interfaccia consente di espandere le quote nei scelti piani, uno alla volta, per visualizzare i dettagli di ogni quota in un piano e tornare indietro per apportare le modifiche necessarie.

6. Selezionare **Create** per creare l'offerta.

   ![Rivedi e crea](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Modificare lo stato di un'offerta

Dopo aver creato l'offerta, è possibile modificare il relativo stato. Le offerte devono essere apportate **pubblica** agli utenti di ottenere una visione completa quando hanno eseguito la sottoscrizione. Possono essere offerte:

- **Public**: Visibile agli utenti.
- **Privato**: Visibili solo agli amministratori di cloud. Questa impostazione è utile durante la progettazione del piano o offerta, o se l'amministratore cloud desidera [creare tutte le sottoscrizioni per gli utenti](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Rimuovere le autorizzazioni**: Chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile rimuovere le autorizzazioni offerte per evitare future sottoscrizioni, ma lasciare inalterate sottoscrittori correnti.

  > [!TIP]  
  > Modifiche all'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, gli utenti potrebbero essere necessario disconnettersi e accedere nuovamente al portale utenti per visualizzare la nuova offerta.

Esistono due modi per modificare lo stato di un'offerta:

1. Nelle **tutte le risorse**, selezionare il nome dell'offerta. Nel **Overview** schermata per l'offerta, seleziona **modificare lo stato**. Scegliere lo stato da usare (ad esempio, **pubblica**).

   ![Scegliere lo stato](media/azure-stack-create-offer/change-state.png)

2. Nelle **tutte le risorse**, selezionare il nome dell'offerta. Quindi selezionare **impostazioni dell'offerta**. Scegliere lo stato da usare (ad esempio, **pubbliche**), quindi selezionare **salvare**.

   ![Selezionare lo stato di accesso facilitato](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Creare un'offerta (1901 e precedenti)

1. Accedi per il [portale di amministrazione di Azure Stack](https://adminportal.local.azurestack.external) e selezionare **+ crea una risorsa**, quindi **Tenant offerte e piani**e quindi **offrono**.

   ![Creare un'offerta](media/azure-stack-create-offer/image01.png)
  
2. Sotto **offrono nuove**, immettere un **nome visualizzato** e un **nome della risorsa**, quindi nella sezione **gruppo di risorse**, selezionare **crea nuove** oppure **Usa esistente**. Il nome visualizzato è il nome descrittivo per l'offerta. Questo nome descrittivo è l'unica informazione sull'offerta, gli utenti visualizzano quando hanno eseguito la sottoscrizione a un'offerta. Usare un nome intuitivo che consente agli utenti di comprendere ciò che viene fornito con l'offerta. Solo l'amministratore può visualizzare il nome della risorsa. È il nome usato dagli amministratori per lavorare con l'offerta come risorsa di Azure Resource Manager.

   ![Nuova offerta](media/azure-stack-create-offer/image01a.png)
  
3. Selezionare **piani di Base** per aprire il **pianificare**. Selezionare i piani a cui si desidera includere nell'offerta e quindi scegliere **seleziona**. Per creare l'istruzione select offerta **Create**.

   ![Seleziona piano](media/azure-stack-create-offer/image02.png)
  
4. Dopo aver creato l'offerta, è possibile modificare il relativo stato. Le offerte devono essere apportate **pubblica** agli utenti di ottenere una visione completa quando hanno eseguito la sottoscrizione. Possono essere offerte:

   - **Public**: Visibile agli utenti.
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

- [Create subscriptions](azure-stack-subscribe-plan-provision-vm.md) (Crea sottoscrizioni)
- [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
