---
title: Trasferire la proprietà della fatturazione della sottoscrizione di Azure a un altro account | Microsoft Docs
description: Viene descritto come trasferire la proprietà della fatturazione della sottoscrizione di Azure a un altro account e alcune domande frequenti (FAQ) sul processo
keywords: trasferire la sottoscrizione di trasferimento di sottoscrizione di azure, azure, spostare una sottoscrizione di azure a un altro proprietario della sottoscrizione cambia account, azure, trasferire la sottoscrizione di azure a un'altra trasferimento dell'account, azure billing
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
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657892"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Trasferire la proprietà della fatturazione della sottoscrizione di Azure a un altro account

È possibile trasferire la proprietà della fatturazione della sottoscrizione di Azure se si stanno lasciando l'organizzazione o si vuole usare la sottoscrizione per la fatturazione a un altro account. Trasferimento della proprietà della fatturazione a un altro account fornisce agli amministratori nel nuovo account l'autorizzazione per eseguire attività di fatturazione, ad esempio modificare il metodo di pagamento, visualizzare gli addebiti e annullare la sottoscrizione.

Se si desidera mantenere le proprietà della fatturazione, ma modifica il tipo di sottoscrizione, vedere [trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md). Se si desidera controllare quali utenti può gestire le risorse nella sottoscrizione, vedere [ruoli predefiniti per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se si è clienti di Enterprise Agreement (EA), gli amministratori dell'organizzazione possono trasferire la proprietà della fatturazione delle sottoscrizioni tra gli account. Per altre informazioni, vedere [trasferire la proprietà della fatturazione delle sottoscrizioni Enterprise Agreement (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Trasferire la proprietà della fatturazione della sottoscrizione di Azure

1. Accedi per il [portale di Azure](https://portal.azure.com) come amministratore dell'account di fatturazione con la sottoscrizione che si desidera trasferire. Per scoprire se sei un amministratore, vedere [domande frequenti](#faq).

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selezionare **sottoscrizioni** nel riquadro a sinistra. In base all'accesso, è necessario selezionare un ambito di fatturazione e quindi selezionare **abbonamenti** oppure **sottoscrizioni di Azure**.

1. Selezionare **trasferire la proprietà della fatturazione** per la sottoscrizione che si desidera trasferire. 

   ![Selezionare la sottoscrizione da trasferire](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Immettere l'indirizzo di posta elettronica di un utente che è un amministratore di fatturazione dell'account che sarà il nuovo proprietario per la sottoscrizione e quindi selezionare **richiesta di trasferimento della trasmissione**.

    > [!IMPORTANT]
    >
    > Se si trasferisce la proprietà della fatturazione della sottoscrizione a un account utente in Azure AD un altro tenant, tutti [controllo di accesso basato sui ruoli (RBAC)](../role-based-access-control/overview.md) assegnazioni per gestire le risorse nella sottoscrizione vengono rimosse definitivamente. Solo il nuovo proprietario sarà possibile accedere per gestire le risorse nella sottoscrizione. Per altre informazioni, vedere [il trasferimento della sottoscrizione a un utente in un altro tenant di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Invia pagina trasferimento](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. L'utente riceve un messaggio di posta elettronica con istruzioni per rivedere la richiesta di trasferimento.

   ![Messaggio di posta elettronica di trasferimento della sottoscrizione al destinatario](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni. L'utente dovrà selezionare un metodo di pagamento che verrà usato per pagare la sottoscrizione. Inoltre, se l'utente non dispone di un account Azure, dovranno essere effettuare l'iscrizione per un nuovo account. 

   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Operazione completata La sottoscrizione è stata trasferita.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Il trasferimento della sottoscrizione a un account in un altro tenant di Azure AD

Un tenant di Azure Active Directory (AD) viene creato automaticamente quando si effettua l'iscrizione ad Azure. Il tenant rappresenta l'account. Usare il tenant per gestire l'accesso alle tue risorse e sottoscrizioni.

Quando si crea una nuova sottoscrizione, è ospitato nel tenant di Azure AD dell'account. Se si desidera fornire che altri utenti di accedere alla sottoscrizione o le risorse, è necessario inviare un invito per il tenant. Consente di controllare l'accesso per le sottoscrizioni e risorse.

Quando si trasferisce la proprietà della fatturazione della sottoscrizione a un account in un altro tenant di Azure AD, la sottoscrizione viene spostata al tenant del nuovo account. Tutti gli utenti, gruppi o entità servizio che avevano [in base al ruolo (RBAC) di accesso](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione perdono l'accesso. Solo l'utente nel nuovo account che accetta la richiesta di trasferimento avrà accesso per gestire le risorse. Per fornire accesso agli utenti che conteneva in origine l'accesso, il nuovo proprietario doveva [aggiungere manualmente utenti alla sottoscrizione](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Trasferimento di Visual Studio, Microsoft Partner Network (MPN) e le sottoscrizioni a pagamento a consumo, sviluppo/Test

Le sottoscrizioni di Visual Studio e Microsoft Partner Network hanno ricorrenti crediti di Azure associati. Quando si trasferiscono queste sottoscrizioni, il tuo credito non è disponibile nell'account di fatturazione di destinazione. La sottoscrizione utilizza la carta di credito nell'account di fatturazione di destinazione. Ad esempio, se Bob trasferisce una sottoscrizione di Visual Studio Enterprise per l'account in data 9 settembre Jane e Jane accetta il trasferimento. Una volta completato il trasferimento, viene avviata la sottoscrizione tramite carta di credito nell'account di Jane. Il credito verrà reimpostate ogni mese il 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Trasferire la proprietà della fatturazione delle sottoscrizioni Enterprise Agreement (EA)

L'amministratore dell'organizzazione può trasferire la proprietà di sottoscrizioni tra gli account all'interno di una registrazione. Per altre informazioni, vedere [trasferire la proprietà dell'Account](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) nel portale EA.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Passaggi successivi dopo aver accettato la proprietà della fatturazione

Se si hanno accettato le proprietà della fatturazione della sottoscrizione di Azure, è consigliabile che consultare questi passaggi:

1. Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e gli altri ruoli RBAC. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](billing-add-change-azure-subscription-administrator.md) e [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
1. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   1. Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
   1. Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.
1. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [collega un ID partner agli account di Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Trasferimento della sottoscrizione nel portale di Azure è disponibile per i tipi di sottoscrizione elencati di seguito. Trasferimento non è attualmente supportato per [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) oppure [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) sottoscrizioni. Per aggirare questa limitazione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md). Per il trasferimento di altre sottoscrizioni, ad esempio [sponsorizzazione](https://azure.microsoft.com/offers/ms-azr-0036p/) o, piani di supporto [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

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

\* [Tramite il portale EA](#EA).

\*\* Supportato solo per gli account creati durante l'iscrizione sul sito Web di Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Domande frequenti (FAQ per i mittenti)

Queste domande frequenti si applicano agli utenti, che trasferiscono la proprietà della fatturazione della sottoscrizione di Azure a un altro account.

### <a name="whoisaa"></a> Chi è un amministratore di fatturazione di un account?

Un amministratore di fatturazione è una persona che dispone dell'autorizzazione per gestire la fatturazione per un account. È autorizzato ad accedere a fatturazione nel [portale di Azure](https://portal.azure.com) ed eseguire diverse attività di fatturazione, ad esempio creare le sottoscrizioni, visualizzazione e pagare le fatture o aggiornamento i metodi di pagamento.

Per identificare le sottoscrizioni per cui si è un amministratore di fatturazione, procedere come segue:

1. Visitare il [gestione costi + fatturazione pagina nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selezionare **sottoscrizioni** nel riquadro a sinistra. In base all'accesso, è necessario selezionare un ambito di fatturazione e quindi selezionare **abbonamenti** o **sottoscrizioni di Azure**
1. La pagina sottoscrizioni vengono elencate tutte le sottoscrizioni per cui si è un amministratore di fatturazione.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Vengono trasferite tutte le risorse? Anche gruppi di risorse, macchine virtuali, dischi e altri servizi in esecuzione?

Tutte le risorse, come le macchine virtuali, dischi e siti Web vengono trasferite al nuovo account. Tuttavia, se si trasferisce sottoscrizione a un account di Azure AD un altro tenant, qualsiasi [ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) e [controllo di accesso basato sui ruoli (RBAC)](../role-based-access-control/role-assignments-portal.md) assegnazioni della sottoscrizione [No trasferimento](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). È inoltre [registrazioni per l'app](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e altri servizi specifici del tenant non è possibile trasferire insieme alla sottoscrizione.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>È possibile trasferire la proprietà a un account in un altro paese?
Sfortunatamente, tra paese non è possibile utilizzare trasferimenti nel portale di Azure. Per trasferire la sottoscrizione in paesi [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Sono un amministratore in due account. È possibile trasferire una sottoscrizione da uno degli account a un altro?
Sì, è possibile trasferire sottoscrizioni tra gli account. Gli account a livello concettuale sono considerati gli account di due utenti diversi in modo che è possibile usare i passaggi precedenti per il trasferimento di sottoscrizioni tra gli account.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Il trasferimento di una sottoscrizione comporta tempi di inattività del servizio?

Se si trasferisce una sottoscrizione a un utente nello stesso tenant di Azure AD, non vi è alcun impatto per le risorse in esecuzione nella sottoscrizione.  Tuttavia, se si trasferisce la sottoscrizione a un utente in un altro tenant, tutti gli utenti, gruppi e le entità servizio che avevano [in base al ruolo (RBAC) di accesso](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione perdono l'accesso. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Il destinatario ha accesso alla cronologia di utilizzo e di fatturazione?

Le uniche informazioni disponibili per il destinatario sono il costo del mese precedente per la sottoscrizione. Tutta la cronologia di utilizzo e fatturazione non viene trasferito con la sottoscrizione

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?

Se non è possibile trasferire la proprietà di una sottoscrizione, eseguire la migrazione manuale delle risorse. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se si trasferisce una sottoscrizione di Visual Studio o Microsoft Partner Network, mia carta di credito riporta con la sottoscrizione nel nuovo account?

No, il tuo credito non è disponibile nel nuovo account. L'utente che accetta la richiesta di trasferimento deve avere una licenza di Visual Studio per accettare la richiesta di trasferimento. La sottoscrizione utilizza il credito di Visual Studio che è disponibile nell'account dell'utente. Per altre informazioni, vedere [abbonamenti il trasferimento di Visual Studio, Microsoft Partner Network (MPN) e sviluppo/Test di pagamento a consumo](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Domande frequenti (FAQ per i destinatari)

Queste domande frequenti si applicano agli utenti, che impiega oltre le proprietà della fatturazione della sottoscrizione di Azure da un altro account.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se si rileva la proprietà della fatturazione di una sottoscrizione da un altro account, gli utenti in quanto account continuano ad avere accesso alle risorse?

Se la sottoscrizione viene trasferita a un account in stesso tenant di Azure AD, tutti gli utenti, gruppi e le entità servizio che avevano [in base al ruolo (RBAC) di accesso](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione di mantengono l'accesso. Per visualizzare gli utenti che dispongono dell'accesso RBAC per la sottoscrizione, procedere come segue:

1. Visitare il [pagina di sottoscrizione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione che si desidera controllare e quindi selezionare **controllo di accesso (IAM)** nel riquadro a sinistra.
1. Selezionare **assegnazioni di ruolo** dalla parte superiore della pagina. La pagina di assegnazioni di ruolo sono elencati tutti gli utenti che hanno accesso RBAC per la sottoscrizione.

Se la sottoscrizione viene trasferita a un account in un altro tenant di Azure AD, tutti gli utenti, gruppi ed entità servizio che avevano [in base al ruolo (RBAC) di accesso](../role-based-access-control/overview.md) per gestire le risorse nella sottoscrizione perdono l'accesso. Tuttavia, anche se non hanno più accesso RBAC, potrebbe avere ancora accesso alla sottoscrizione tramite alcuni meccanismi di sicurezza, tra cui:

* Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md).
* Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
* Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare, l'accesso alle risorse, è necessario considerare l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Scegliere **Tutte le risorse** dal menu Hub.
  3. Selezionare la risorsa.
  4. Nella pagina delle risorse, fare clic su **impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se si rileva la le proprietà della fatturazione di una sottoscrizione nel corso del ciclo di fatturazione, è necessario pagare per l'intero ciclo di fatturazione?

L'account è responsabile del pagamento per qualsiasi utilizzo segnalato dal momento del trasferimento e versioni successive. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. L'utilizzo è incluso nella fattura del tuo account.

### <a name="can-i-use-a-different-payment-method"></a>È possibile usare un metodo di pagamento diverso?

Sì. Durante l'accettazione della richiesta di trasferimento, è possibile selezionare un metodo di pagamento esistente che è collegato al tuo account o aggiungere un nuovo metodo di pagamento.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a id="no-button"></a> Perché non viene visualizzato il pulsante Trasferisci sottoscrizione?

Sfortunatamente, il trasferimento di sottoscrizione self-service non è disponibile per l'account di fatturazione. Attualmente non Supportiamo trasferimento dell'esecuzione di proprietà della fatturazione dell'account di Enterprise Agreement (EA) nel portale di Azure. Inoltre, gli account Microsoft dal cliente contratto creati mentre si lavora in Microsoft sales non supportano trasferimento della proprietà di fatturazione delle sottoscrizioni. 

### <a id="no-button"></a> Non perché la sottoscrizione di tipo di trasferimento di supporto? 

Sfortunatamente, non tutti i tipi di sottoscrizioni supportano fatturazione trasferimento della proprietà. Per visualizzare l'elenco dei tipi di sottoscrizione che supporta i trasferimenti, vedere [supportati i tipi di sottoscrizione](#supported-subscription-types)

### <a id="no-button"></a> Perché ho ricevuto un errore di accesso negato quando si prova a trasferire la proprietà della fatturazione di una sottoscrizione? 

Verrà visualizzato questo errore se si sta trasferire una sottoscrizione al piano di Microsoft Azure e non si dispone dell'autorizzazione necessaria. Per trasferire una sottoscrizione al piano di Microsoft Azure, è necessario essere proprietario o collaboratore nella sezione della fattura in cui viene fatturata alla sottoscrizione. Per altre informazioni, vedere [gestire le sottoscrizioni per la sezione della fattura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e gli altri ruoli RBAC. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](billing-add-change-azure-subscription-administrator.md) e [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
