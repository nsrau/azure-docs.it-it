---
title: "Trasferire la proprietà della sottoscrizione di Azure a un altro account | Microsoft Docs"
description: Questo articolo descrive come trasferire una sottoscrizione di Azure a un altro utente e contiene alcune domande frequenti (FAQ) sul processo
keywords: trasferire la sottoscrizione di Azure, trasferimento della sottoscrizione di Azure, spostare una sottoscrizione di Azure su account, cambiare il proprietario della sottoscrizione di Azure, trasferire la sottoscrizione di Azure a un altro account
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/13/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f061197cbe9fd52594d9fb000d8f3bcbd2d5285
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Trasferimento della proprietà di una sottoscrizione di Azure a un altro account

Trasferire la sottoscrizione a un altro utente nel centro Account di modificare l'amministratore dell'Account e passare la proprietà di fatturazione di sottoscrizione. Per modificare la sottoscrizione passando a un'offerta diversa, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Attualmente non è supportato il trasferimento delle sottoscrizioni per la versione di valutazione gratuita o le sottoscrizioni [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Per aggirare questa limitazione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).

## <a name="transfer-ownership-of-an-azure-subscription"></a>Trasferimento della proprietà di una sottoscrizione di Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Accedi a [centro Account Azure](https://account.windowsazure.com/Subscriptions) come l'amministratore Account. Per scoprire chi è l'amministratore dell'Account della sottoscrizione, vedere [domande frequenti](#faq).

1. Selezionare la sottoscrizione da trasferire.

1. Verificare che la sottoscrizione sia idonea per il trasferimento in modalità self-service verificando **Offerta** e **ID offerta** in base all'[elenco delle offerte supportate](#supported).

   ![Verificare l'ID offerta della sottoscrizione nel Centro account](./media/billing-subscription-transfer/image0.png)
1. Fare clic su **Trasferisci sottoscrizione**.

   ![Scheda delle sottoscrizioni dell'account Azure](./media/billing-subscription-transfer/image1.png)
1. Specificare il destinatario.

   ![Finestra di dialogo Trasferisci sottoscrizione](./media/billing-subscription-transfer/image2.PNG)
1. Il destinatario riceve automaticamente un messaggio e-mail con un link per l'accettazione.

   ![Messaggio di posta elettronica di trasferimento della sottoscrizione al destinatario](./media/billing-subscription-transfer/image3.png)
1. Il destinatario deve fare clic sul link e seguire le istruzioni, inclusa l'immissione delle informazioni sul pagamento.

   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image4.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image5.png)
1. Completamento della procedura La sottoscrizione è stata trasferita.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Trasferire la proprietà di una sottoscrizione per i clienti con contratto Enterprise (EA, Enterprise Agreement)

L'amministratore Enterprise può trasferire la proprietà delle sottoscrizioni nell'ambito di una registrazione. Per iniziare, vedere [Transfer Account Ownership](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) (Trasferire la proprietà account) nel portale EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Passaggi successivi all'accettazione della proprietà di una sottoscrizione

1. Sono ora l'amministratore Account. Rivedere e aggiornare gli altri ruoli RBAC l'amministratore del servizio e coamministratori. Per ulteriori informazioni, vedere [aggiungere o modificare ruoli di amministratore di Azure che gestiscono la sottoscrizione o servizi](billing-add-change-azure-subscription-administrator.md).
1. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   1. Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Create and upload a management certificate for Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
   1. Credenziali di accesso remoto per servizi quali macchine virtuali di Azure. 
1. [Aggiornare gli avvisi di fatturazione per questa sottoscrizione](billing-set-up-alerts.md) tramite il [Centro account di Azure](https://account.windowsazure.com/Subscriptions). 
1. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [portale di Azure](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Attività supportate:

Il trasferimento della sottoscrizione in modalità self-service è disponibile per le offerte o i tipi di sottoscrizione elencati nella tabella seguente. Per il trasferimento di altre sottoscrizioni, ad esempio [Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) o piani di supporto, [contattare il supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nome offerta                                                                             | Numero offerta |
|----------------------------------------------------------------------------------------|--------------|
| [Contratto Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [Tramite portale EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

### <a name="whoisaa"></a>Chi è l'amministratore dell'Account della sottoscrizione?

L'amministratore account è la persona che ha eseguito l'iscrizione o ha acquistato la sottoscrizione di Azure. È autorizzato ad accedere al [Centro account](https://account.azure.com/Subscriptions) ed eseguire diverse attività di gestione, ad esempio creare e annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione oppure modificare l'amministratore del servizio. Se non si conosce l'amministratore account per una sottoscrizione, seguire questa procedura per individuarlo.

1. Accedere alla [pagina delle sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Vengono trasferite tutte le risorse? Anche gruppi di risorse, macchine virtuali, dischi e altri servizi in esecuzione?

Tutte le risorse come le macchine virtuali, dischi e trasferimento di siti Web per il nuovo proprietario. I [ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) e i criteri di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) eventualmente configurati, invece, non vengono trasferiti tra directory diverse. Inoltre, [registrazioni di app](../active-directory//develop/active-directory-integrating-applications.md) e altri servizi specifici del tenant non trasferire lungo.

### <a id="no-button"></a> Perché non viene visualizzato il pulsante Trasferisci sottoscrizione?

Sfortunatamente, il trasferimento della sottoscrizione in modalità self-service non è disponibile per l'offerta o il paese specifico. Per trasferire la sottoscrizione, [contattare il supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Il trasferimento di una sottoscrizione comporta tempi di inattività del servizio?

Il trasferimento non incide in alcun modo sul servizio. Viene annullata la sottoscrizione dell'attuale amministratore account e ne viene creata una nuova per l'account del destinatario, associando però i servizi di Azure sottostanti alla nuova sottoscrizione. L'ID sottoscrizione rimane invariato.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Come è possibile usare questo processo per cambiare la directory della sottoscrizione?

Viene creata una sottoscrizione di Azure nella directory a cui appartiene l'amministratore account. Per modificare la directory, trasferire la sottoscrizione a un account utente nella directory di destinazione. Quando tale utente avrà completato i passaggi per accettare il trasferimento, la sottoscrizione passa automaticamente alla directory di destinazione.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Se si rileva la proprietà della fatturazione di una sottoscrizione da un'altra organizzazione, quest'ultima continua ad avere accesso alle risorse del nuovo proprietario?

Se la sottoscrizione viene trasferita a un altro tenant, gli utenti associati al tenant precedente perdono l'accesso alla sottoscrizione. Anche se un utente non è più amministratore del servizio o coamministratore, potrebbe avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, tra cui:

* Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md).
* Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
* Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse, deve prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Scegliere **Tutte le risorse** dal menu Hub.
  3. Selezionare la risorsa.
  4. Nel pannello della risorsa fare clic su **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Se si trasferisce la sottoscrizione nel corso del ciclo di fatturazione, il destinatario dovrà pagare il ciclo intero?

Il mittente è responsabile del pagamento per qualsiasi utilizzo segnalato fino al momento in cui il trasferimento viene completato. Il destinatario è responsabile per l'utilizzo segnalato dal momento del trasferimento in avanti. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. In questo caso l'uso è incluso nella fattura del destinatario.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Il destinatario ha accesso alla cronologia di utilizzo e di fatturazione?

  Le sole informazioni disponibili per il destinatario riguardano l'importo dell'ultima fattura oppure, se la sottoscrizione è stata trasferita prima dell'emissione della prima fattura, il saldo attuale. Il resto della cronologia di fatturazione e utilizzo non viene trasferito con la sottoscrizione.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>L'offerta può essere modificata durante un trasferimento?

L'offerta deve rimanere invariata. Per modificare l'offerta, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>È possibile trasferire una sottoscrizione a un account utente in un altro paese?

No, il trasferimento di una sottoscrizione a un account utente in un altro paese non è supportato. L'account utente del destinatario deve essere nello stesso paese.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Il destinatario può usare un metodo di pagamento diverso?

Sì. Tuttavia, la cronologia di fatturazione della sottoscrizione verrà suddivisa tra i due account.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Il metodo di pagamento è in qualche modo interessato dal trasferimento di una sottoscrizione di Azure?

Per accettare il trasferimento di una sottoscrizione, è necessario fornire una carta di credito o un metodo di pagamento analogo per la sottoscrizione. Ad esempio, se Bob trasferisce una sottoscrizione a Jane e Jane accetta il trasferimento, Jane deve specificare anche il metodo di pagamento usato per la sottoscrizione. Al termine del trasferimento, la fattura per la sottoscrizione viene fatta a Jane, non a Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?

Se non è possibile trasferire la proprietà di una sottoscrizione, eseguire la migrazione manuale delle risorse. Vedere [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Spostare le risorse in un nuovo gruppo o sottoscrizione).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.