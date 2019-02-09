---
title: Sottoscrivere un'offerta in Azure Stack | Microsoft Docs
description: Creare sottoscrizioni per le offerte in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6f91d011e9f15b80c2efd6ad1c6bbcd549786882
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976844"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Creare le sottoscrizioni di offerte in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Dopo aver [creare un'offerta](azure-stack-create-offer.md), gli utenti necessari una sottoscrizione per l'offerta prima possono usare l'account. Esistono due modi che gli utenti possono ottenere sottoscritti a un'offerta:

- Come operatore di cloud, è possibile creare una sottoscrizione per un utente dall'interno del portale di amministratore. Le sottoscrizioni create è possibile per le offerte pubbliche e private.
- Come un utente del tenant, è possibile sottoscrivere un'offerta pubblica quando si usa il portale utenti.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creare una sottoscrizione come un operatore cloud

Gli operatori cloud è possono usare il portale di amministrazione per creare una sottoscrizione a un'offerta per un utente. È possibile creare sottoscrizioni per i membri del proprio tenant di directory. Quando [multi-tenancy](azure-stack-enable-multitenancy.md) è abilitato, è anche possibile creare sottoscrizioni per gli utenti nel tenant di directory aggiuntive.

Se non desidera che i tenant a creare le proprie sottoscrizioni, rendere privato le offerte e quindi creare le sottoscrizioni per i tenant. Questo approccio è comune quando si integra Azure Stack con fatturazione esterna o sistemi del servizio catalogo.

Dopo aver creato una sottoscrizione per un utente, possono accedere al portale per gli utenti e vedere che hanno eseguito la sottoscrizione all'offerta.  

### <a name="to-create-a-subscription-for-a-user"></a>Per creare una sottoscrizione per un utente

1. Nel portale di amministrazione, passare a **sottoscrizioni utente.**
2. Selezionare **Aggiungi**. Sotto **nuova sottoscrizione utente**, immettere le informazioni seguenti:  

   - **Nome visualizzato** : un nome descrittivo per identificare la sottoscrizione che viene visualizzato come il *il nome di sottoscrizione utente*.
   - **Utente** : specificare un utente da un tenant di directory disponibili per questa sottoscrizione. Il nome utente viene visualizzato come *proprietario*.  Il formato del nome utente dipende la soluzione di identità. Ad esempio:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Tenant di directory** : selezionare il tenant di directory a cui appartiene l'account utente. Se non è stata abilitata multi-tenancy, è disponibile solo il tenant di directory locale.

3. Selezionare **offrono**. Sotto **offre**, scegliere un' **offrono** per questa sottoscrizione. Poiché si sta creando la sottoscrizione per un utente, selezionare **privati** come lo stato di accessibilità.

4. Selezionare **Create** per creare la sottoscrizione. La nuova sottoscrizione viene visualizzato sotto **sottoscrizione utente**. Quando l'utente accede al portale utenti, è possibile visualizzare i dettagli della sottoscrizione.

### <a name="to-make-an-add-on-plan-available"></a>Per rendere disponibile un piano aggiuntivo

Un operatore cloud è possibile aggiungere un piano per una sottoscrizione creata in precedenza in qualsiasi momento:

1. Nel portale di amministrazione, selezionare **tutti i servizi** e quindi sotto il **risorse amministrative** categoria, seleziona **sottoscrizioni utente**. Selezionare la sottoscrizione da modificare.

2. Selezionare **i componenti aggiuntivi** e quindi selezionare **+ Aggiungi**.  

3. Sotto **Aggiungi piano**, selezionare il piano desiderato come componente aggiuntivo.

## <a name="create-a-subscription-as-a-user"></a>Creare una sottoscrizione con un account utente

Come un utente, è possibile accedere al portale utenti di trovare e sottoscrivere pubbliche offerte e piani aggiuntivi per il tenant di directory (organizzazione).

>[!NOTE]
>Se l'ambiente Azure Stack supporta [multi-tenancy](azure-stack-enable-multitenancy.md), è inoltre possibile sottoscrivere le offerte da un tenant di directory remota.

### <a name="to-subscribe-to-an-offer"></a>Sottoscrivere un'offerta

1. [Accedere](azure-stack-connect-azure-stack.md) per il [portale utenti di Azure Stack](https://portal.local.azurestack.external) e selezionare **ottenere una sottoscrizione**.

   ![Ottieni una sottoscrizione](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sotto **ottenere una sottoscrizione**, immettere il nome descrittivo della sottoscrizione in **nome visualizzato**. Selezionare **offrono** e nella **scegliere un'offerta**, scegliere un'offerta. Selezionare **Create** per creare la sottoscrizione.

   ![Creare un'offerta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Dopo la sottoscrizione a un'offerta, aggiornare il portale per vedere quali servizi fanno parte della nuova sottoscrizione.

4. Per visualizzare la sottoscrizione è stato creato, selezionare **tutti i servizi** e quindi nel **generali** categoria, selezionare **sottoscrizioni**. Selezionare la sottoscrizione per visualizzare i dettagli della sottoscrizione.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Per sottoscrivere un piano aggiuntivo

Se un'offerta ha un piano del componente aggiuntivo, è possibile aggiungere tale piano per la sottoscrizione in qualsiasi momento.  

1. Nel portale per gli utenti, selezionare **tutti i servizi**. Quindi, sotto il **generali** categoria, seleziona **sottoscrizioni**e quindi selezionare la sottoscrizione che si desidera modificare. Se sono disponibili, tutti i piani aggiuntivi **+ Aggiungi piano** è attivo ed è presente un riquadro per **piani aggiuntivi**. 

   Se **+ Aggiungi piano** non è attivo, quindi non esistono nessun piano aggiuntivo per l'offerta associato alla sottoscrizione.

1. Selezionare **+ Aggiungi piano** o nella **piani aggiuntivi** riquadro. Sotto **piani aggiuntivi**, selezionare il piano di cui si desidera aggiungere.

## <a name="next-steps"></a>Passaggi successivi

- [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
