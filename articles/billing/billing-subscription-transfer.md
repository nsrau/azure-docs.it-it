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
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: e64c0336578be2c1a1126f7459775f47d73f9d6e
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017

---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Trasferimento della proprietà di una sottoscrizione di Azure a un altro account

Il trasferimento di proprietà di una sottoscrizione a un altro utente è previsto per le sottoscrizioni con pagamento in base al consumo, Visual Studio, Action Pack o BizSpark nel Centro account. Il trasferimento dei servizi esterni di Azure è supportato anche per questi tipi di sottoscrizione. 

Potrebbe essere necessario trasferire la proprietà di una sottoscrizione di Azure se:

* È necessario trasferire la proprietà della fatturazione della propria sottoscrizione di Azure ad altri.
* Si vuole cambiare l'account usato per accedere ad Azure. È stato usato l'account Microsoft personale, ma si voleva invece usare quello aziendale o dell'istituto di istruzione.
* Si vuole spostare la sottoscrizione di Azure da una directory a un'altra.
* Azure e Office 365 sono in tenant diversi e si vogliono consolidare.

Per modificare la sottoscrizione passando a un'offerta diversa, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md). 

## <a name="transfer-ownership-of-an-azure-subscription"></a>Trasferimento della proprietà di una sottoscrizione di Azure
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Accedere a <https://account.windowsazure.com/Subscriptions>. È necessario essere amministratore account per eseguire un trasferimento di proprietà. Per individuare l'amministratore account della sottoscrizione, vedere [Domande frequenti](#faq).

2. Selezionare la sottoscrizione da trasferire.

3. Fare clic sull'opzione **Trasferisci sottoscrizione** . Se non viene visualizzato il pulsante, vedere [Domande frequenti](#no-button).

   ![Scheda delle sottoscrizioni dell'account Azure](./media/billing-subscription-transfer/image1.png)
4. Specificare il destinatario.

   ![Finestra di dialogo Trasferisci sottoscrizione](./media/billing-subscription-transfer/image2.PNG)
5. Il destinatario riceve automaticamente un messaggio e-mail con un link per l'accettazione.

   ![Messaggio di posta elettronica di trasferimento della sottoscrizione al destinatario](./media/billing-subscription-transfer/image3.png)
6. Il destinatario deve fare clic sul link e seguire le istruzioni, inclusa l'immissione delle informazioni sul pagamento.

   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image4.png)

   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image5.png)
7. Completamento della procedura La sottoscrizione è stata trasferita.

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Trasferire la proprietà di una sottoscrizione per i clienti con contratto Enterprise (EA, Enterprise Agreement)
L'amministratore Enterprise può trasferire la proprietà delle sottoscrizioni nell'ambito di una registrazione. Per iniziare, vedere [Transfer Account Ownership](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) (Trasferire la proprietà account) nel portale EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Passaggi successivi all'accettazione della proprietà di una sottoscrizione
1. A questo punto si è l'amministratore dell'account. Rivedere e aggiornare l’amministratore del servizio e i co-amministratori. Gestire gli amministratori nel [portale di Azure classico](https://manage.windowsazure.com) accedendo alle impostazioni. [Altre informazioni sui ruoli di amministratore](billing-add-change-azure-subscription-administrator.md).

2. Per la sottoscrizione e i servizi è anche usare il controllo degli accessi in base al ruolo. Visitare il [portale di Azure](https://portal.azure.com). [Altre informazioni sul controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md)

3. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   
   * Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Create and upload a management certificate for Azure](../cloud-services/cloud-services-certs-create.md)
   
   * Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).
   
   * Credenziali di accesso remoto per servizi quali macchine virtuali di Azure. 

4. [Aggiornare gli avvisi di fatturazione per questa sottoscrizione](billing-set-up-alerts.md) tramite il [Centro account di Azure](https://account.windowsazure.com/Subscriptions). 

5. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions).

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Domande frequenti
* <a name="whoisaa"></a> **Chi è l'amministratore account della sottoscrizione?**

  L'amministratore account è la persona che ha eseguito l'accesso o ha acquistato la sottoscrizione di Azure. È autorizzato ad accedere al [Centro account](https://account.windowsazure.com/Home/Index) ed eseguire diverse attività di gestione, ad esempio creare e annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione oppure modificare l'amministratore del servizio. Se non si conosce l'amministratore account per una sottoscrizione, seguire questa procedura per individuarlo.

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Nel menu Hub, selezionare **Sottoscrizione**.
  3. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
  4. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .  

* **Vengono trasferite tutte le risorse? Anche gruppi di risorse, macchine virtuali, dischi e altri servizi in esecuzione?**

  Sì, tutte le risorse, come macchine virtuali, dischi e siti Web, vengono trasferite al nuovo proprietario. I [ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) e i criteri di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) eventualmente configurati, invece, non vengono trasferiti tra directory diverse.

* <a id="no-button"></a> **Perché non viene visualizzato il pulsante Transfer Subscription (Trasferisci sottoscrizione)?**

  La mancata visualizzazione del pulsante **Transfer Subscription** (Trasferisci sottoscrizione) indica che il trasferimento della sottoscrizione non è supportato per l'offerta. [Contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

* **Il trasferimento della sottoscrizione comporta tempi di inattività del servizio?**

  Il trasferimento non incide in alcun modo sul servizio. Viene annullata la sottoscrizione dell'attuale amministratore account e ne viene creata una nuova per l'account del destinatario, associando però i servizi di Azure sottostanti alla nuova sottoscrizione. L'ID sottoscrizione rimane invariato.

* **Come è possibile usare questo processo per cambiare la directory della sottoscrizione?**

  Viene creata una sottoscrizione di Azure nella directory a cui appartiene l'amministratore account. Per modificare la directory, trasferire la sottoscrizione a un account utente nella directory di destinazione. Quando tale utente avrà completato i passaggi per accettare il trasferimento, la sottoscrizione passa automaticamente alla directory di destinazione.

* **Se si rileva la proprietà della fatturazione di una sottoscrizione da un'altra organizzazione, quest'ultima continua ad avere accesso alle risorse del nuovo proprietario?**

  Se la sottoscrizione viene trasferita a un altro tenant, gli utenti associati al tenant precedente perdono l'accesso alla sottoscrizione. Anche se un utente non è più amministratore del servizio o coamministratore, potrebbe avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, tra cui:

  * Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../cloud-services/cloud-services-certs-create.md).
  * Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).
  * Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

 Se il destinatario deve limitare l'accesso alle risorse, deve prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Scegliere **Tutte le risorse** dal menu Hub.
    3. Selezionare la risorsa. 
    4. Nel pannello della risorsa fare clic su **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.

* **Se si trasferisce la sottoscrizione nel corso del ciclo di fatturazione, il destinatario dovrà pagare il ciclo intero?**

  Il mittente è responsabile del pagamento per qualsiasi utilizzo segnalato fino al momento in cui il trasferimento viene completato. Il destinatario è responsabile per l'utilizzo segnalato dal momento del trasferimento in avanti. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. In questo caso l'uso è incluso nella fattura del destinatario.

* **Il destinatario ha accesso alla cronologia di utilizzo e di fatturazione?**

  Le sole informazioni disponibili per il destinatario riguardano l'importo dell'ultima fattura oppure, se la sottoscrizione è stata trasferita prima dell'emissione della prima fattura, il saldo attuale. Il resto della cronologia di fatturazione e utilizzo non viene trasferito con la sottoscrizione.

* **L'offerta può essere modificata durante un trasferimento?**

  L'offerta deve rimanere invariata. Per modificare l'offerta, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md).

* **È possibile trasferire una sottoscrizione a un account utente in un altro paese?**

  No, il trasferimento di una sottoscrizione a un account utente in un altro paese non è supportato. L'account utente del destinatario deve essere nello stesso paese.

* **Il destinatario può usare un metodo di pagamento diverso?**

  Sì. Tuttavia, la cronologia di fatturazione della sottoscrizione verrà suddivisa tra i due account.  

* **Il metodo di pagamento è in qualche modo interessato dal trasferimento di una sottoscrizione di Azure?**

  Per accettare un trasferimento di sottoscrizione, è necessario specificare una carta di credito o un metodo di pagamento analogo. Ad esempio, se Bob trasferisce una sottoscrizione a Jane e Jane accetta il trasferimento, Jane deve specificare anche il metodo di pagamento usato per la sottoscrizione. Al termine del trasferimento, la fattura per la sottoscrizione viene fatta a Jane, non a Bob.

* **Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?**

  Se non è possibile trasferire la proprietà di una sottoscrizione, eseguire la migrazione manuale delle risorse. Vedere [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Spostare le risorse in un nuovo gruppo o sottoscrizione).



## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema. 



