---
title: Delegare offerte in Azure Stack | Microsoft Docs
description: Informazioni su come inserire altri utenti responsabile della creazione di offerte e iscrizione gli utenti per l'utente.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 1efe64d2057a4dccc0d82a8a99bfbf3eaa719521
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159112"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegare offerte in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

L'operatore di Azure Stack, è spesso necessario inserire altri utenti responsabile di effettuare l'iscrizione agli utenti e la creazione di sottoscrizioni. Ad esempio, se sei un provider di servizi, è possibile i rivenditori a effettuare l'iscrizione ai clienti e gestirli per tuo conto. In alternativa, se si fa parte di un gruppo IT centrale in un'organizzazione, è possibile delegare l'iscrizione dell'utente per il personale IT.

Delega rende più semplice accedere e gestire più utenti, è possibile eseguire da soli, come illustrato nella figura seguente:

![Livelli di delega](media/azure-stack-delegated-provider/image1.png)

Con la delega, il provider delegato gestisce un'offerta (l'offerta delegata) e i clienti finali ottenere sottoscrizioni per tale offerta senza coinvolgere l'amministratore di sistema.

## <a name="understand-delegation-roles-and-steps"></a>Comprendere i passaggi e i ruoli di delega

### <a name="delegation-roles"></a>Ruoli di delega

I ruoli seguenti fanno parte della delega:

* Il *operatore di Azure Stack* gestisce l'infrastruttura di Azure Stack e crea un modello di offerta. L'operatore di delega ad altri utenti di fornire offerte ai tenant.

* Gli operatori di Azure Stack delegati sono utenti con *proprietario* o *collaboratore* chiamato diritti nelle sottoscrizioni *provider delegata*. Appartengono ad altre organizzazioni, ad esempio altri tenant Azure Active Directory (Azure AD).

* *Gli utenti* effettuare l'iscrizione per le offerte e usarli per la gestione dei carichi di lavoro, creazione di macchine virtuali, l'archiviazione dei dati e così via.

### <a name="delegation-steps"></a>Passaggi di delega

Esistono due passaggi di base per la configurazione di delega:

1. **Creare una sottoscrizione del provider delegata**: Eseguire la sottoscrizione all'utente di un'offerta che contiene solo il servizio di abbonamenti. Gli utenti che sottoscrivono questa offerta è quindi possono estendere le offerte delegate ad altri utenti mediante la firma per le offerte.

2. **Delegare un'offerta per il provider delegato**: Questa offerta consente al provider delegato per creare le sottoscrizioni oppure per estendere l'offerta agli utenti. Il provider delegato può ora richiedere l'offerta e offrirlo ad altri utenti.

Nella figura seguente vengono illustrati i passaggi per configurare la delega:

![Creare il provider delegato e consentirne l'accesso degli utenti](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>Requisiti del provider delegata

Per agire come un provider delegata, un utente stabilisce una relazione con il provider principale mediante la creazione di una sottoscrizione. Questa sottoscrizione identifica il provider delegato come avente il diritto per presentare le offerte delegate per conto di principale provider.

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
       Questo piano deve includere solo il servizio di sottoscrizione. Questo articolo usa un piano denominato **PlanForDelegation** come esempio.

   b.  [Creare un'offerta](azure-stack-create-offer.md) basato su questo piano. Questo articolo si usa un'offerta denominata **OfferToDP** come esempio.

   c.  Aggiungere il provider delegato come sottoscrittore per questa offerta selezionando **abbonamenti**, quindi **Add**, quindi **nuova sottoscrizione di Tenant**.

   ![Aggiungere il provider delegato come sottoscrittore](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Come con tutte le offerte di Azure Stack, è possibile scegliere di rendere l'offerta pubblici e ciò consente agli utenti iscriversi a, o mantenendola private e consentire l'operatore di Azure Stack di gestire l'iscrizione. I provider delegati sono in genere un piccolo gruppo. Si vuole controllare chi è ammesso, in modo da mantenere privato questa offerta ha senso nella maggior parte dei casi.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operatore di Azure Stack consente di creare l'offerta delegata

Il passaggio successivo consiste nel creare il piano e offerta che si desidera delegare e che verrà usato dagli utenti. È consigliabile definire questa offerta, esattamente come si desidera che gli utenti a vederla, perché il provider delegato non è possibile modificare i piani e le quote che sono incluse.

1. Un operatore, Azure Stack [creare un piano](azure-stack-create-plan.md) e [un'offerta](azure-stack-create-offer.md) in base al piano. Questo articolo si usa un'offerta denominata **DelegatedOffer** come esempio.

   > [!NOTE]
   > Questa offerta non deve essere pubblico, ma è possibile renderlo pubblico. Tuttavia, nella maggior parte dei casi solo necessario provider delegati per avere accesso all'offerta. Dopo la delega di un'offerta privata, come descritto nei passaggi seguenti, il provider delegato possa accedervi.

2. Delegare l'offerta. Passare a **DelegatedOffer**. Sotto **le impostazioni**, selezionare **provider delegata**, quindi selezionare **Add**.

3. Selezionare la sottoscrizione per il provider delegato nell'elenco a discesa e quindi selezionare **delegato**.

   ![Aggiungere un provider delegato](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Provider delegata consente di personalizzare l'offerta

Accedere al portale per gli utenti come provider di delegato e quindi creare una nuova offerta tramite l'offerta delegata come modello.

1. Selezionare **+ crea una risorsa**, quindi **Tenant offerte e piani**, quindi selezionare **offrono**.

    ![Creare una nuova offerta](media/azure-stack-delegated-provider/image5.png)

2. Assegnare un nome per l'offerta. Questo esempio viene usato **ResellerOffer**. Selezionare l'offerta delegata su cui basare, e quindi selezionare **Create**.

   ![Assegnare un nome](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >È importante comprendere che i provider delegati è possono selezionare solo le offerte che vengono delegate a essi. Non possono apportare modifiche a tali offerte. Solo un operatore di Azure Stack è possibile modificare queste offerte, ad esempio, modificare le quote e piani. Un provider delegata non ne crea un'offerta da piani di base e i piani aggiuntivi.

3. Il provider delegato può rendere queste offerte pubblici tramite i propri portale URL. Per rendere pubblico l'offerta, selezionare **esplorare**e quindi **offre**. Selezionare l'offerta e quindi selezionare **Cambia stato**.

4. Le offerte delegate pubbliche ora sono visibili solo tramite il portale di delegato. Per trovare e modificare questo URL:

    a.  Selezionare **esplorare**, quindi **tutti i servizi**, quindi nel **generali** categoria, seleziona **sottoscrizioni**. Selezionare la sottoscrizione del provider delegata; ad esempio, **DPSubscription**, quindi **proprietà**.

    b.  Copiare il portale di URL in un percorso distinto, ad esempio Blocco note.

    ![Selezionare la sottoscrizione del Provider delegata](media/azure-stack-delegated-provider/dpportaluri.png)  

   È stata completata la creazione di un'offerta delegata come provider di delegato. Disconnettersi come provider di delegati e chiudere la finestra del browser.

### <a name="sign-up-for-the-offer"></a>Effettuare l'iscrizione all'offerta

1. In una nuova finestra del browser accedere al portale di delegati URL a cui è stato salvato nel passaggio precedente. Accedere al portale come utente.

   >[!NOTE]
   >Le offerte Delegate non sono visibili a meno che non si usa il portale di delegato.

1. Nel dashboard, selezionare **ottenere una sottoscrizione**. Si noterà che solo le offerte delegate che sono state create dal provider del delegato vengano presentate all'utente.

   ![Visualizzare e selezionare le offerte](media/azure-stack-delegated-provider/image8.png)

Il processo di delega di un'offerta è stato completato. A questo punto un utente può iscriversi a questa offerta tramite il recupero di una sottoscrizione a esso.

## <a name="move-subscriptions-between-delegated-providers"></a>Spostare sottoscrizioni tra i provider delegati

Se necessario, una sottoscrizione può essere spostata tra le sottoscrizioni nuove o esistenti del provider delegata che appartengono allo stesso tenant di directory. Questa operazione viene eseguita usando il cmdlet di PowerShell [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin).

Il valore è utile se:

* Caricare un nuovo membro del team che sarà eseguite nel ruolo del provider delegata e si da assegnare a questo sottoscrizioni utente membro del team creati in precedenza nella sottoscrizione del provider predefinito.
* Si hanno più sottoscrizioni di provider delegati dello stesso tenant di directory (Azure Active Directory) ed è necessario spostare le sottoscrizioni utente tra di essi. Questo scenario potrebbe essere un caso in cui un membro del team si sposta tra i team e la sottoscrizione a pagamento deve essere allocata al nuovo team.

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)