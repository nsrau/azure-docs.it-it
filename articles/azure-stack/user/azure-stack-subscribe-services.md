---
title: In questa esercitazione, è illustrato come sottoscrivere un'offerta di Azure Stack | Documenti Microsoft
description: Questa esercitazione viene illustrato come creare una nuova sottoscrizione ai servizi di Azure Stack e l'offerta di test tramite la creazione di una macchina virtuale di test.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238420"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Esercitazione: creare e testare una sottoscrizione
Questa esercitazione viene illustrato come creare una sottoscrizione che contiene un'offerta e quindi eseguirne il test. Per il test, si verrà accedere al portale per gli utenti dello Stack di Azure come un amministratore del cloud, sottoscrivere l'offerta e quindi creare una macchina virtuale.

> [!TIP]
> Per più un'esperienza valutazione più avanzata, è possibile [creare una sottoscrizione per un determinato utente](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) e quindi accedere come tale utente nel portale per gli utenti. 

Questa esercitazione viene illustrato come sottoscrivere un'offerta di Azure Stack.

Lezioni dell'esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

## <a name="subscribe-to-an-offer"></a>Sottoscrivere un'offerta
Per sottoscrivere un'offerta con un account utente, è necessario eseguire l'accesso al portale per gli utenti dello Stack di Azure per individuare i servizi che sono stati offerti dall'operatore dello Stack di Azure.

1. Accedere al portale per gli utenti e fare clic su **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-services/get-subscription.png)

2. Nel campo **Nome visualizzato** digitare un nome per la sottoscrizione. Quindi, fare clic su **offrono** per selezionare una delle offerte disponibili nel **scegliere un'offerta** sezione e quindi fare clic su **crea**.

   ![Creare un'offerta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Ora è necessario aggiornare il portale per gli utenti per iniziare a usare la sottoscrizione.

3. Per visualizzare la sottoscrizione appena creata, fare clic su **altri servizi**, fare clic su **sottoscrizioni**, quindi fare clic su nella nuova sottoscrizione. Dopo la sottoscrizione a un'offerta, aggiornare il portale per vedere se i nuovi servizi sono stati inclusi come parte della nuova sottoscrizione. In questo esempio, **macchine virtuali** è stato aggiunto.

   ![Vista sottoscrizione](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testare l'offerta
Mentre è connesso al portale per gli utenti, è possibile testare l'offerta effettuando il provisioning di una macchina virtuale utilizzando le nuove funzionalità di sottoscrizione. 

> [!NOTE]
> Questo test è necessario che una macchina virtuale di Windows Server 2016 Data Center prima di tutto è stato aggiunto a marketplace dello Stack di Azure. 

1. Accedere al portale per gli utenti.

2. Nel portale per gli utenti, fare clic su **macchine virtuali** > **Add** > **Data Center di Windows Server 2016**, quindi fare clic su **crea** .

3. Nel **nozioni di base** , immettere un **nome**, **nome utente**, e **Password**, scegliere un **sottoscrizione**, creare un **gruppo di risorse** o selezionarne uno esistente, quindi fare clic su **OK**.

4. Nel **scegliere una dimensione** fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  

5. Nel pannello delle impostazioni, accettare le impostazioni predefinite e fare clic su **OK**.

6. Nel **riepilogo** fare clic su **OK** per creare la macchina virtuale.  

7. Per visualizzare la nuova macchina virtuale, fare clic su **macchine virtuali**, quindi cercare la nuova macchina virtuale e fare clic sul relativo nome.

    ![Tutte le risorse](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Distribuzione della macchina virtuale richiederà alcuni minuti.


## <a name="next-steps"></a>Passaggi successivi

Le informazioni acquisite in questa esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta


> [!div class="nextstepaction"]
> [Creare una macchina virtuale da un modello della community](azure-stack-create-vm-template.md)