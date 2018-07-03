---
title: Creare un'offerta in Azure Stack | Microsoft Docs
description: Un amministratore del cloud, informazioni su come creare un'offerta per gli utenti in Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/2/2018
ms.author: brenduns
ms.openlocfilehash: eed715a7c2cb967f6c9ea0b7d4442a4f9976bd17
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345890"
---
# <a name="create-an-offer-in-azure-stack"></a>Creare un'offerta in Azure Stack

[Offre](azure-stack-key-features.md) sono gruppi di uno o più piani che i provider propongono agli utenti di acquistare o alla sottoscrizione. Questo documento illustra come creare un'offerta che include il [piano creato](azure-stack-create-plan.md). Questa offerta fornisce abbonati la possibilità di configurare le macchine virtuali.

1. Accedere al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external) e selezionare **New** > **Tenant offerte e piani** > **offrono**.

   ![Creare un'offerta](media/azure-stack-create-offer/image01.png)
  
2. Sotto **offrono nuove**, immettere un **nome visualizzato** e un **nome della risorsa**, quindi nella sezione **gruppo di risorse**, selezionare **crea nuove** oppure **Usa esistente**. Il nome visualizzato è il nome descrittivo per l'offerta. Questo nome descrittivo è l'unica informazione sull'offerta, visibile agli utenti quando hanno eseguito la sottoscrizione a un'offerta. Usare un nome intuitivo che consente agli utenti di comprendere ciò che viene fornito con l'offerta. e può essere visualizzato solo dall'amministratore. È il nome usato dagli amministratori per lavorare con l'offerta come risorsa di Azure Resource Manager.

   ![Nuova offerta](media/azure-stack-create-offer/image01a.png)
  
3. Selezionare **piani di Base** per aprire il **pianificare**. Selezionare i piani a cui si desidera includere nell'offerta e quindi scegliere **seleziona**. Per creare l'istruzione select offerta **Create**.

   ![Seleziona piano](media/azure-stack-create-offer/image02.png)
  
4. Dopo aver creato l'offerta, è possibile modificare il relativo stato. Le offerte devono essere apportate *pubblica* agli utenti di ottenere una visione completa quando hanno eseguito la sottoscrizione. Possono essere offerte:

   - **Pubblica**: visibile agli utenti.
   - **Privato**: visibili solo agli amministratori di cloud. Questa impostazione è utile durante la progettazione del piano o offerta, o se l'amministratore cloud desidera [creare tutte le sottoscrizioni per gli utenti](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Rimossi**: chiusi ai nuovi sottoscrittori. L'amministratore del cloud è possibile usare autorizzazioni rimosse per evitare future sottoscrizioni, ma lasciare inalterate sottoscrittori correnti.

   > [!TIP]  
   > Le modifiche all'offerta non sono immediatamente visibili all'utente. Per visualizzare le modifiche, gli utenti potrebbero essere necessario disconnettersi e accedere nuovamente al portale utenti per visualizzare la nuova offerta.

   Per modificare lo stato dell'offerta:

   - **Versione 1803 e versioni successive**:  
     Nella panoramica dell'offerta, selezionare **lo stato di accessibilità**. Scegliere lo stato da usare (ad esempio, *pubbliche*) e quindi selezionare **salvare**.
 
     ![Selezionare lo stato di accesso facilitato](media/azure-stack-create-offer/change-state.png)

     In alternativa, dopo l'accesso a un'offerta è possibile passare a **impostazioni dell'offerta**. Selezionare **lo stato di accessibilità** per modificare lo stato.

   - **Prima della versione 1803**:  
     Selezionare **tutte le risorse**, cercare la nuova offerta e quindi selezionare la nuova offerta. Selezionare **modifica stato**, quindi selezionare **pubblico**.

   > [!NOTE]
   > È anche possibile usare PowerShell per creare predefinite offerte, quote e piani. Per altre informazioni, vedere [modulo di Azure Stack PowerShell 1.3.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0).

## <a name="next-steps"></a>Passaggi successivi

- [Creare sottoscrizioni](azure-stack-subscribe-plan-provision-vm.md)
- [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
