---
title: Sottoscrivere un'offerta nello Stack di Azure | Documenti Microsoft
description: Creare sottoscrizioni per le offerte nello Stack di Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295211"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Creare sottoscrizioni per le offerte nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Dopo aver [creare un'offerta](azure-stack-create-offer.md), gli utenti è necessaria una sottoscrizione a tale offerta prima di poter utilizzare. Esistono due modi che gli utenti possono ottenere sottoscritti a un'offerta:

- Come operatore di cloud, è possibile creare una sottoscrizione per un utente all'interno del portale di amministrazione. È possibile sottoscrizioni create per le offerte pubbliche e private.
- Come un utente di tenant, è possibile sottoscrivere un'offerta pubblica quando si usa il portale per gli utenti.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creare una sottoscrizione come operatore di cloud

Gli operatori di cloud è possono utilizzare il portale di amministrazione per creare una sottoscrizione a un'offerta per un utente.  È possibile creare sottoscrizioni per i membri del tenant di directory.  Quando [multi-tenancy](azure-stack-enable-multitenancy.md) è abilitato, è inoltre possibile creare sottoscrizioni per gli utenti nel tenant di directory aggiuntive.

Se non desidera che i tenant per creare le proprie sottoscrizioni, rendere privato le offerte e quindi creare le sottoscrizioni per i tenant. Questo approccio è comune per l'integrazione di Azure Stack con fatturazione esterna o sistemi di servizio catalogo.

Dopo aver creato una sottoscrizione per un utente, possono accedere al portale per gli utenti e vedere che è stata effettuata la sottoscrizione dell'offerta.  

### <a name="to-create-a-subscription-for-a-user"></a>Per creare una sottoscrizione per un utente

1. Nel portale di amministrazione, passare a **le sottoscrizioni dell'utente.**
2. Selezionare **Aggiungi**. Sotto **nuova sottoscrizione utente**, immettere le informazioni seguenti:  

   - **Nome visualizzato** : un nome descrittivo per identificare la sottoscrizione che viene visualizzato come il *nome sottoscrizione utente*.
   - **Utente** : specificare un utente da un tenant di directory disponibili per questa sottoscrizione. Il nome utente viene visualizzato come *proprietario*.  Il formato del nome utente dipende dalla soluzione delle identità. Ad esempio: 

     - **Azure AD:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     - **ADFS:***&lt;user1 > @&lt;azurestack.local >* 

   - **Tenant di directory** : selezionare il tenant di directory in cui appartiene l'account utente. Se non è stata abilitata multi-tenancy, solo il tenant di directory locale è disponibile.

3. Selezionare **offrono**. Sotto **offre**, scegliere un **offrono** per questa sottoscrizione. Poiché si sta creando la sottoscrizione per un utente, selezionare **privato** come lo stato di accessibilità.

4. Selezionare **crea** per creare la sottoscrizione. Verrà visualizzata la nuova sottoscrizione sotto **sottoscrizione utente**. Quando l'utente accede al portale utenti è possibile visualizzare i dettagli della sottoscrizione.

### <a name="to-make-an-add-on-plan-available"></a>Per rendere disponibile un piano del componente aggiuntivo

Un operatore di cloud è possibile aggiungere un piano del componente aggiuntivo a una sottoscrizione creata in precedenza in qualsiasi momento:

1. Nel portale di amministrazione, selezionare **più servizi** > **sottoscrizioni utente**. Selezionare la sottoscrizione da modificare.

2. Selezionare **i componenti aggiuntivi** e quindi selezionare **+ Aggiungi**.  

3. Sotto **Aggiungi piano**, selezionare il piano desiderato come componente aggiuntivo.

## <a name="create-a-subscription-as-a-user"></a>Creare una sottoscrizione con un account utente

Come un utente, è possibile accedere al portale per gli utenti per individuare ed eseguire la sottoscrizione alle offerte pubbliche e ai piani di componente aggiuntivo per il tenant di directory (organizzazione).

>[!NOTE]
>Se l'ambiente dello Stack di Azure supporta [multi-tenancy](azure-stack-enable-multitenancy.md) è inoltre possibile sottoscrivere offerte da un tenant di directory remota.

### <a name="to-subscribe-to-an-offer"></a>Sottoscrivere un'offerta

1. [Accedi](azure-stack-connect-azure-stack.md) al portale utenti Azure Stack (https://portal.local.azurestack.external) e selezionare **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sotto **ottenere una sottoscrizione**, immettere il nome descrittivo della sottoscrizione in **nome visualizzato**. Selezionare **offrono** e in **scegliere un'offerta**, scegliere un'offerta. Selezionare **crea** per creare la sottoscrizione.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Dopo la sottoscrizione a un'offerta, aggiornare il portale di servizi che fanno parte della nuova sottoscrizione.
4. Per visualizzare la sottoscrizione è stato creato, selezionare **altri servizi** e quindi selezionare **sottoscrizioni**. Selezionare la sottoscrizione per visualizzare i dettagli della sottoscrizione.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Per sottoscrivere un piano del componente aggiuntivo

Se un'offerta dispone di un piano del componente aggiuntivo, è possibile aggiungere tale piano per la sottoscrizione in qualsiasi momento.  

1. Nel portale per gli utenti, selezionare **più servizi** > **sottoscrizioni**e quindi selezionare la sottoscrizione che si desidera modificare. Se sono disponibili, i piani di componente aggiuntivo **+ Aggiungi piano** è attiva ed è presente un riquadro per **piani di componente aggiuntivo**.

   >[!NOTE]
   >Se **+ Aggiungi piano** non è attivo, quindi non sono tutti i piani di componente aggiuntivo per l'offerta associate alla sottoscrizione.

1. Selezionare **+ Aggiungi piano** o il **piani di componente aggiuntivo** riquadro. Sotto **piani di componente aggiuntivo**, selezionare il piano di cui si desidera aggiungere.

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
