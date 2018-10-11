---
title: In questa esercitazione descrive come sottoscrivere un'offerta di Azure Stack | Microsoft Docs
description: Questa esercitazione illustra come creare una nuova sottoscrizione a servizi di Azure Stack e testare l'offerta tramite la creazione di una macchina virtuale di test.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: aff20cba3bc07924f669420a8c367613b41111a1
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076101"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Esercitazione: creare e testare una sottoscrizione
Questa esercitazione illustra come creare una sottoscrizione che contiene un'offerta e quindi testarla. Per il test, si verrà Accedi al portale utenti Azure Stack come amministratore del cloud, sottoscrive l'offerta e quindi creare una macchina virtuale.

> [!TIP]
> Per più una più avanzata esperienza di valutazione, puoi [creare una sottoscrizione per un determinato utente](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) e quindi accedere come utente nel portale per gli utenti. 

Questa esercitazione illustra come sottoscrivere un'offerta di Azure Stack.

Che cosa si apprenderà:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

## <a name="subscribe-to-an-offer"></a>Sottoscrivere un'offerta
Per sottoscrivere un'offerta con un account utente, è necessario eseguire l'accesso al portale utenti di Azure Stack per individuare i servizi che sono stati offerti dall'operatore di Azure Stack.

1. Accedere al portale per gli utenti e fare clic su **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-services/get-subscription.png)

2. Nel campo **Nome visualizzato** digitare un nome per la sottoscrizione. Fare quindi clic **offrono** per selezionare una delle offerte disponibili nel **scegliere un'offerta** sezione e quindi fare clic su **Create**.

   ![Creare un'offerta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > È ora necessario aggiornare il portale per gli utenti per iniziare a usare la sottoscrizione.

3. Per visualizzare la sottoscrizione è stato creato, fare clic su **tutti i servizi**.  Quindi, sotto il **generali** categoria, selezionare **sottoscrizioni**e quindi selezionare la nuova sottoscrizione. Dopo la sottoscrizione a un'offerta, aggiornare il portale per vedere se i nuovi servizi sono stati inclusi come parte della nuova sottoscrizione. In questo esempio **macchine virtuali** è stato aggiunto.

   ![Visualizza sottoscrizione](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testare l'offerta
Mentre si è connessi al portale utenti, è possibile testare l'offerta effettuando il provisioning di una macchina virtuale usando le nuove funzionalità di sottoscrizione. 

> [!NOTE]
> Questo test richiede che una macchina virtuale di Windows Server 2016 Datacenter prima di tutto è stato aggiunto nel Marketplace di Azure Stack. 

1. Accedere al portale per gli utenti.

2. Nel portale per gli utenti, fare clic su **macchine virtuali** > **Add** > **Windows Server 2016 Datacenter**, quindi fare clic su **Create** .

3. Nel **nozioni di base** , quindi digitare un **Name**, **nome utente**, e **Password**, scegliere un **sottoscrizione**, creare un **gruppo di risorse** (o selezionarne uno esistente), quindi fare clic su **OK**.

4. Nel **Scegli una dimensione** fare clic su **Standard A1**, quindi fare clic su **selezionare**.  

5. Nel pannello delle impostazioni, accettare le impostazioni predefinite e fare clic su **OK**.

6. Nel **Summary** fare clic su **OK** per creare la macchina virtuale.  

7. Per visualizzare la nuova macchina virtuale, fare clic su **macchine virtuali**, quindi cercare la nuova macchina virtuale e fare clic sul relativo nome.

    ![Tutte le risorse](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> La distribuzione di macchina virtuale richiederà alcuni minuti.


## <a name="next-steps"></a>Passaggi successivi

Quanto appreso in questa esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta


> [!div class="nextstepaction"]
> [Creare una macchina virtuale da un modello della community](azure-stack-create-vm-template.md)