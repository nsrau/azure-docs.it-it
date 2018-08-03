---
title: Delegare offerte in Azure Stack | Microsoft Docs
description: Informazioni su come inserire altri utenti responsabile della creazione di offerte e iscrizione gli utenti per l'utente.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 161a17f2360767dacc4f418a078c695d7cb8daa7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449959"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegare offerte in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

L'operatore di Azure Stack, è spesso necessario inserire altri utenti responsabile della creazione di offerte e iscrizione gli utenti. Ad esempio, se sei un provider di servizi, è possibile i rivenditori a effettuare l'iscrizione ai clienti e gestirli per tuo conto. In alternativa, se si fa parte di un gruppo IT centrale in un'organizzazione, è possibile delegare l'accesso degli utenti fino al personale IT.

Delega rende più semplice accedere e gestire più utenti che è possibile eseguire da soli. La figura seguente mostra un livello di delega, ma Azure Stack supporta più di un livello. I provider delegati (DPs) possono delegare ad altri provider, fino a cinque livelli.

![Livelli di delega](media/azure-stack-delegated-provider/image1.png)

## <a name="understand-delegation-roles-and-steps"></a>Comprendere i passaggi e i ruoli di delega

### <a name="delegation-roles"></a>Ruoli di delega

I ruoli seguenti fanno parte della delega:

* Il *operatore di Azure Stack* gestisce l'infrastruttura di Azure Stack e crea un modello di offerta. L'operatore di delega altri per proporre offerte ai propri utenti.

* Gli operatori di Azure Stack delegati vengono chiamati *delegate provider*. Appartengono ad altre organizzazioni, ad esempio altri utenti di Azure Active Directory (Azure AD).

* *Gli utenti* effettuare l'iscrizione per le offerte e usarli per la gestione dei carichi di lavoro, creazione di macchine virtuali, l'archiviazione dei dati e così via.

### <a name="delegation-steps"></a>Passaggi di delega

Esistono due passaggi di base per la configurazione di delega:

1. *Creare un provider delegato* sottoscrivendo un utente a un'offerta basata su un piano solo con il servizio di abbonamenti. Gli utenti che sottoscrivono questa offerta possono quindi estendere offerte e accesso degli utenti per le offerte.

1. *Delegare un'offerta per il provider delegato*. Questa offerta è un modello per ciò che il provider delegato può offrire. Il provider delegato può ora richiedere l'offerta e offrirlo ad altri utenti.

La figura successiva illustra i passaggi per configurare la delega.

![Creare il provider delegato e consentirne l'accesso degli utenti](media/azure-stack-delegated-provider/image2.png)

**Requisiti del provider delegata**

Per agire come un provider delegata, un utente deve essere stabilita una relazione con il provider principale mediante la creazione di una sottoscrizione. Questa sottoscrizione identifica il provider delegato come avente il diritto alle offerte presente per conto di principale provider.

Dopo aver stabilita questa relazione, l'operatore di Azure Stack può delegare un'offerta per il provider delegato. Il provider delegato può sfruttare l'offerta, rinominarlo (ma non modificare la sostanza) e offrono ai clienti.

## <a name="delegation-walkthrough"></a>Procedura dettagliata di delega

Le sezioni seguenti forniscono procedure dettagliate pratiche per la configurazione di un provider delegata, la delega di un'offerta e la verifica che gli utenti possono effettuare l'iscrizione per l'offerta delegata.

### <a name="set-up-roles"></a>Configurare i ruoli

Per usare questa procedura dettagliata, sono necessari due account di Azure AD oltre all'account di operatore di Azure Stack. Se non si dispone di questi due account, è necessario crearli. L'account può appartenere a qualsiasi utente di Azure AD e vengono definiti per il provider di delegato e l'utente.

| **Ruolo** | **Diritti dell'organizzazione** |
| --- | --- |
| Provider delegati |Utente |
| Utente |Utente |

### <a name="identify-the-delegated-provider"></a>Identificare il provider delegato

1. Accedere al portale di amministrazione come un operatore di Azure Stack.

1. Per creare un'offerta che consente a un utente a diventare un provider di delegato:

   a.  [Creare un piano](azure-stack-create-plan.md).
       Questo piano deve includere solo il servizio di abbonamenti. Questo articolo usa un piano denominato **PlanForDelegation** come esempio.

   b.  [Creare un'offerta](azure-stack-create-offer.md) basato su questo piano. Questo articolo si usa un'offerta denominata **OfferToDP** come esempio.

   c.  Aggiungere il provider delegato come sottoscrittore per questa offerta selezionando **abbonamenti** > **Add** > **nuova sottoscrizione di Tenant**.

   ![Aggiungere il provider delegato come sottoscrittore](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Come con tutte le offerte di Azure Stack, è possibile scegliere di rendere l'offerta pubblici e ciò consente agli utenti iscriversi a, o mantenendola private e consentire l'operatore di Azure Stack di gestire l'iscrizione. I provider delegati sono in genere un piccolo gruppo. Si vuole controllare chi è ammesso, in modo da mantenere privato questa offerta ha senso nella maggior parte dei casi.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operatore di Azure Stack consente di creare l'offerta delegata

Il passaggio successivo consiste nel creare il piano e offerta che si desidera delegare e che verrà usato dagli utenti. È consigliabile definire questa offerta, esattamente come si desidera che gli utenti visualizzato perché il provider delegato non è possibile modificare i piani e le quote che sono incluse.

1. Un operatore, Azure Stack [creare un piano](azure-stack-create-plan.md) e [un'offerta](azure-stack-create-offer.md) in base al piano. Questo articolo si usa un'offerta denominata **DelegatedOffer** come esempio.

   > [!NOTE]
   > Questa offerta non deve essere pubblico, ma è possibile renderlo pubblico se si desidera. Tuttavia, nella maggior parte dei casi solo necessario provider delegati per avere accesso all'offerta. Dopo la delega di un'offerta privata, come descritto nei passaggi seguenti, il provider delegato possa accedervi.

1. Delegare l'offerta. Passare a **DelegatedOffer**. Sotto **le impostazioni**, selezionare **provider delegata** > **Aggiungi**.

1. Selezionare la sottoscrizione per il provider delegati nell'elenco a discesa e quindi selezionare **delegato**.

   ![Aggiungere un provider delegato](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Provider delegata consente di personalizzare l'offerta

Accedere al portale per gli utenti come provider di delegato e quindi creare una nuova offerta tramite l'offerta delegata come modello.

1. Selezionare **nuove** > **Tenant offre + piani** > **offrono**.

    ![Creare una nuova offerta](media/azure-stack-delegated-provider/image5.png)

1. Assegnare un nome per l'offerta. Questo articolo viene usato **ResellerOffer** come esempio. Selezionare l'offerta delegata su cui basare, e quindi selezionare **Create**.

   ![Assegnare un nome](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >È importante comprendere che a differenza di un operatore di Azure Stack, un provider delegato non crea un'offerta da piani di base e i piani aggiuntivi. Provider delegati possono scegliere solo le offerte delegate ad essi, non possono apportare modifiche a tali offerte.

1. Rendere pubblico l'offerta selezionando **esplorare**e quindi **offre**. Selezionare l'offerta e quindi selezionare **Cambia stato**.

1. Il provider delegato espone queste offerte tramite i propri portale URL. Queste offerte sono visibili solo tramite il portale di delegato. Per trovare e modificare questo URL:

    a.  Selezionare **esplorare** > **altri servizi** > **sottoscrizioni**. Selezionare quindi la sottoscrizione del provider delegata. Ad esempio, **DPSubscription** > **proprietà**.

    b.  Copiare il portale di URL in un percorso distinto, ad esempio Blocco note.

    ![Selezionare la sottoscrizione del provider delegata](media/azure-stack-delegated-provider/dpportaluri.png)  

   È stata completata la creazione di un'offerta delegata come provider di delegato. Disconnettersi come provider di delegati e chiudere la finestra del browser in uso.

### <a name="sign-up-for-the-offer"></a>Effettuare l'iscrizione all'offerta

1. In una nuova finestra del browser accedere al portale di delegati URL a cui è stato salvato nel passaggio precedente. Accedere al portale come utente.

   >[!NOTE]
   >Le offerte Delegate non sono visibili a meno che non si usa il portale di delegato.

1. Nel dashboard, selezionare **ottenere una sottoscrizione**. Si noterà che solo le offerte delegate che sono state create dal provider del delegato vengano presentate all'utente.

   ![Visualizzare e selezionare le offerte](media/azure-stack-delegated-provider/image8.png)

Il processo di delega di un'offerta è stato completato. A questo punto un utente può iscriversi a questa offerta tramite il recupero di una sottoscrizione per tale.

## <a name="multiple-tier-delegation"></a>Delega a più livelli

La delega a più livelli consente un provider di delegato delegare l'offerta da altre entità. Ad esempio, per creare il rivenditore più approfondito dei canali where:

* Il provider che gestisce l'Azure Stack delega un'offerta a un server di distribuzione.
* I delegati di server di distribuzione per un rivenditore.

Per creare più livelli di delega offerta, il provider delegato delega dell'offerta sul provider successivo. Il processo è lo stesso per il provider delegato a quella necessaria per l'operatore di Azure Stack. Per altre informazioni, vedere [operatore di Azure Stack consente di creare l'offerta delegata](#cloud-operator-creates-the-delegated-offer).

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)