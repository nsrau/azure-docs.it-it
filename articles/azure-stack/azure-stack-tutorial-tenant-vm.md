---
title: Rendere disponibili agli utenti di Azure Stack le macchine virtuali | Microsoft Docs
description: Informazioni su come rendere disponibili le macchine virtuali in Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 09b9126125006fb70f5e2560f04b815b4a874405
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027300"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Esercitazione: rendere le macchine virtuali disponibili per gli utenti di Azure Stack

In qualità di amministratore di cloud di Azure Stack, è possibile creare offerte che possono sottoscrivere gli utenti (talvolta detto tenant). Tramite la sottoscrizione a un'offerta, gli utenti possono utilizzare i servizi di Azure Stack che fornisce un'offerta.

Questa esercitazione illustra come creare un'offerta per una macchina virtuale e quindi accedere come un utente per testare l'offerta.

Che cosa si apprenderà:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta

In Azure Stack, i servizi vengono forniti agli utenti tramite sottoscrizioni, offerte e piani. Gli utenti possono sottoscrivere più offerte. Un'offerta può includere uno o più piani e un piano può avere uno o più servizi.

![Le sottoscrizioni, offerte e piani](media/azure-stack-key-features/image4.png)

Per altre informazioni, vedere [chiave di funzionalità e concetti di Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Creare un'offerta

Le offerte sono gruppi di uno o più piani che i provider propongono agli utenti per l'acquisto o sottoscrivere. Il processo di creazione di un'offerta prevede diversi passaggi. In primo luogo, viene chiesto di creare l'offerta, quindi un piano e infine, le quote.

1. [Accedere](azure-stack-connect-azure-stack.md) al portale come amministratore del cloud e quindi selezionare **New** > **offre + piani** > **offrono**.

   ![Nuova offerta](media/azure-stack-tutorial-tenant-vm/image01.png)

1. Nelle **offrono nuove**, immettere una **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è nome descrittivo dell'offerta. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome usato dagli amministratori per lavorare con l'offerta come risorsa di Azure Resource Manager.

   ![Nome visualizzato](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Selezionare **piani di Base**e il **piano** selezionare **Aggiungi** per aggiungere un nuovo piano all'offerta.

   ![Aggiungere un piano](media/azure-stack-tutorial-tenant-vm/image03.png)

1. Nel **nuovi piani** sezione, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome che gli operatori cloud usano per lavorare con il piano come una risorsa di Azure Resource Manager.

   ![Nome visualizzato del piano](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Selezionare **Services**. Dall'elenco dei servizi, selezionare **Microsoft. COMPUTE**, **Microsoft. Network**, e **Microsoft. Storage**. Scegli **seleziona** per aggiungere questi servizi al piano.

   ![Servizi del piano](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Selezionare **quote**e quindi selezionare il primo servizio che si desidera creare una quota per. Per una quota di IaaS, usare l'esempio seguente come guida per la configurazione delle quote per i servizi di calcolo, rete e archiviazione.

   - Creare innanzitutto una quota per il servizio di calcolo. Nell'elenco dello spazio dei nomi, selezionare **Microsoft. COMPUTE** e quindi selezionare **Crea nuova quota**.

     ![Crea nuova quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Nelle **Crea quota**, immettere un nome per la quota. È possibile modificare o accettare uno dei valori di quota che vengono visualizzati per la quota che si sta creando. In questo esempio, si accettano le impostazioni predefinite e selezionare **OK**.

     ![Nome della quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Prelievo **Microsoft. COMPUTE** nell'elenco dello spazio dei nomi, quindi selezionare la quota che è stato creato. Questo collega la quota per il servizio di calcolo.

     ![Selezionare quota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Ripetere questi passaggi per i servizi di rete e archiviazione. Al termine, selezionare **OK** nelle **quote** per salvare tutte le quote.

1. Nelle **nuovo piano**, selezionare **OK**.

1. Sotto **piano**, selezionare il nuovo piano e quindi **seleziona**.

1. Nelle **nuova offerta**, selezionare **crea**. Verrà visualizzata una notifica quando viene creato l'offerta.

1. Nel menu del dashboard, selezionare **offre** e quindi selezionare l'offerta è stato creato.

1. Selezionare **modifica stato**e quindi scegliere **pubblico**.

    ![Pubblico stato](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Aggiungere un'immagine

Prima è possibile eseguire il provisioning di macchine virtuali, è necessario aggiungere un'immagine di marketplace di Azure Stack. È possibile aggiungere l'immagine di propria scelta, incluse le immagini Linux, da Azure Marketplace.

Se si opera in uno scenario connesso e se l'istanza di Azure Stack è stato registrato con Azure, quindi è possibile scaricare l'immagine di macchina virtuale di Windows Server 2016 da Azure Marketplace usando la procedura descritta nel [Download marketplace gli elementi da Azure ad Azure Stack](azure-stack-download-azure-marketplace-item.md) argomento.

Per informazioni sull'aggiunta di elementi diversi nel Marketplace, vedere [Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testare l'offerta

Ora che è stato creato un'offerta, è possibile eseguirne il test. Dovrai accedere con un account utente, sottoscrive l'offerta e quindi aggiungere una macchina virtuale.

1. **Sottoscrivere un'offerta**

   a. Accedere al portale per gli utenti con un account utente e selezionare il **ottenere una sottoscrizione** riquadro.
   - Per un sistema integrato, varia a seconda dell'operatore area e nome di dominio esterno, l'URL e sarà nel formato https://portal.&lt; *area geografica*&gt;.&lt; *FQDN*&gt;.
   - Se si usa Azure Stack Development Kit, l'indirizzo del portale è https://portal.local.azurestack.external.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. Nelle **ottenere una sottoscrizione**, immettere un nome per la sottoscrizione nel **nome visualizzato** campo. Selezionare **offrono**, quindi scegliere una delle offerte nel **scegliere un'offerta** elenco. Selezionare **Create**.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Per visualizzare la sottoscrizione, selezionare **tutti i servizi**, quindi nel **generali** categoria, selezionare **sottoscrizioni**. Selezionare la nuova sottoscrizione per vedere quali servizi fanno parte della sottoscrizione.

   >[!NOTE]
   >Dopo la sottoscrizione a un'offerta, potrebbe essere necessario aggiornare il portale per vedere quali servizi fanno parte della nuova sottoscrizione.

1. **Effettuare il provisioning di una macchina virtuale**

   Dal portale per gli utenti è possibile eseguire il provisioning di una macchina virtuale usando la nuova sottoscrizione.

   a. Accedere al portale per gli utenti con un account utente.
      - Per un sistema integrato, varia a seconda dell'operatore area e nome di dominio esterno, l'URL e sarà nel formato https://portal.&lt; *area geografica*&gt;.&lt; *FQDN*&gt;.
   - Se si usa Azure Stack Development Kit, l'indirizzo del portale è https://portal.local.azurestack.external.

   b.  Nel dashboard, selezionare **New** > **calcolo** > **Windows Server 2016 Datacenter Eval**, quindi selezionare **crea**.

   c. Nelle **nozioni di base**, fornire le informazioni seguenti:
      - Immettere un **nome**
      - Immettere un **nome utente**
      - Immettere un **Password**
      - Scegliere una **sottoscrizione**
      - Creare un **gruppo di risorse** (o selezionarne uno esistente). 
      - Selezionare **OK** per salvare queste informazioni.

   d. Nelle **Scegli una dimensione**, selezionare **Standard A1**e quindi **selezionare**.  

   e. Nelle **le impostazioni**, selezionare **rete virtuale**.

   f. Nelle **Scegli rete virtuale**, selezionare **Crea nuovo**.

   g. Nelle **crea rete virtuale**, accettare tutte le impostazioni predefinite e selezionare **OK**.

   h. Selezionare **OK** nelle **impostazioni** per salvare la configurazione di rete.

   ![Creare una rete virtuale](media/azure-stack-provision-vm/image04.png)

   i. Nelle **Summary**, selezionare **OK** per creare la macchina virtuale.  

   j. Per visualizzare la nuova macchina virtuale, selezionare **tutte le risorse**. Cercare la macchina virtuale e selezionare il nome nei risultati della ricerca.

   ![Tutte le risorse](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta

Passare all'esercitazione successiva per apprendere come:
> [!div class="nextstepaction"]
> [Rendere disponibili i database SQL per gli utenti di Azure Stack](azure-stack-tutorial-sql-server.md)