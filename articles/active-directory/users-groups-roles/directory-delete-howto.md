---
title: Eliminare una directory di Azure AD - Azure Active Directory Documenti Microsoft
description: Viene illustrato come preparare una directory di Azure AD per l'eliminazione, incluse le directory self-service
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
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961828"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminare una directory in Azure Active DirectoryDelete a directory in Azure Active Directory

Quando viene eliminata una directory di Azure AD, vengono eliminate anche tutte le risorse contenute nella directory. Preparare l'organizzazione riducendo al minimo le risorse associate prima di eliminarle. Solo un amministratore globale di Azure Active Directory (Azure AD) può eliminare una directory di Azure AD dal portale.

## <a name="prepare-the-directory"></a>Preparare la directory

Non è possibile eliminare una directory in Azure AD finché non supera diversi controlli. Questi controlli riducono il rischio che l'eliminazione di una directory di Azure AD influisca negativamente sull'accesso degli utenti, ad esempio la possibilità di accedere a Office 365 o alle risorse in Azure.These checks reduce risk that deleting an Azure AD directory negatively impacts user access, such as the ability to sign in to Office 365 or access resources in Azure. Ad esempio, se la directory associata a una sottoscrizione viene eliminata inavvertitamente, gli utenti non possono accedere alle risorse di Azure per tale sottoscrizione. Viene verificato che siano soddisfatte le condizioni seguenti:

* Non possono essere presenti utenti nella directory, ad eccezione di un amministratore globale che deve eliminare la directory. Tutti gli altri utenti devono essere eliminati prima che possa essere eliminata la directory. Se gli utenti vengono sincronizzati da locale, la sincronizzazione deve prima essere disattivata e gli utenti devono essere eliminati nella directory cloud usando il portale di Azure o i cmdlet di Azure PowerShell.If users are synchronized from on-premises, then sync must first be off, and the users must be deleted in the cloud directory using the Azure portal or Azure PowerShell cmdlets.
* La directory non può contenere applicazioni. Tutte le applicazioni devono essere rimosse prima di poter eliminare la directory.
* Non possono essere presenti provider di autenticazione a più fattori collegati alla directory.
* Alla directory non possono essere associate sottoscrizioni per i Microsoft Online Services, ad esempio Microsoft Azure, Office 365 o Azure AD Premium. Se, ad esempio, in Azure è stata creata una directory predefinita, non è possibile eliminare la directory se la propria sottoscrizione di Azure si basa ancora su di essa per l'autenticazione. Analogamente, non è possibile eliminare una directory se la sottoscrizione di un altro utente è associata a tale directory.

## <a name="delete-the-directory"></a>Eliminare la directory

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account amministratore globale dell'organizzazione.

2. Selezionare **Azure Active Directory**.

3. Passare alla directory che si desidera eliminare.
  
   ![Confermare l'organizzazione prima dell'eliminazione](./media/directory-delete-howto/delete-directory-command.png)

4. Selezionare **Elimina directory**.
  
   ![selezionare il comando per eliminare l'organizzazione](./media/directory-delete-howto/delete-directory-list.png)

5. Se la directory non supera uno o più controlli, viene fornito un collegamento per ulteriori informazioni su come passare. Dopo aver superato tutti i controlli, selezionare **Elimina** per completare il processo.

## <a name="if-you-cant-delete-the-directory"></a>Se non è possibile eliminare la directory

Dopo aver configurato la directory di Azure AD, è possibile che siano state attivate anche sottoscrizioni basate su licenza per l'organizzazione, ad esempio Azure AD Premium P2, Office 365 Business Premium o Enterprise Mobility , Sicurezza E5. Per evitare la perdita accidentale di dati, non è possibile eliminare una directory finché le sottoscrizioni non vengono completamente eliminate. Le sottoscrizioni devono essere in uno stato **Deprovisioning** per consentire l'eliminazione della directory. Una sottoscrizione **scaduta** o **annullata** passa allo stato **Disabilitato** e la fase finale è lo stato **Deprovisioning.**

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni sulla conservazione dei dati di Office 365 dati e il ciclo di vita della sottoscrizione, vedere [Cosa accade ai dati e all'accesso quando la sottoscrizione aziendale a Office 365 termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stato sottoscrizione | Dati | Accesso ai dati
----- | ----- | -----
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale all'interfaccia di amministrazione di Microsoft 365 e alle risorse 
Scaduto (30 giorni) | Dati accessibili a tutti| Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale all'interfaccia di amministrazione di Microsoft 365 e alle risorse
Disattivato (30 giorni) | Dati accessibili solo all'amministratore | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere all'interfaccia di amministrazione di Microsoft 365, ma non possono assegnare licenze o aggiornare gli utenti
Deprovisioning eseguito (30 giorni dopo la disattivazione) | Dati eliminati (eliminati automaticamente se nessun altro servizio è in funzione) | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere all'interfaccia di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioniAdmins can access the Microsoft 365 admin center to purchase and manage other subscriptions

## <a name="delete-a-subscription"></a>Eliminazione di una sottoscrizione

È possibile inserire una sottoscrizione nello stato Deprovisioning da eliminare in tre giorni utilizzando l'interfaccia di amministrazione di Microsoft 365.You can put a subscription into the **Deprovisioned** state to be deleted in three days using the Microsoft 365 admin center.

1. Accedere all'interfaccia di amministrazione di [Microsoft 365](https://admin.microsoft.com) con un account amministratore globale dell'organizzazione. Se si sta tentando di eliminare la directory "Contoso" con il dominio predefinito admin@contoso.onmicrosoft.cominiziale contoso.onmicrosoft.com, accedere con un UPN come .

2. Visualizzare l'anteprima della nuova interfaccia di amministrazione di Microsoft 365 assicurandosi che l'interruttore **Prova la nuova interfaccia di amministrazione** sia abilitata.

   ![Visualizzare in anteprima la nuova esperienza dell'interfaccia di amministrazione M365](./media/directory-delete-howto/preview-toggle.png)

3. Una volta abilitata la nuova interfaccia di amministrazione, è necessario annullare un abbonamento prima di poterlo eliminare. Selezionare **Fatturazione** e selezionare **Prodotti & servizi**, quindi Annulla **abbonamento** per l'abbonamento che si desidera annullare. Sarete portati a una pagina di feedback.

   ![Scegliere un abbonamento da annullare](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Compila il modulo di feedback e seleziona **Annulla abbonamento** per annullare l'abbonamento.

   ![Comando Annulla nell'anteprima dell'abbonamento](./media/directory-delete-howto/cancel-command.png)

5. È ora possibile eliminare la sottoscrizione. Selezionare **Elimina** per la sottoscrizione da eliminare. Se non è possibile trovare l'abbonamento nella pagina **Prodotti & servizi,** assicurarsi di avere Lo **stato dell'abbonamento** impostato su **Tutti**.

   ![Eliminare il collegamento per l'eliminazione della sottoscrizione](./media/directory-delete-howto/delete-command.png)

6. Selezionare **Elimina sottoscrizione** per eliminare la sottoscrizione e accettare i termini e le condizioni. Tutti i dati verranno definitivamente eliminati entro tre giorni. Se cambi idea, puoi [riattivare l'abbonamento](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante i tre giorni.
  
   ![leggere attentamente i termini e le condizioni](./media/directory-delete-howto/delete-terms.png)

7. Ora lo stato della sottoscrizione è cambiato e la sottoscrizione è contrassegnata per l'eliminazione. La sottoscrizione passa allo stato **Deprovisioning eseguito** 72 ore dopo.

8. Dopo aver eliminato una sottoscrizione nella directory e 72 ore trascorse, è possibile accedere nuovamente all'interfaccia di amministrazione di Azure AD e non dovrebbe essere necessaria alcuna azione e nessuna sottoscrizione che blocca l'eliminazione della directory. Dovrebbe essere possibile eliminare correttamente la directory di Azure AD.
  
   ![superare il controllo della sottoscrizione nella schermata di eliminazione](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ho una sottoscrizione di prova che blocca l'eliminazione

Sono disponibili prodotti per [l'iscrizione self-service](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) come Microsoft Power BI, Rights Management Services, Microsoft Power Apps o Dynamics 365, i singoli utenti possono iscriversi tramite Office 365, che crea anche un utente guest per l'autenticazione nella directory di Azure AD. Questi prodotti self-service bloccano le eliminazioni di directory fino a quando non vengono eliminati completamente dalla directory, per evitare la perdita di dati. Possono essere eliminati solo dall'amministratore di Azure AD, sia che l'utente si sia registrato singolarmente o sia stato assegnato al prodotto.

Esistono due tipi di prodotti di iscrizione self-service nel modo in cui vengono assegnati: 

* Assegnazione a livello di organizzazione: un amministratore di Azure AD assegna il prodotto all'intera organizzazione e un utente può usare attivamente il servizio con questa assegnazione a livello di organizzazione anche se non dispone di una licenza individuale.
* Assegnazione a livello di utente: un singolo utente durante l'iscrizione self-service assegna essenzialmente il prodotto a se stesso senza un amministratore. Una volta che l'organizzazione viene gestita da un amministratore (vedere Acquisizione di una [directory non gestita](domains-admin-takeover.md)da parte dell'amministratore , l'amministratore può assegnare direttamente il prodotto agli utenti senza registrazione self-service.  

Quando si inizia l'eliminazione del prodotto di registrazione self-service, l'azione elimina definitivamente i dati e rimuove tutti gli accessi degli utenti al servizio. A qualsiasi utente a cui è stata assegnata l'offerta singolarmente o a livello di organizzazione viene quindi impedito di accedere o accedere ai dati esistenti. Se si desidera evitare la perdita di dati con il prodotto di iscrizione self-service come i dashboard di Microsoft Power BI o la configurazione dei criteri di [Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), [assicurarsi](https://docs.microsoft.com/power-bi/service-export-to-pbix) che venga eseguito il backup e il salvataggio dei dati altrove.

Per ulteriori informazioni sui prodotti e i servizi di iscrizione self-service attualmente disponibili, vedere [Programmi self-service disponibili.](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni sulla conservazione dei dati di Office 365 dati e il ciclo di vita della sottoscrizione, vedere [Cosa accade ai dati e all'accesso quando la sottoscrizione aziendale a Office 365 termina?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stato del prodotto | Dati | Accesso ai dati
------------- | ---- | --------------
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno normale accesso a prodotti di iscrizione self-service, file o app<br>Gli amministratori hanno accesso normale all'interfaccia di amministrazione di Microsoft 365 e alle risorse
Deleted | Dati cancellati | Gli utenti non possono accedere a prodotti di iscrizione self-service, file o app<br>Gli amministratori possono accedere all'interfaccia di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioniAdmins can access the Microsoft 365 admin center to purchase and manage other subscriptions

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Come è possibile eliminare un prodotto di iscrizione self-service nel portale di Azure?

È possibile inserire un prodotto di iscrizione self-service come Microsoft Power BI o Azure Rights Management Services in uno stato **Elimina** per essere immediatamente eliminato nel portale di Azure AD.

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account che sia un amministratore globale nell'organizzazione. Se si sta tentando di eliminare la directory "Contoso" con il dominio predefinito admin@contoso.onmicrosoft.cominiziale contoso.onmicrosoft.com, accedere con un UPN come .

2. Selezionare **Licenze**, quindi Prodotti per **l'iscrizione self-service**. È possibile visualizzare tutti i prodotti di iscrizione self-service separatamente dagli abbonamenti basati su postazioni. Scegli il prodotto che desideri eliminare definitivamente. Ecco un esempio in Microsoft Power BI:

    ![il nome utente non è stato digitato in modo errato o non trovato](./media/directory-delete-howto/licenses-page.png)

3. Selezionare **Elimina** per eliminare il prodotto e accettare i termini che i dati vengono eliminati immediatamente e irrevocabilmente. Questa azione di eliminazione rimuoverà tutti gli utenti e rimuoverà l'accesso dell'organizzazione al prodotto. Fare clic su Sì per andare avanti con l'eliminazione.  

    ![il nome utente non è stato digitato in modo errato o non trovato](./media/directory-delete-howto/delete-product.png)

4. Quando si seleziona **Sì**, verrà avviata l'eliminazione del prodotto self-service. C'è una notifica che ti dirà della cancellazione in corso.  

    ![il nome utente non è stato digitato in modo errato o non trovato](./media/directory-delete-howto/progress-message.png)

5. Ora lo stato del prodotto self-service è stato modificato in **Deleted**. Quando si aggiorna la pagina, il prodotto deve essere rimosso dalla pagina Prodotti di **iscrizione self-service.**  

    ![il nome utente non è stato digitato in modo errato o non trovato](./media/directory-delete-howto/product-deleted.png)

6. Dopo aver eliminato tutti i prodotti, è possibile accedere nuovamente all'interfaccia di amministrazione di Azure AD e non dovrebbero essere presenti azioni obbligatorie e nessun prodotto che blocca l'eliminazione della directory. Dovrebbe essere possibile eliminare correttamente la directory di Azure AD.

    ![il nome utente non è stato digitato in modo errato o non trovato](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passaggi successivi

[Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
