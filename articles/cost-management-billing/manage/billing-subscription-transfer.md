---
title: Trasferire la proprietà della fatturazione di una sottoscrizione di Azure
description: Questo articolo descrive come trasferire la proprietà della fatturazione di una sottoscrizione di Azure in un altro account e contiene alcune domande frequenti sul processo
keywords: trasferire la sottoscrizione di Azure, trasferimento della sottoscrizione di Azure, spostare una sottoscrizione di Azure in un altro account, cambiare il proprietario della sottoscrizione di Azure, trasferire la sottoscrizione di Azure in un altro account, trasferire la fatturazione di Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27ae816e3930ee0e5948c2daae40a0961a6cf474
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685035"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Trasferire la proprietà della fatturazione di una sottoscrizione di Azure in un altro account

Si potrebbe avere l'esigenza di trasferire la proprietà della fatturazione della sottoscrizione di Azure nel caso in cui si lasci l'organizzazione o se si vuole che la sottoscrizione venga addebitata a un altro account. Il trasferimento della proprietà della fatturazione a un altro account fornisce agli amministratori del nuovo account le autorizzazioni necessarie per le attività di fatturazione. Tali amministratori possono cambiare il metodo di pagamento, visualizzare gli addebiti e annullare la sottoscrizione.

Se si vuole mantenere la proprietà della fatturazione, ma cambiare il tipo di sottoscrizione, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](switch-azure-offer.md). Per controllare chi può accedere alle risorse della sottoscrizione, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Per i clienti con contratto Enterprise Agreement (EA), gli amministratori dell'organizzazione possono trasferire la proprietà della fatturazione delle sottoscrizioni tra gli account. Per altre informazioni, vedere [Trasferire la proprietà di fatturazione delle sottoscrizioni Enterprise Agreement (EA)](#EA).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Trasferire la proprietà della fatturazione di una sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell'account di fatturazione che include la sottoscrizione da trasferire. Per verificare se si è un amministratore, vedere le [domande frequenti](#faq).

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selezionare **Sottoscrizioni** nel riquadro sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un ambito di fatturazione e quindi **Sottoscrizioni** o **Sottoscrizioni di Azure**.

1. Selezionare **Trasferisci la proprietà della fatturazione** per la sottoscrizione da trasferire.

   ![Selezionare la sottoscrizione da trasferire](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Immettere l'indirizzo di posta elettronica di un utente con il ruolo di amministratore della fatturazione dell'account che diventerà il nuovo proprietario della sottoscrizione.

1. Se la sottoscrizione viene trasferita in un account in un altro tenant di Azure AD, scegliere se la si vuole spostare nel tenant del nuovo account. Per altre informazioni, vedere [Trasferimento della sottoscrizione in un account in un altro tenant di Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account)

    > [!IMPORTANT]
    >
    > Se si sceglie di spostare la sottoscrizione nel tenant di Azure AD del nuovo account, tutte le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per l'accesso alle risorse nella sottoscrizione vengono rimosse definitivamente. Solo l'utente del nuovo account che accetta la richiesta di trasferimento avrà accesso per la gestione delle risorse della sottoscrizione. Per altre informazioni, vedere la sezione successiva [Trasferire una sottoscrizione in un account di un altro tenant di Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account). In alternativa, è possibile deselezionare la casella **Tenant Azure AD della sottoscrizione** per trasferire la proprietà di fatturazione senza spostare la sottoscrizione nel tenant del nuovo account. In tal caso, le assegnazioni di ruolo di Azure esistenti per l'accesso alle risorse di Azure verranno mantenute.

    ![Pagina di invio della richiesta di trasferimento](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Selezionare **Invia la richiesta di trasferimento**.

1. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.

   ![Messaggio di posta elettronica sul trasferimento della sottoscrizione inviato al destinatario](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni. L'utente seleziona quindi un metodo di pagamento che verrà usato per pagare la sottoscrizione. Se l'utente non ha un account Azure, dovrà iscriversi per riceverne uno nuovo.

   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Operazione riuscita. La sottoscrizione è stata trasferita.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Trasferire una sottoscrizione in un account di un altro tenant di Azure AD

Quando ci si iscrive ad Azure, viene automaticamente creato un tenant di Azure Active Directory (AD). Il tenant rappresenta l'account. Viene usato per gestire l'accesso alle sottoscrizioni e alle risorse.

Le nuove sottoscrizioni create vengono ospitate nel tenant di Azure AD del proprio account. Se si vuole consentire ad altri utenti di accedere alla sottoscrizione o alle relative risorse, è necessario invitarli ad aggiungersi al tenant. In questo modo è possibile controllare l'accesso alle sottoscrizioni e alle risorse.

Se la proprietà della fatturazione della sottoscrizione viene trasferita in un account in un altro tenant di Azure AD, scegliere se la si vuole spostare nel tenant del nuovo account. In questo caso, tutti gli utenti, i gruppi o le entità servizio che avevano [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per la gestione delle sottoscrizioni e delle relative risorse perderanno l'accesso. Solo l'utente del nuovo account che accetta la richiesta di trasferimento avrà accesso per la gestione delle risorse. Il nuovo proprietario deve aggiungere manualmente questi utenti alla sottoscrizione per fornire l'accesso agli utenti che lo hanno perso. Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure a un'altra directory di Azure AD (anteprima)](../../role-based-access-control/transfer-subscription.md).


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Trasferire le sottoscrizioni di Visual Studio e Partner Network

Alle sottoscrizioni di Visual Studio e Microsoft Partner Network è associato un credito Azure ricorrente mensile. Quando si trasferiscono queste sottoscrizioni, il credito non è disponibile nell'account di fatturazione di destinazione. La sottoscrizione usa il credito nell'account di fatturazione di destinazione. Supponiamo ad esempio che Bob trasferisca una sottoscrizione di Visual Studio Enterprise all'account di Jane il 9 settembre e che Jane accetti il trasferimento. Al termine del trasferimento, la sottoscrizione inizia a usare il credito nell'account di Jane. Il credito viene reimpostato ogni nono giorno del mese.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>Trasferire la proprietà di fatturazione delle sottoscrizioni con contratto Enterprise

L'amministratore dell'organizzazione può trasferire la proprietà delle sottoscrizioni tra gli account all'interno di una registrazione. Per altre informazioni, vedere [Cambiare il proprietario dell'account](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner).

## <a name="next-steps-after-accepting-billing-ownership"></a>Passaggi successivi all'accettazione della proprietà della fatturazione

Se è stata accettata la proprietà di fatturazione di una sottoscrizione di Azure, è consigliabile rivedere i passaggi successivi seguenti:

1. Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e le assegnazioni di ruolo di Azure. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](add-change-subscription-administrator.md) e [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
1. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   1. Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).
   1. Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.
1. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Collegare un ID partner agli account Azure](link-partner-id.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Il trasferimento di sottoscrizioni nel portale di Azure è disponibile per i tipi di sottoscrizione elencati di seguito. Attualmente il trasferimento non è supportato per le sottoscrizioni della [versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) o [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/). Per aggirare questa limitazione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Per trasferire altre sottoscrizioni, ad esempio i piani di supporto, [contattare il supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

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

\* [Tramite EA Portal](#EA).

\*\* Supportato solo per gli account creati durante l'iscrizione nel sito Web di Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Domande frequenti per i mittenti

Queste domande frequenti si applicano agli utenti che trasferiscono la proprietà della fatturazione di una sottoscrizione di Azure in un altro account.

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> Chi è l'amministratore fatturazione di un account?

L'amministratore fatturazione è una persona autorizzata a gestire la fatturazione per un account. Ha l'autorizzazione per accedere alla fatturazione nel [portale di Azure](https://portal.azure.com) ed eseguire varie attività collegate, ad esempio creare sottoscrizioni, visualizzare e pagare fatture oppure aggiornare i metodi di pagamento.

Per identificare gli account di cui si è un amministratore fatturazione, procedere come segue:

1. Visitare la pagina [Gestione dei costi e fatturazione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selezionare **Tutti gli ambiti di fatturazione** nel riquadro sinistro.
1. Nella pagina delle sottoscrizioni sono elencate tutte le sottoscrizioni in cui si è un amministratore fatturazione.

Se non si conosce l'amministratore account per una sottoscrizione, seguire questa procedura per individuarlo.

1. Accedere alla [pagina delle sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Vengono trasferite tutte le risorse? Anche gruppi di risorse, macchine virtuali, dischi e altri servizi in esecuzione?

Tutte le risorse, come VM, dischi e siti Web, vengono trasferite al nuovo account. Tuttavia, se una sottoscrizione viene trasferita a un account in un altro tenant di Azure AD, i [ruoli di amministratore](add-change-subscription-administrator.md) e le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) della sottoscrizione [non vengono trasferiti](#transfer-a-subscription-to-another-azure-ad-tenant-account). Inoltre, le [registrazioni di app](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e gli altri servizi specifici del tenant non vengono trasferiti insieme alla sottoscrizione.

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>È possibile trasferire la proprietà in un account di un altro paese/area geografica?
Purtroppo i trasferimenti tra paesi/aree geografiche non possono essere eseguiti nel portale di Azure. Per trasferire la sottoscrizione in altri paesi/aree geografiche, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Gli amministratori di due account possono trasferire una sottoscrizione da un account all'altro?
Sì, è possibile trasferire la sottoscrizione tra gli account. Gli account vengono considerati concettualmente come account di due utenti diversi, quindi è possibile eseguire la procedura descritta sopra per trasferire le sottoscrizioni.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Il trasferimento di una sottoscrizione comporta tempi di inattività del servizio?

Il trasferimento di una sottoscrizione in un account nello stesso tenant di Azure AD non ha alcun impatto sulle risorse in esecuzione nella sottoscrizione. Le informazioni di contesto salvate in PowerShell non vengono tuttavia aggiornate, pertanto potrebbe essere necessario cancellarle o modificare le impostazioni. Se la sottoscrizione viene trasferita a un account in un altro tenant e si decide di spostarla nel tenant, tutti gli utenti, i gruppi e le entità servizio che avevano le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per l'accesso alle risorse della sottoscrizione perderanno l'accesso. Questo si potrebbe tradurre in tempi di inattività del servizio.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>Gli utenti del nuovo account possono accedere alla cronologia di fatturazione e utilizzo?

Le uniche informazioni disponibili per gli utenti in un nuovo account sono quelle relative al costo dell'ultimo mese per la sottoscrizione. Il resto della cronologia di fatturazione e utilizzo non viene trasferito con la sottoscrizione.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?

Se non è possibile trasferire la proprietà di una sottoscrizione, eseguire la migrazione manuale delle risorse. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se si trasferisce una sottoscrizione di Visual Studio o di Microsoft Partner Network, il credito viene riportato nella sottoscrizione del nuovo account?

No, il credito non è disponibile nel nuovo account. Per accettare la richiesta di trasferimento, l'utente deve avere una licenza di Visual Studio. La sottoscrizione usa il credito di Visual Studio disponibile nell'account dell'utente. Per altre informazioni, vedere [Trasferire le sottoscrizioni di Visual Studio e Partner Network](#transfer-visual-studio-and-partner-network-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Domande frequenti per i destinatari

Queste domande frequenti si applicano agli utenti che accettano la proprietà della fatturazione di una sottoscrizione di Azure da un altro account.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se si rileva la proprietà della fatturazione di una sottoscrizione da un altro account, gli utenti di quest'ultimo continuano ad avere accesso alle risorse del nuovo proprietario?

Sì. Tuttavia, i [ruoli di amministratore](add-change-subscription-administrator.md) e le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) potrebbero essere rimossi. La perdita dell'accesso si verifica quando l'account si trova in un tenant di Azure AD diverso dal tenant della sottoscrizione e l'utente che ha inviato la richiesta di trasferimento sposta la sottoscrizione nel tenant dell'account. Per visualizzare gli utenti che hanno le assegnazioni di ruolo di Azure per l'accesso alle risorse della sottoscrizione, seguire questa procedura:

1. Visitare la [pagina delle sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione da verificare e quindi **Controllo di accesso (IAM)** nel riquadro sinistro.
1. Selezionare **Assegnazioni di ruolo** nella parte superiore della pagina. La pagina Assegnazioni di ruolo contiene l'elenco di tutti gli utenti con accesso nella sottoscrizione.

Anche se le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) vengono rimosse durante il trasferimento, gli utenti nell'account proprietario originale potrebbero avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, tra cui:

* Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../../cloud-services/cloud-services-certs-create.md).
* Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).
* Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse, deve prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Scegliere **Tutte le risorse** dal menu Hub.
  3. Selezionare la risorsa.
  4. Nella pagina della risorsa selezionare **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se si trasferisce la proprietà della fatturazione nel corso del ciclo di fatturazione, sarà necessario pagare il ciclo intero?

L'account è responsabile del pagamento di eventuali utilizzi segnalati a partire dal momento del trasferimento. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. L'utilizzo è incluso nella fattura dell'account.

### <a name="can-i-use-a-different-payment-method"></a>È possibile usare un metodo di pagamento diverso?

Sì. Durante l'accettazione della richiesta di trasferimento, è possibile selezionare un metodo di pagamento esistente collegato all'account o aggiungerne uno nuovo.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Come è possibile trasferire la proprietà dell'account di sottoscrizione di Contratto Enterprise (EA) se il proprietario dell'account originale non fa più parte dell'organizzazione?

L'amministratore dell'organizzazione può aggiornare la proprietà dell'account per qualsiasi account anche dopo che il proprietario dell'account originale non fa più parte dell'organizzazione. Questa operazione può essere effettuata seguendo le istruzioni per il [trasferimento della proprietà dell'account per tutte le sottoscrizioni](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) in EA Portal.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a> Perché non viene visualizzato il pulsante Trasferisci sottoscrizione?

Il trasferimento della sottoscrizione in modalità self-service non è disponibile per questo account di fatturazione. Attualmente, nel portale di Azure non è supportato il trasferimento della proprietà della fatturazione delle sottoscrizioni negli account del contratto Enterprise Agreement (EA). Inoltre, gli account con contratto del cliente Microsoft creati in collaborazione con un rappresentante Microsoft non supportano il trasferimento della proprietà della fatturazione.

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> Perché un tipo di sottoscrizione non supporta il trasferimento?

Non tutti i tipi di sottoscrizione supportano il trasferimento della proprietà della fatturazione. Per visualizzare l'elenco dei tipi che li supportano, vedere [Tipi di sottoscrizione supportati](#supported-subscription-types).

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> Perché si riceve un errore di accesso negato quando si prova a trasferire la proprietà della fatturazione di una sottoscrizione?

Questo errore viene visualizzato se si prova a trasferire una sottoscrizione di un piano di Microsoft Azure e non si hanno le autorizzazioni necessarie. Per trasferire una sottoscrizione di un piano di Microsoft Azure, è necessario essere un proprietario o un collaboratore nella sezione della fattura in cui viene addebitata la sottoscrizione. Per altre informazioni, vedere la sezione [Gestire le sottoscrizioni per la sezione della fattura](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e le assegnazioni di ruolo di Azure. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](add-change-subscription-administrator.md) e [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
