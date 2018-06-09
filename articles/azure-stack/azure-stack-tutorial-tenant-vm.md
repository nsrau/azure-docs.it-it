---
title: Rendere disponibili agli utenti di Azure Stack macchine virtuali | Documenti Microsoft
description: Scopri come rendere disponibili le macchine virtuali nello Stack di Azure
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248160"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Esercitazione: rendere disponibili le macchine virtuali per gli utenti di Azure Stack

Un amministratore di cloud di Azure Stack, è possibile creare offerte che possono sottoscrivere gli utenti (talvolta detto tenant). Tramite la sottoscrizione a un'offerta, gli utenti possono utilizzare i servizi di Azure Stack che fornisce un'offerta.

Questa esercitazione viene illustrato come creare un'offerta per una macchina virtuale e quindi accedere come un utente per testare l'offerta.

Lezioni dell'esercitazione:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta

Nello Stack di Azure, servizi vengono recapitati a utenti che utilizzano i piani, offerte e sottoscrizioni. Gli utenti possano sottoscrivere più offerte. Un'offerta può avere uno o più piani e un piano può avere uno o più servizi.

![I piani, offerte e sottoscrizioni](media/azure-stack-key-features/image4.png)

Per ulteriori informazioni, vedere [chiave concetti nello Stack di Azure e le funzionalità](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Creare un'offerta

Offerte sono gruppi di uno o più piani che i provider di presentano agli utenti per l'acquisto o sottoscrivere. Il processo di creazione di un'offerta comprende diversi passaggi. Prima di tutto, viene chiesto di creare l'offerta, quindi un piano e, infine, le quote.

1. [Accedi](azure-stack-connect-azure-stack.md) per il portale come amministratore del cloud e quindi selezionare **New** > **offre + piani** > **offrono**.

   ![Nuova offerta](media/azure-stack-tutorial-tenant-vm/image01.png)

2. In **offrono nuove**, immettere un **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è nome descrittivo dell'offerta. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome che gli amministratori utilizzano per funzionare con l'offerta come risorsa di gestione risorse di Azure.

   ![Nome visualizzato](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Selezionare **piani di Base**e il **piano** sezione, selezionare **Aggiungi** per aggiungere un nuovo piano per l'offerta.

   ![Aggiungere un piano](media/azure-stack-tutorial-tenant-vm/image03.png)

4. Nel **nuovo piano** sezione, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome che gli operatori di cloud eseguire per utilizzare con il piano come risorsa di gestione risorse di Azure.

   ![Nome del piano di visualizzazione](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Selezionare **Services**. Dall'elenco dei servizi, selezionare **Microsoft. COMPUTE**, **Microsoft. Network**, e **appartenga**. Scegliere **selezionare** per aggiungere questi servizi al piano.

   ![Servizi del piano](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Selezionare **quote**, quindi selezionare il primo servizio che si desidera creare una quota per. Per una quota di IaaS, usare l'esempio seguente come guida per la configurazione di quote per i servizi di calcolo, rete e archiviazione.

   - Creare innanzitutto una quota per il servizio di calcolo. Nell'elenco dello spazio dei nomi, selezionare **Microsoft. COMPUTE** e quindi selezionare **Crea nuova quota**.

     ![Crea nuova quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - In **Crea quota**, immettere un nome per la quota. È possibile modificare o accettare uno dei valori di quota che vengono visualizzati per la quota che si sta creando. In questo esempio, accettare le impostazioni predefinite e selezionare **OK**.

     ![Nome della quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Pick **Microsoft. COMPUTE** nell'elenco dello spazio dei nomi, quindi selezionare la quota creato. Questo collega la quota per il servizio di calcolo.

     ![Selezionare quota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Ripetere questi passaggi per i servizi di rete e archiviazione. Al termine, selezionare **OK** in **quote** per salvare tutte le quote.

7. In **nuovo piano**, selezionare **OK**.

8. Sotto **piano**, selezionare il nuovo piano e quindi **selezionare**.

9. In **nuova offerta**, selezionare **crea**. Verrà visualizzata una notifica quando viene creato l'offerta.

10. Nel menu del dashboard, selezionare **offre** , quindi selezionare l'offerta è stato creato.

11. Selezionare **modifica dello stato**, quindi scegliere **pubblica**.

    ![Stato pubblico](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Aggiungere un'immagine

Prima che è possibile eseguire il provisioning di macchine virtuali, è necessario aggiungere un'immagine del marketplace di Stack di Azure. È possibile aggiungere l'immagine di propria scelta, incluse le immagini Linux, da Azure Marketplace.

Se si opera in uno scenario connesso e se l'istanza dello Stack di Azure è stato registrato con Azure, quindi è possibile scaricare l'immagine di macchina virtuale di Windows Server 2016 da Azure Marketplace tramite la procedura descritta nel [Download marketplace elementi da Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md) argomento.

Per informazioni sull'aggiunta di diversi elementi nel Marketplace, vedere [The Azure Marketplace di Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testare l'offerta

Dopo aver creato un'offerta, è possibile eseguirne il test. Accedere con un nome utente, sottoscrivere l'offerta e vengono quindi aggiunte una macchina virtuale.

1. **Sottoscrivere un'offerta**

   a. Accedere al portale per gli utenti con un account utente e selezionare il **ottenere una sottoscrizione** riquadro.
   - Per un sistema integrato, l'URL varia in base area e il nome di dominio esterno dell'operatore e sarà nel formato https://portal.&lt; *area*&gt;.&lt; *FQDN*&gt;.
   - Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. In **ottenere una sottoscrizione**, immettere un nome per la sottoscrizione nel **nome visualizzato** campo. Selezionare **offrono**, quindi scegliere una delle offerte nel **scegliere un'offerta** elenco. Selezionare **Create**.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Per visualizzare la sottoscrizione, selezionare **altri servizi**, quindi selezionare **sottoscrizioni**. Selezionare la nuova sottoscrizione per vedere quali servizi sono parte della sottoscrizione.

   >[!NOTE]
   >Dopo la sottoscrizione a un'offerta, potrebbe essere necessario aggiornare il portale per vedere quali servizi sono parte della nuova sottoscrizione.

2. **Effettuare il provisioning di una macchina virtuale**

   Dal portale per gli utenti è possibile eseguire il provisioning di una macchina virtuale utilizzando la nuova sottoscrizione.

   a. Accedere al portale per gli utenti con un account utente.
      - Per un sistema integrato, l'URL varia in base area e il nome di dominio esterno dell'operatore e sarà nel formato https://portal.&lt; *area*&gt;.&lt; *FQDN*&gt;.
   - Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

   b.  Nel dashboard, selezionare **New** > **calcolo** > **Eval di Data Center di Windows Server 2016**, quindi selezionare **crea**.

   c. In **nozioni di base**, fornire le informazioni seguenti:
      - Immettere un **nome**
      - Immettere un **nome utente**
      - Immettere un **Password**
      - Scegliere una **sottoscrizione**
      - Creare una **gruppo di risorse** (o selezionarne uno esistente.) 
      - Selezionare **OK** per salvare queste informazioni.

   d. In **scegliere una dimensione**, selezionare **A1 Standard**, quindi **selezionare**.  

   e. In **impostazioni**, selezionare **rete virtuale**.

   f. In **rete virtuale Choose**, selezionare **Crea nuovo**.

   g. In **crea rete virtuale**, accettare tutte le impostazioni predefinite e selezionare **OK**.

   h. Selezionare **OK** in **impostazioni** per salvare la configurazione di rete.

   ![Creare una rete virtuale](media/azure-stack-provision-vm/image04.png)

   i. In **riepilogo**, selezionare **OK** per creare la macchina virtuale.  

   j. Per visualizzare la nuova macchina virtuale, selezionare **tutte le risorse**. Cercare la macchina virtuale, selezionarne il nome dai risultati della ricerca.

   ![Tutte le risorse](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta

Per passare alla prossima esercitazione per informazioni su come:
> [!div class="nextstepaction"]
> [Rendere disponibili agli utenti dello Stack di Azure SQL database](azure-stack-tutorial-sql-server.md)