---
title: Sottoscrivere un'offerta nello Stack di Azure | Documenti Microsoft
description: Creare sottoscrizioni per le offerte nello Stack di Azure
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Creare sottoscrizioni per le offerte nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Dopo aver [creare un'offerta](azure-stack-create-offer.md), gli utenti è necessaria una sottoscrizione a tale offerta prima di poter utilizzare.   
- Come operatore di cloud, è possibile creare una sottoscrizione per un utente all'interno del portale di amministrazione.  È possibile sottoscrizioni create per le offerte pubbliche e private.
- Come un utente di tenant, è possibile sottoscrivere un'offerta pubblica quando si usa il portale per gli utenti.  

Nelle sezioni seguenti forniscono istruzioni per gli operatori di cloud durante la creazione di sottoscrizioni per gli utenti e come sottoscrivere un'offerta con un account utente.

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creare una sottoscrizione come operatore di cloud
Gli operatori di cloud è possono utilizzare il portale di amministrazione per creare una sottoscrizione a un'offerta per un utente.  È possibile creare sottoscrizioni per i membri del tenant di directory.  Quando [multi-tenancy](azure-stack-enable-multitenancy.md) è abilitato, è inoltre possibile creare sottoscrizioni per gli utenti nel tenant di directory aggiuntive.

È possibile creare sottoscrizioni per le offerte pubbliche e private.  Se non si desidera che i tenant per creare le proprie sottoscrizioni, tutte le privato offerte e quindi creare sottoscrizioni per conto di tenant. Questo approccio è comune per l'integrazione di Azure Stack con fatturazione esterna o sistemi di servizio catalogo.

Dopo aver creato una sottoscrizione per un utente, tale utente può accedere al portale per gli utenti e troverà che hanno eseguito la sottoscrizione per l'offerta.  

### <a name="to-create-the-subscription-for-a-user"></a>Per creare la sottoscrizione per un utente
1.  Nel portale di amministrazione, passare a **le sottoscrizioni dell'utente.**
2.  Selezionare **Aggiungi** per aprire la **nuova sottoscrizione utente** riquadro. Specificare i dettagli seguenti:  

  a. **Nome visualizzato** : un nome descrittivo per identificare la sottoscrizione che viene visualizzato come il *nome sottoscrizione utente*.

  b. **Utente** : specificare un utente di un tenant di directory disponibili per questa sottoscrizione. Il nome utente viene visualizzato come *proprietario*.  Il formato del nome utente dipende dalla soluzione delle identità. Ad esempio:    

     -  **Azure AD:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  c.    **Tenant di directory** : selezionare il tenant di directory in cui appartiene l'account utente. Se non è stato abilitato per multi-tenancy, è disponibile solo al tenant di directory locale.

3.  Selezionare **offrono** per aprire la **offre** riquadro, quindi scegliere un **offrono** per questa sottoscrizione. Poiché si sta creando una sottoscrizione per un utente, è possibile selezionare un'offerta pubblica o privata.

4.  Selezionare **crea** per creare la sottoscrizione. Il **sottoscrizioni utente** riquadro Visualizza ora la nuova sottoscrizione.  In un secondo momento, quando l'utente accede al portale di utente, è possibile visualizzare informazioni dettagliate su questa sottoscrizione.

### <a name="to-make-an-add-on-plan-available"></a>Per rendere disponibile un piano del componente aggiuntivo  
Un operatore di cloud è possibile aggiungere un piano del componente aggiuntivo a una sottoscrizione creata in precedenza in qualsiasi momento:   
1.  Nel portale di amministrazione, passare a **più servizi** > **sottoscrizioni utente**e quindi selezionare la sottoscrizione che si desidera modificare.   

2.  Selezionare **componenti aggiuntivi** > **Aggiungi** per aprire la **Aggiungi piano** riquadro.  

3.  Selezionare il piano a cui che si desidera aggiungere come componente aggiuntivo.  Nella console vengono quindi visualizzati i piani associati alla sottoscrizione.




## <a name="create-a-subscription-as-a-user"></a>Creare una sottoscrizione con un account utente
Come un utente, è possibile accedere al portale per gli utenti di trovare e sottoscrivere offerta pubblica e i piani di componente aggiuntivo dal tenant di directory (organizzazione). Quando l'ambiente dello Stack di Azure supporta [multi-tenancy](azure-stack-enable-multitenancy.md) è possibile sottoscrivere offerte da un tenant di directory remota.

### <a name="to-subscribe-to-an-offer"></a>Sottoscrivere un'offerta
1. [Accedi](azure-stack-connect-azure-stack.md) il portale utenti Azure Stack (https://portal.local.azurestack.external) e fare clic su **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. Nel **nome visualizzato** campo, digitare un nome per la sottoscrizione, fare clic su **offrono**, fare clic su una delle offerte nel **scegliere un'offerta** riquadro e quindi fare clic su  **Creare**.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Per visualizzare la sottoscrizione è stato creato, fare clic su **più servizi**, fare clic su **sottoscrizioni**, quindi fare clic sulla nuova sottoscrizione.  

Dopo la sottoscrizione a un'offerta, aggiornare il portale di servizi che fanno parte della nuova sottoscrizione.

### <a name="to-subscribe-to-an-add-on-plan"></a>Per sottoscrivere un piano del componente aggiuntivo
Se un'offerta dispone di un piano del componente aggiuntivo, è possibile aggiungere tale piano per la sottoscrizione in qualsiasi momento.  

1. Nel portale per gli utenti, selezionare **più servizi** > **sottoscrizioni**e quindi selezionare la sottoscrizione che si desidera modificare.

2. Selezionare **Aggiungi piano** pulsante e quindi selezionare il piano del componente aggiuntivo desiderato. Se **Aggiungi piano** è non disponibile, non vi sono piani alcun componente aggiuntivo per l'offerta associate alla sottoscrizione.



## <a name="next-steps"></a>Passaggi successivi
[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
