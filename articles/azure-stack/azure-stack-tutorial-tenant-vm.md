---
title: Rendere disponibili agli utenti di Azure Stack macchine virtuali | Documenti Microsoft
description: Esercitazione per rendere disponibili le macchine virtuali nello Stack di Azure
services: azure-stack
documentationcenter: 
author: vhorne
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f6fce4a3230c98295afb19e633bf2801c115831f
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti di Azure Stack macchine virtuali

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Un amministratore di cloud di Azure Stack, è possibile creare offerte che possono sottoscrivere gli utenti (talvolta detto tenant). Con la sottoscrizione, gli utenti potranno quindi utilizzare i servizi di Azure Stack.

In questo articolo viene illustrato come creare un'offerta e quindi eseguirne il test. Per il test, si verrà Accedi al portale come utente, sottoscrivere l'offerta e quindi creare una macchina virtuale tramite la sottoscrizione.

Lezioni dell'esercitazione:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta


Nello Stack di Azure, servizi vengono recapitati a utenti che utilizzano i piani, offerte e sottoscrizioni. Gli utenti possano sottoscrivere più offerte. Le offerte possono includere uno o più piani, che a loro volta possono includere uno o più servizi.

![I piani, offerte e sottoscrizioni](media/azure-stack-key-features/image4.png)

Per ulteriori informazioni, vedere [chiave concetti nello Stack di Azure e le funzionalità](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Creare un'offerta

Ora è possibile ottenere elementi pronti per gli utenti. Quando si avvia il processo, viene chiesto prima di creare l'offerta, quindi un piano e infine le quote.

3. **Creare un'offerta**

   Offerte sono gruppi di uno o più piani che i provider di presentano agli utenti per l'acquisto o sottoscrivere.

   a. [Accedi](azure-stack-connect-azure-stack.md) al portale come un amministratore del cloud e quindi fare clic su **New** > **offre + piani** > **offrono**.
   ![Nuova offerta](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. Nel **offrono nuove** sezione, compilare **nome visualizzato** e **nome risorsa**e quindi selezionare una nuova o esistente **gruppo di risorse**. Il nome visualizzato è nome descrittivo dell'offerta. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome che gli amministratori utilizzano per funzionare con l'offerta come risorsa di gestione risorse di Azure.

   ![Nome visualizzato](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Fare clic su **piani di Base**e il **piano** fare clic su **Aggiungi** per aggiungere un nuovo piano per l'offerta.

   ![Aggiungere un piano](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. Nel **nuovo piano** sezione, compilare **nome visualizzato** e **nome risorsa**. Il nome visualizzato è nome descrittivo del piano visualizzato dagli utenti. Solo l'operatore cloud è possibile visualizzare il nome della risorsa. È il nome che gli operatori di cloud eseguire per utilizzare con il piano come risorsa di gestione risorse di Azure.

   ![Nome del piano di visualizzazione](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Fare clic su **servizi**selezionare **Microsoft. COMPUTE**, **Network**, e **Microsoft.Storage**, quindi fare clic su **Selezionare**.

   ![Servizi del piano](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Fare clic su **quote**, quindi selezionare il primo servizio per il quale si desidera creare una quota. Per una quota di IaaS, seguire questi passaggi per i servizi di calcolo, rete e archiviazione.

   In questo esempio, è necessario creare una quota per il servizio di calcolo. Nell'elenco dello spazio dei nomi, selezionare il **Microsoft. COMPUTE** dello spazio dei nomi e quindi fare clic su **Crea nuova quota**.
   
   ![Creare una nuova quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Nel **Crea quota** sezione, digitare un nome per la quota e impostare i parametri desiderati per la quota e fare clic su **OK**.

   ![Nome della quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. A questo punto, per **Microsoft. COMPUTE**, selezionare la quota creato.

   ![Selezionare quota](media/azure-stack-tutorial-tenant-vm/image08.png)

   Ripetere questi passaggi per i servizi di rete e archiviazione e quindi fare clic su **OK** sul **quote** sezione.

   i. Fare clic su **OK** sul **nuovo piano** sezione.

   j. Nel **piano** sezione, selezionare il nuovo piano e fare clic su **selezionare**.

   k. Nel **nuova offerta** fare clic su **crea**. Viene visualizzata una notifica quando l'offerta è stato creato.

   l. Scegliere il menu del dashboard, **offre** e quindi scegliere l'offerta è stato creato.

   m. Fare clic su **modifica dello stato**, quindi fare clic su **pubblica**.

   ![Stato pubblico](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Aggiungere un'immagine

Prima che è possibile eseguire il provisioning di macchine virtuali, è necessario aggiungere un'immagine del marketplace di Stack di Azure. È possibile aggiungere l'immagine di propria scelta, incluse le immagini Linux, da Azure Marketplace.

Se si opera in uno scenario connesso e se l'istanza dello Stack di Azure è stato registrato con Azure, quindi è possibile scaricare l'immagine di macchina virtuale di Windows Server 2016 da Azure Marketplace tramite la procedura descritta nel [Download marketplace elementi da Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md) argomento.

Per informazioni sull'aggiunta di diversi elementi nel Marketplace, vedere [The Azure Marketplace di Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testare l'offerta

Dopo aver creato un'offerta, è possibile eseguirne il test. Accedere come utente e sottoscrivere l'offerta e quindi aggiungere una macchina virtuale.

1. **Sottoscrivere un'offerta**

   Ora è possibile accedere al portale come utente di sottoscrivere un'offerta.

   a. Accedere al portale per gli utenti come un utente e fare clic su **ottenere una sottoscrizione**.
   - Per un sistema integrato, l'URL varia in base sulla regione dell'operatore e il nome di dominio esterno e si trova il https://portal formato. &lt; *area*&gt;.&lt; *FQDN*&gt;.
   - Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. Nel **nome visualizzato** campo, digitare un nome per la sottoscrizione, fare clic su **offrono**, fare clic su una delle offerte nel **scegliere un'offerta** sezione e quindi fare clic su  **Creare**.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Per visualizzare la sottoscrizione è stato creato, fare clic su **più servizi**, fare clic su **sottoscrizioni**, quindi fare clic sulla nuova sottoscrizione.  

   Dopo la sottoscrizione a un'offerta, aggiornare il portale di servizi che fanno parte della nuova sottoscrizione.

2. **Effettuare il provisioning di una macchina virtuale**

   Ora è possibile accedere al portale come utente di eseguire il provisioning di una macchina virtuale tramite la sottoscrizione. 

   a. Accedere al portale per gli utenti con un account utente.
      - Per un sistema integrato, l'URL varia in base sulla regione dell'operatore e il nome di dominio esterno e si trova il https://portal formato. &lt; *area*&gt;.&lt; *FQDN*&gt;.
   - Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

   b.  Nel dashboard, fare clic su **New** > **calcolo** > **Eval di Data Center di Windows Server 2016**, quindi fare clic su **crea**.

   c. Nel **nozioni di base** , immettere un **nome**, **nome utente**, e **Password**, scegliere un **sottoscrizione**, creare un **gruppo di risorse** o selezionarne uno esistente, quindi fare clic su **OK**.

   d. Nel **scegliere una dimensione** fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  

   e. Nel **impostazioni** fare clic su **rete virtuale**. Nel **rete virtuale scegliere** fare clic su **Crea nuovo**. Nel **crea rete virtuale** sezione accettare tutte le impostazioni predefinite e fare clic su **OK**. Nel **impostazioni** fare clic su **OK**.

   ![Creare una rete virtuale](media/azure-stack-provision-vm/image04.png)

   f. Nel **riepilogo** fare clic su **OK** per creare la macchina virtuale.  

   g. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, quindi cercare la macchina virtuale e fare clic sul relativo nome.

    ![Tutte le risorse](media/azure-stack-provision-vm/image06.png)

Le informazioni acquisite in questa esercitazione:

> [!div class="checklist"]
> * Creare un'offerta
> * Aggiungere un'immagine
> * Testare l'offerta

> [!div class="nextstepaction"]
> [Rendere disponibili agli utenti di Azure Stack web, dispositivi mobili e App per le API](azure-stack-tutorial-app-service.md)