---
title: Eliminare una directory di Azure AD - Azure Active Directory | Microsoft Docs
description: Spiega come preparare Azure Active directory per l'eliminazione, comprese le directory self-service
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607297"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminare una directory in Azure Active Directory

Quando viene eliminata una directory Azure AD, vengono eliminate anche tutte le risorse che sono contenute nella directory. Preparare l'organizzazione, riducendo al minimo le risorse associate prima di eliminare. Solo un amministratore globale di Azure Active Directory (Azure AD) è possibile eliminare una directory Azure AD dal portale.

## <a name="prepare-the-directory"></a>Preparare la directory

È possibile eliminare una directory di Azure AD fino a quando non supera i controlli diversi. Questi controlli di riducono il rischio che l'eliminazione di una directory Azure AD a influire negativamente influisce sull'accesso utente, ad esempio la possibilità di accedere a Office 365 o alle risorse in Azure. Ad esempio, se la directory associata a una sottoscrizione viene eliminata accidentalmente, gli utenti non possono accedere le risorse di Azure per la sottoscrizione. Viene verificato che siano soddisfatte le condizioni seguenti:

* Non può esistere alcun utente nella directory, ad eccezione di un amministratore globale che eliminerà la directory. Tutti gli altri utenti devono essere eliminati prima che possa essere eliminata la directory. Se gli utenti vengono sincronizzati dall'ambiente locale, quindi sincronizzazione è necessario prima disattivare e gli utenti devono essere eliminati nella directory cloud usando il portale di Azure o i cmdlet di Azure PowerShell.
* La directory non può contenere applicazioni. Tutte le applicazioni devono essere rimossi prima di poter eliminare la directory.
* Non può essere presente alcun provider di multi-factor authentication alla directory collegati.
* Alla directory non possono essere associate sottoscrizioni per i Microsoft Online Services, ad esempio Microsoft Azure, Office 365 o Azure AD Premium. Se, ad esempio, in Azure è stata creata una directory predefinita, non è possibile eliminare la directory se la propria sottoscrizione di Azure si basa ancora su di essa per l'autenticazione. Analogamente, non è possibile eliminare una directory se la sottoscrizione di un altro utente è associata a tale directory.

## <a name="delete-the-directory"></a>Eliminare la directory

1. Accedi per il [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale dell'organizzazione.

2. Selezionare **Azure Active Directory**.

3. Passare alla directory che si desidera eliminare.
  
   ![Conferma dell'organizzazione prima dell'eliminazione](./media/directory-delete-howto/delete-directory-command.png)

4. Selezionare **Elimina directory**.
  
   ![Selezionare il comando per eliminare l'organizzazione](./media/directory-delete-howto/delete-directory-list.png)

5. Se la directory non viene passato uno o più controlli, viene visualizzata con un collegamento per altre informazioni su come passare. Dopo aver superato tutti i controlli, selezionare **Elimina** per completare il processo.

## <a name="if-you-cant-delete-the-directory"></a>Se non è possibile eliminare la directory

Quando è stata configurata la directory di Azure AD, è per l'organizzazione, ad esempio Azure AD Premium P2, Office 365 Business Premium o Enterprise Mobility + Security E5 possono avere attivato anche le sottoscrizioni basate su licenza. Per evitare la perdita accidentale dei dati, è possibile eliminare una directory fino a quando le sottoscrizioni vengono completamente rimossi. Le sottoscrizioni devono essere un **deprovisioning eseguito** dello stato per consentire l'eliminazione di directory. Un' **scaduto** o **Canceled** sottoscrizione viene spostata nel **disabilitato** dello stato e la fase finale è il **deprovisioning eseguito** dello stato.

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni sulla conservazione dei dati di Office 365 dati e il ciclo di vita della sottoscrizione, vedere [Cosa accade ai dati e all'accesso quando la sottoscrizione aziendale a Office 365 termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stato sottoscrizione | Dati | Accesso ai dati
----- | ----- | -----
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale interfaccia di amministrazione di Microsoft 365 e alle risorse 
Scaduto (30 giorni) | Dati accessibili a tutti| Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale interfaccia di amministrazione di Microsoft 365 e alle risorse
Disattivato (30 giorni) | Dati accessibili solo all'amministratore | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere al centro di amministrazione Microsoft 365, ma non è possibile assegnare le licenze o aggiornare gli utenti
Deprovisioning eseguito (30 giorni dopo la disattivazione) | Dati eliminati (eliminati automaticamente se nessun altro servizio è in funzione) | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere l'interfaccia di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioni

## <a name="delete-a-subscription"></a>Elimina una sottoscrizione

È possibile inserire una sottoscrizione nello stato di deprovisioning eseguito deve essere eliminato entro tre giorni usando l'interfaccia di amministrazione di Microsoft 365.

1. Accedi per il [interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com) con un account che sia un amministratore globale dell'organizzazione. Se si sta tentando di eliminare la directory "Contoso" che ha contoso.onmicrosoft.com il dominio predefinito iniziale, accedere con un nome UPN, ad esempio admin@contoso.onmicrosoft.com.

2. Selezionare **fatturazione** e selezionare **sottoscrizioni**, quindi scegliere la sottoscrizione da annullare. Dopo aver fatto clic su **Annulla**, aggiornare la pagina.
  
   ![Eliminare il collegamento per l'eliminazione della sottoscrizione](./media/directory-delete-howto/delete-command.png)
  
3. Selezionare **Elimina** per eliminare la sottoscrizione e accettare i termini e le condizioni. Tutti i dati verranno definitivamente eliminati entro tre giorni. Se si cambia idea, è possibile riattivare la sottoscrizione nel corso dei tre giorni.
  
   ![leggere attentamente le condizioni](./media/directory-delete-howto/delete-terms.png)

4. Una volta modificato lo stato, la sottoscrizione è contrassegnata per l'eliminazione. La sottoscrizione passa allo stato **Deprovisioning eseguito** 72 ore dopo.

5. Una volta eliminata una sottoscrizione nella directory e trascorrere delle 72 ore, è possibile effettuare li riconverte in Centro di amministrazione di Azure AD nuovamente e vi devono essere Nessuna azione necessaria e non sono presenti sottoscrizioni blocca l'eliminazione di directory. Dovrebbe essere possibile eliminare la directory di Azure AD.
  
   ![superare il controllo della sottoscrizione nella schermata di eliminazione](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ho una sottoscrizione di valutazione che blocca l'eliminazione

Esistono [prodotti iscrizione self-servizi](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) come Microsoft Power BI, Rights Management Services, Microsoft Powerapps o Dynamics 365, singoli utenti possono iscriversi tramite Office 365, che crea anche un utente guest per l'autenticazione in directory di Azure AD. Questi prodotti self-servizi bloccano le operazioni di eliminazione di directory finché vengono eliminati completamente dalla directory, per evitare la perdita di dati. Si può essere eliminati solo dall'amministratore di Azure AD se l'utente è iscritto singolarmente o è stato assegnato il prodotto.

Esistono due tipi di prodotti di iscrizione self-servizi in modo in cui sono assegnati: 

* Assegnazione a livello di organizzazione: Un amministratore di Azure AD assegna il prodotto per l'intera organizzazione e un utente può essere attivamente usando il servizio con questa assegnazione a livello di organizzazione anche se essi non dispongono della licenza singolarmente.
* Assegnazione del livello di utente: Un singolo utente durante l'iscrizione Self-Service assegna essenzialmente il prodotto a se stessi senza un amministratore. Quando l'organizzazione diventa gestita da un amministratore (vedere [acquisizioni della proprietà amministratore di una directory non gestita](domains-admin-takeover.md), quindi l'amministratore può assegnare direttamente il prodotto agli utenti senza iscrizione Self-Service.  

Quando si inizia l'eliminazione del prodotto iscrizione self-service, l'azione vengono eliminati i dati in modo permanente e rimuove completamente l'accesso utente al servizio. Tutti gli utenti singolarmente o a livello di organizzazione è stato assegnato l'offerta viene quindi impedito l'accesso o l'accesso ai dati esistenti. Se si desidera evitare la perdita di dati con il prodotto di iscrizione self-service, ad esempio [i dashboard di Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) oppure [configurazione dei criteri di Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), assicurarsi che i dati viene eseguito il backup e salvato in un' posizione.

Per altre informazioni sui servizi e prodotti di iscrizione self-servizi attualmente disponibili, vedere [programmi disponibili a Self-servizi](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni su Office 365 dati conservazione e la sottoscrizione del ciclo di vita, vedere [cosa accade ai dati e l'accesso al termine di Office 365 per la sottoscrizione di business?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stato del prodotto | Dati | Accesso ai dati
------------- | ---- | --------------
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno accesso normale per il prodotto di iscrizione self-service, file o le app<br>Gli amministratori hanno accesso normale interfaccia di amministrazione di Microsoft 365 e alle risorse
Deleted | Dati eliminati | Gli utenti non possono accedere prodotto iscrizione self-service, file o le app<br>Gli amministratori possono accedere l'interfaccia di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioni

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Come è possibile eliminare un prodotto di iscrizione self-service nel portale di Azure?

È possibile inserire un prodotto di iscrizione self-service, come Microsoft Power BI o Azure Rights Management Services in una **eliminare** lo stato deve essere immediatamente eliminato nel portale di Azure AD.

1. Accedi per il [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account che sia un amministratore globale dell'organizzazione. Se si sta tentando di eliminare la directory "Contoso" che ha contoso.onmicrosoft.com il dominio predefinito iniziale, accedere con un nome UPN, ad esempio admin@contoso.onmicrosoft.com.

2. Selezionare **licenze**, quindi selezionare **prodotti iscrizione self-servizi**. È possibile visualizzare tutti i self-servizi per l'abbonamento prodotti separatamente dalle sottoscrizioni basati sulle postazioni. Scegliere il prodotto che si desidera eliminare definitivamente. Ecco un esempio in Microsoft Power BI:

    ![il nome utente è stato digitato correttamente o non trovato](./media/directory-delete-howto/licenses-page.png)

3. Selezionare **eliminare** per eliminare il prodotto e accettare le condizioni per i dati vengono eliminati immediatamente e irrevocabilmente. Questa azione Elimina rimuoverà tutti gli utenti e rimuovere l'accesso dell'organizzazione per il prodotto. Fare clic su Sì per procedere con l'eliminazione.  

    ![il nome utente è stato digitato correttamente o non trovato](./media/directory-delete-howto/delete-product.png)

4. Quando si seleziona **Sì**, verrà avviata l'eliminazione del prodotto self-service. È presente una notifica che indicherà dell'eliminazione in corso.  

    ![il nome utente è stato digitato correttamente o non trovato](./media/directory-delete-howto/progress-message.png)

5. A questo punto il prodotto di iscrizione self-service è stato **Deleted**. Quando si aggiorna la pagina, il prodotto deve essere rimosso dal **prodotti iscrizione self-servizi** pagina.  

    ![il nome utente è stato digitato correttamente o non trovato](./media/directory-delete-howto/product-deleted.png)

6. Dopo avere eliminato tutti i prodotti, è possibile firmare nuovamente nel centro di amministrazione di Azure AD e non vi sarà alcuna azione necessaria e prodotti non blocca l'eliminazione di directory. Dovrebbe essere possibile eliminare la directory di Azure AD.

    ![il nome utente è stato digitato correttamente o non trovato](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passaggi successivi

[Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
