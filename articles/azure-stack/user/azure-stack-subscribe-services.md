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
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 431c31b39103e5ef21cb83b388167cab57e0c129
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649085"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Esercitazione: creare e testare una sottoscrizione

Questa esercitazione illustra come creare una sottoscrizione che contiene un'offerta e quindi testarla. Per il test, accedi al portale utenti Azure Stack come amministratore di cloud, sottoscrive l'offerta e quindi creare una macchina virtuale.

> [!TIP]
> Per più una più avanzata esperienza di valutazione, puoi [creare una sottoscrizione per un determinato utente](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) e quindi accedere come utente nel portale per gli utenti.

Questa esercitazione illustra come sottoscrivere un'offerta di Azure Stack.

Che cosa si apprenderà:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

## <a name="subscribe-to-an-offer"></a>Sottoscrivere un'offerta

Per sottoscrivere un'offerta con un account utente, accedi al portale utenti di Azure Stack per individuare i servizi che sono stati offerti dall'operatore di Azure Stack.

1. Accedi all'utente del portale e selezionare **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-services/get-subscription.png)

2. Nel campo **Nome visualizzato** digitare un nome per la sottoscrizione. Quindi selezionare **offrono** per scegliere una delle offerte disponibili nel **scegliere un'offerta** sezione. Selezionare quindi **Crea**.

   ![Creare un'offerta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > È ora necessario aggiornare il portale per gli utenti per iniziare a usare la sottoscrizione.

3. Per visualizzare la sottoscrizione è stato creato, selezionare **tutti i servizi**. Quindi, sotto il **generali** categoria, selezionare **sottoscrizioni**e quindi selezionare la nuova sottoscrizione. Dopo la sottoscrizione a un'offerta, aggiornare il portale per vedere se i nuovi servizi sono stati inclusi come parte della nuova sottoscrizione. In questo esempio **macchine virtuali** è stato aggiunto.

   ![Visualizza sottoscrizione](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testare l'offerta

Eseguito l'accesso al portale utenti, è possibile testare l'offerta effettuando il provisioning di una macchina virtuale usando le nuove funzionalità di sottoscrizione.

> [!NOTE]
> Questo test richiede che una macchina virtuale di Windows Server 2016 Datacenter prima di tutto è stato aggiunto nel Marketplace di Azure Stack.

1. Accedere al portale per gli utenti.

2. Nel portale per gli utenti, selezionare **macchine virtuali**, quindi **Add**, quindi **Windows Server 2016 Datacenter**, quindi fare clic su **Create**.

3. Nel **nozioni di base** , quindi digitare un **Name**, **nome utente**, e **Password**, scegliere un **sottoscrizione**, creare un **gruppo di risorse** (o selezionarne uno esistente), quindi selezionare **OK**.

4. Nel **Scegli una dimensione** sezione, selezionare **Standard A1**, quindi fare clic su **selezionare**.  

5. Nel **le impostazioni** blade, accettare le impostazioni predefinite e selezionare **OK**.

6. Nel **Summary** fare clic su **OK** per creare la macchina virtuale.  

7. Per visualizzare la nuova macchina virtuale, selezionare **macchine virtuali**, quindi cercare la nuova macchina virtuale e fare clic sul relativo nome.

    ![Tutte le risorse](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> La distribuzione di macchina virtuale richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Quanto appreso in questa esercitazione:

> [!div class="checklist"]
> * Sottoscrivere un'offerta 
> * Testare l'offerta

> [!div class="nextstepaction"]
> [Creare una macchina virtuale da un modello della community](azure-stack-create-vm-template.md)