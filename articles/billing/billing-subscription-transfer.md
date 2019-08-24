---
title: Trasferire la proprietà di fatturazione della sottoscrizione di Azure a un altro account | Microsoft Docs
description: Viene descritto come trasferire la proprietà di fatturazione di una sottoscrizione di Azure a un altro account e alcune domande frequenti sul processo
keywords: trasferire la sottoscrizione di Azure, la sottoscrizione di Azure Transfer, spostare la sottoscrizione di Azure a un altro account, il proprietario della sottoscrizione di Azure Change, trasferire la sottoscrizione di Azure a un altro account, Azure Transfer Billing
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012527"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Trasferire la proprietà di fatturazione di una sottoscrizione di Azure a un altro account

Potrebbe essere necessario trasferire la proprietà di fatturazione della sottoscrizione di Azure se si lascia l'organizzazione o si vuole che la sottoscrizione venga addebitata a un altro account. Il trasferimento della proprietà della fatturazione a un altro account fornisce agli amministratori del nuovo account l'autorizzazione per eseguire attività di fatturazione come il metodo di pagamento delle modifiche, visualizzare gli addebiti e annullare la sottoscrizione.

Se si vuole proteggere la proprietà della fatturazione, ma modificare il tipo di sottoscrizione, vedere [passare alla sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md). Se si vuole controllare gli utenti che possono gestire le risorse nella sottoscrizione, vedere [ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se si è un cliente Enterprise Agreement (EA), gli amministratori dell'organizzazione possono trasferire la proprietà di fatturazione delle sottoscrizioni tra gli account. Per altre informazioni, vedere [trasferire la proprietà di fatturazione delle sottoscrizioni Enterprise Agreement (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Trasferire la proprietà di fatturazione di una sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell'account di fatturazione con la sottoscrizione che si desidera trasferire. Per scoprire se si è un amministratore, vedere [domande frequenti](#faq).

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selezionare **sottoscrizioni** nel riquadro a sinistra. A seconda dell'accesso, potrebbe essere necessario selezionare un ambito di fatturazione e quindi selezionare le **sottoscrizioni** o le sottoscrizioni di **Azure**.

1. Selezionare **trasferire la proprietà di fatturazione** per la sottoscrizione che si desidera trasferire. 

   ![Selezionare la sottoscrizione da trasferire](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Immettere l'indirizzo di posta elettronica di un utente che è un amministratore di fatturazione dell'account che sarà il nuovo proprietario per la sottoscrizione.

1. Se si trasferisce la sottoscrizione a un account in un altro tenant Azure AD, selezionare se si vuole spostare la sottoscrizione nel tenant del nuovo account. Per altre informazioni, vedere [trasferimento della sottoscrizione a un account in un altro tenant Azure ad](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Se si sceglie di spostare la sottoscrizione nel tenant Azure AD del nuovo account, tutte le assegnazioni di controllo degli accessi in [base al ruolo (RBAC)](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione vengono rimosse definitivamente. Solo l'utente nel nuovo account che accetta la richiesta di trasferimento avrà accesso per gestire le risorse nella sottoscrizione. Per altre informazioni, vedere [trasferimento della sottoscrizione a un utente in un altro tenant Azure ad](../active-directory/managed-identities-azure-resources/known-issues.md). In alternativa, è possibile deselezionare la casella per la sottoscrizione Azure AD tenant per trasferire la proprietà della fatturazione senza spostare la sottoscrizione nel tenant del nuovo account. In tal caso, verranno mantenute le autorizzazioni RBAC esistenti per gestire le risorse di Azure.
  
    ![Pagina Invia trasferimento](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selezionare **Invia richiesta di trasferimento**.

1. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.

   ![Messaggio di posta elettronica di trasferimento della sottoscrizione inviato al destinatario](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni. L'utente deve selezionare un metodo di pagamento che verrà usato per pagare la sottoscrizione. Inoltre, se l'utente non dispone di un account Azure, dovrebbe iscriversi per ottenere un nuovo account. 

   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Operazione completata La sottoscrizione è stata trasferita.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Trasferimento della sottoscrizione a un account in un altro tenant Azure AD

Un tenant di Azure Active Directory (AD) viene creato automaticamente quando si esegue l'iscrizione ad Azure. Il tenant rappresenta l'account. Il tenant viene usato per gestire l'accesso alle sottoscrizioni e alle risorse.

Quando si crea una nuova sottoscrizione, questa viene ospitata nel tenant Azure AD dell'account. Se si vuole fornire ad altri utenti l'accesso alla sottoscrizione o alle relative risorse, è necessario invitare gli utenti a partecipare al tenant. Questo consente di controllare l'accesso alle sottoscrizioni e alle risorse.

Quando si trasferisce la proprietà della fatturazione della sottoscrizione a un account in un altro tenant di Azure AD, è possibile spostare la sottoscrizione nel tenant del nuovo account. In tal caso, tutti gli utenti, i gruppi o le entità servizio che hanno eseguito [l'accesso basato sui ruoli (RBAC)](../role-based-access-control/role-assignments-portal.md) per gestire le sottoscrizioni e le relative risorse perderanno l'accesso. Solo l'utente nel nuovo account che accetta la richiesta di trasferimento avrà accesso per gestire le risorse. Per consentire l'accesso agli utenti che originariamente avevano accesso, il nuovo proprietario avrebbe dovuto [aggiungere manualmente questi utenti alla sottoscrizione](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Trasferimento di sottoscrizioni di sviluppo/test di Visual Studio, MPN e con pagamento in base al consumo

Le sottoscrizioni di Visual Studio e Microsoft Partner Network hanno un credito Azure ricorrenti mensile associato. Quando si trasferiscono queste sottoscrizioni, il credito non è disponibile nell'account di fatturazione di destinazione. La sottoscrizione usa il credito nell'account di fatturazione di destinazione. Ad esempio, se Bob trasferisce una sottoscrizione di Visual Studio Enterprise all'account di Jane il 9 settembre e Jane accetta il trasferimento. Al termine del trasferimento, la sottoscrizione inizia a usare il credito nell'account di Jane. Il credito viene reimpostato ogni mese il 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Trasferire la proprietà di fatturazione delle sottoscrizioni Enterprise Agreement (EA)

L'amministratore dell'organizzazione può trasferire la proprietà delle sottoscrizioni tra gli account all'interno di una registrazione. Per altre informazioni, vedere [trasferire la proprietà dell'account](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) nel portale EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Passaggi successivi dopo l'accettazione della proprietà della fatturazione

Se è stata accettata la proprietà di fatturazione di una sottoscrizione di Azure, è consigliabile esaminare i passaggi seguenti:

1. Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e gli altri ruoli RBAC. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](billing-add-change-azure-subscription-administrator.md) e [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
1. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   1. Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
   1. Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.
1. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [collegare un ID partner agli account Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Il trasferimento di sottoscrizioni nella portale di Azure è disponibile per i tipi di sottoscrizione elencati di seguito. Il trasferimento attualmente non è supportato per le sottoscrizioni di [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) o di [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) . Per aggirare questa limitazione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md). Per trasferire altre sottoscrizioni, ad esempio [Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) o piani di supporto, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contratto Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Tramite il portale EA](#EA).

\*\*Supportato solo per gli account creati durante l'iscrizione nel sito Web di Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Domande frequenti (FAQ) per i mittenti

Queste domande frequenti si applicano agli utenti che trasferiscono la proprietà della fatturazione di una sottoscrizione di Azure a un altro account.

### <a name="whoisaa"></a>Chi è l'amministratore della fatturazione di un account?

Un amministratore di fatturazione è una persona che dispone dell'autorizzazione per gestire la fatturazione per un account. Sono autorizzati ad accedere alla fatturazione sul [portale di Azure](https://portal.azure.com) ed eseguono diverse attività di fatturazione come creare sottoscrizioni, visualizzare e pagare fatture o aggiornare i metodi di pagamento.

Per identificare gli account di cui si è un amministratore di fatturazione, attenersi alla procedura seguente:

1. Visitare la [pagina Gestione costi e fatturazione in portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selezionare **tutti gli ambiti di fatturazione** nel riquadro a sinistra. 
1. Nella pagina sottoscrizioni sono elencate tutte le sottoscrizioni di cui si è un amministratore della fatturazione.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Vengono trasferite tutte le risorse? Anche gruppi di risorse, macchine virtuali, dischi e altri servizi in esecuzione?

Tutte le risorse, ad esempio macchine virtuali, dischi e siti Web, vengono trasferite al nuovo account. Tuttavia, se si trasferisce la sottoscrizione a un account in un altro tenant Azure AD, i [ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) e le assegnazioni di controllo degli accessi in [base al ruolo (RBAC)](../role-based-access-control/role-assignments-portal.md) nella sottoscrizione [non vengono trasferiti](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Inoltre, le [registrazioni di app](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e altri servizi specifici del tenant non vengono trasferiti insieme alla sottoscrizione.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>È possibile trasferire la proprietà a un account in un altro paese?
Sfortunatamente, i trasferimenti tra paesi non possono essere eseguiti nel portale di Azure. Per trasferire la sottoscrizione tra paesi, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Sono un amministratore di due account. È possibile trasferire una sottoscrizione da uno dei miei account a un altro?
Sì, è possibile trasferire la sottoscrizione tra gli account. Gli account sono considerati concettualmente come account di due utenti diversi, quindi è possibile usare i passaggi precedenti per trasferire le sottoscrizioni tra gli account.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Il trasferimento di una sottoscrizione comporta tempi di inattività del servizio?

Se si trasferisce una sottoscrizione a un account nello stesso tenant di Azure AD, non ci sono effetti sulle risorse in esecuzione nella sottoscrizione. Tuttavia, se si trasferisce la sottoscrizione a un account in un altro tenant e si decide di spostare la sottoscrizione al tenant, tutti gli utenti, i gruppi e le entità servizio con [accesso basato sui ruoli (RBAC)](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione perderanno l'accesso . Questo può causare tempi di inattività del servizio.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Gli utenti con un nuovo account hanno accesso alla cronologia di utilizzo e di fatturazione?

Le uniche informazioni disponibili per gli utenti in un nuovo account sono il costo dell'ultimo mese per la sottoscrizione. Il resto della cronologia di utilizzo e di fatturazione non viene trasferito con la sottoscrizione

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?

Se non è possibile trasferire la proprietà di una sottoscrizione, eseguire la migrazione manuale delle risorse. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se si trasferisce una sottoscrizione di Visual Studio o di Microsoft Partner Network, il credito viene trasmesso con la sottoscrizione nel nuovo account?

No, il credito non è disponibile nel nuovo account. L'utente che accetta la richiesta di trasferimento deve avere una licenza di Visual Studio per accettare la richiesta di trasferimento. La sottoscrizione usa il credito di Visual Studio disponibile nell'account dell'utente. Per altre informazioni, vedere [trasferimento di Visual Studio, Microsoft Partner Network (MPN) e sottoscrizioni di sviluppo/test con pagamento in base](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)al consumo.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Domande frequenti (FAQ) per i destinatari

Queste domande frequenti si applicano agli utenti che accettano la proprietà di fatturazione di una sottoscrizione di Azure da un altro account.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se si rileva la proprietà della fatturazione di una sottoscrizione da un altro account, gli utenti con tale account continueranno ad avere accesso alle risorse personali?

Sì. Tuttavia, se l'account si trova in un tenant di Azure AD diverso dal tenant della sottoscrizione e l'utente che ha inviato la richiesta di trasferimento sposta la sottoscrizione al tenant dell'account, tutti i [ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) e il controllo degli accessi in base al [ruolo (RBAC) ](../role-based-access-control/role-assignments-portal.md)le assegnazioni vengono rimosse. Per visualizzare gli utenti che dispongono dell'accesso [basato sui ruoli (RBAC)](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione, attenersi alla procedura seguente:

1. Visitare la [pagina relativa alla sottoscrizione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione che si vuole controllare, quindi selezionare **controllo di accesso (IAM)** dal riquadro a sinistra.
1. Selezionare **assegnazioni di ruolo** nella parte superiore della pagina. Nella pagina assegnazioni di ruolo sono elencati tutti gli utenti che dispongono di accesso RBAC alla sottoscrizione.

Anche se le assegnazioni di controllo degli accessi in [base al ruolo](../role-based-access-control/role-assignments-portal.md) vengono rimosse durante il trasferimento, gli utenti nell'account proprietario originale potrebbero avere ancora accesso alla sottoscrizione tramite alcuni meccanismi di sicurezza, tra cui:

* Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md).
* Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
* Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse, è consigliabile aggiornare i segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Scegliere **Tutte le risorse** dal menu Hub.
  3. Selezionare la risorsa.
  4. Nella pagina delle risorse fare clic su **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se si rileva la proprietà della fatturazione di una sottoscrizione nel corso del ciclo di fatturazione, è necessario pagare per l'intero ciclo di fatturazione?

L'account è responsabile del pagamento di eventuali utilizzi segnalati a partire dal momento del trasferimento. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. L'utilizzo è incluso nella fattura del proprio account.

### <a name="can-i-use-a-different-payment-method"></a>Posso usare un metodo di pagamento diverso?

Sì. Durante l'accettazione della richiesta di trasferimento, è possibile selezionare un metodo di pagamento esistente collegato all'account o aggiungere un nuovo metodo di pagamento.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a id="no-button"></a> Perché non viene visualizzato il pulsante Trasferisci sottoscrizione?

Il trasferimento della sottoscrizione self-service non è disponibile per l'account di fatturazione. Attualmente, non è supportato il trasferimento della proprietà di fatturazione delle sottoscrizioni in account Enterprise Agreement (EA) nel portale di Azure. Inoltre, gli account dei contratti con i clienti Microsoft creati quando si lavora con un rappresentante Microsoft non supportano il trasferimento della proprietà della fatturazione. 

### <a id="no-button"></a>Perché il tipo di sottoscrizione non supporta il trasferimento? 

Non tutti i tipi di sottoscrizioni supportano il trasferimento della proprietà della fatturazione. Per visualizzare l'elenco dei tipi di sottoscrizione che supportano i trasferimenti, vedere [tipi di sottoscrizione supportati](#supported-subscription-types) .

### <a id="no-button"></a>Perché si riceve un errore di accesso negato quando si tenta di trasferire la proprietà della fatturazione di una sottoscrizione? 

Questo errore viene visualizzato se si sta provando a trasferire una sottoscrizione del piano Microsoft Azure e non si dispone dell'autorizzazione necessaria. Per trasferire una sottoscrizione del piano di Microsoft Azure, è necessario essere un proprietario o un collaboratore nella sezione relativa alla fattura a cui viene addebitata la sottoscrizione. Per ulteriori informazioni, vedere [la sezione gestire le sottoscrizioni per la fatturazione](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e gli altri ruoli RBAC. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](billing-add-change-azure-subscription-administrator.md) e [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
