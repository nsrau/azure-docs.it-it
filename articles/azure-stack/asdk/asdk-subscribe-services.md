---
title: "In questa esercitazione, è illustrato come sottoscrivere un'offerta di Azure Stack | Documenti Microsoft"
description: Questa esercitazione viene illustrato come creare una nuova sottoscrizione ai servizi di Azure Stack e l'offerta di test tramite la creazione di una macchina virtuale di test.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>Esercitazione: creare e testare una sottoscrizione
Questa esercitazione viene illustrato come creare una sottoscrizione che contiene un'offerta e quindi eseguirne il test. Per il test, si accederà ai [portale per gli utenti](https://portal.local.azurestack.external) come un amministratore del cloud, sottoscrivere l'offerta e quindi creare una macchina virtuale.

> [!TIP]
> Per più un'esperienza valutazione più avanzata, è possibile [creare una sottoscrizione per un determinato utente](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) e quindi accedere come tale utente nel portale per gli utenti. 

Questa esercitazione illustra come sottoscrivere l'offerta creato nel [esercitazione precedente](asdk-offer-services.md).

Lezioni dell'esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

## <a name="subscribe-to-an-offer"></a>Sottoscrivere un'offerta
Per sottoscrivere un'offerta con un account utente, è necessario eseguire l'accesso al portale per gli utenti dello Stack di Azure per individuare i servizi che sono stati offerti dall'operatore dello Stack di Azure.

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external) e fare clic su **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/asdk-subscribe-services/get-subscription.png)

2. Nel campo **Nome visualizzato** digitare un nome per la sottoscrizione. Quindi, fare clic su **offrono** per selezionare una delle offerte disponibili nel **scegliere un'offerta** sezione e quindi fare clic su **crea**.

   ![Creare un'offerta](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > Ora è necessario aggiornare il portale per gli utenti per iniziare a usare la sottoscrizione.

3. Per visualizzare la sottoscrizione appena creata, fare clic su **altri servizi**, fare clic su **sottoscrizioni**, quindi fare clic su nella nuova sottoscrizione. Dopo la sottoscrizione a un'offerta, aggiornare il portale per vedere se i nuovi servizi sono stati inclusi come parte della nuova sottoscrizione. In questo esempio, **macchine virtuali** è stato aggiunto.

   ![Vista sottoscrizione](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testare l'offerta
Mentre si è connessi ai [portale per gli utenti](https://portal.local.azurestack.external), è possibile testare l'offerta effettuando il provisioning di una macchina virtuale utilizzando le nuove funzionalità di sottoscrizione. 

> [!NOTE]
> Questo test richiede l'immagine di macchina virtuale di Windows Server 2016 Data Center che è stato aggiunto al marketplace dello Stack di Azure in un [esercitazione precedente](asdk-marketplace-item.md). 

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external).

2. Nel portale per gli utenti, fare clic su **macchine virtuali** > **Add** > **Data Center di Windows Server 2016**, quindi fare clic su **crea** .

3. Nel **nozioni di base** , immettere un **nome**, **nome utente**, e **Password**, scegliere un **sottoscrizione**, creare un **gruppo di risorse** o selezionarne uno esistente, quindi fare clic su **OK**.

4. Nel **scegliere una dimensione** fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  

5. Nel pannello delle impostazioni, accettare le impostazioni predefinite e fare clic su **OK**.

6. Nel **riepilogo** fare clic su **OK** per creare la macchina virtuale.  

7. Per visualizzare la nuova macchina virtuale, fare clic su **macchine virtuali**, quindi cercare la nuova macchina virtuale e fare clic sul relativo nome.

    ![Tutte le risorse](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> Distribuzione della macchina virtuale richiederà alcuni minuti.


## <a name="next-steps"></a>Passaggi successivi

Le informazioni acquisite in questa esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

Passare alla prossima esercitazione per imparare a creare una macchina virtuale utilizzando un modello.

> [!div class="nextstepaction"]
> [Creare una macchina virtuale da un modello](asdk-create-vm-template.md)