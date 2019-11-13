---
title: Eliminare un Azure AD directory-Azure Active Directory | Microsoft Docs
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
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961828"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminare una directory in Azure Active Directory

Quando viene eliminata una directory di Azure AD, vengono eliminate anche tutte le risorse contenute nella directory. Preparare l'organizzazione riducendo al minimo le risorse associate prima di eliminare. Solo un amministratore globale di Azure Active Directory (Azure AD) può eliminare una directory di Azure AD dal portale.

## <a name="prepare-the-directory"></a>Preparare la directory

Non è possibile eliminare una directory in Azure AD finché non vengono superati diversi controlli. Questi controlli riducono il rischio che l'eliminazione di un Azure AD directory influisca negativamente sull'accesso degli utenti, ad esempio la possibilità di accedere a Office 365 o di accedere alle risorse in Azure. Se ad esempio la directory associata a una sottoscrizione viene eliminata involontariamente, gli utenti non potranno accedere alle risorse di Azure per tale sottoscrizione. Viene verificato che siano soddisfatte le condizioni seguenti:

* Non possono essere presenti utenti nella directory, ad eccezione di un amministratore globale che deve eliminare la directory. Tutti gli altri utenti devono essere eliminati prima che possa essere eliminata la directory. Se gli utenti vengono sincronizzati dall'ambiente locale, è prima necessario disattivare la sincronizzazione e gli utenti devono essere eliminati nella directory cloud usando i cmdlet portale di Azure o Azure PowerShell.
* La directory non può contenere applicazioni. Prima di poter eliminare la directory, è necessario rimuovere tutte le applicazioni.
* Non possono essere presenti provider di autenticazione a più fattori collegati alla directory.
* Alla directory non possono essere associate sottoscrizioni per i Microsoft Online Services, ad esempio Microsoft Azure, Office 365 o Azure AD Premium. Se, ad esempio, in Azure è stata creata una directory predefinita, non è possibile eliminare la directory se la propria sottoscrizione di Azure si basa ancora su di essa per l'autenticazione. Analogamente, non è possibile eliminare una directory se la sottoscrizione di un altro utente è associata a tale directory.

## <a name="delete-the-directory"></a>Elimina la directory

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account che sia l'amministratore globale dell'organizzazione.

2. Selezionare **Azure Active Directory**.

3. Passare alla directory che si vuole eliminare.
  
   ![Conferma organizzazione prima dell'eliminazione](./media/directory-delete-howto/delete-directory-command.png)

4. Selezionare **Elimina directory**.
  
   ![Selezionare il comando per eliminare l'organizzazione](./media/directory-delete-howto/delete-directory-list.png)

5. Se la directory non supera uno o più controlli, viene fornito un collegamento ad altre informazioni su come passare. Dopo aver superato tutti i controlli, selezionare **Elimina** per completare il processo.

## <a name="if-you-cant-delete-the-directory"></a>Se non è possibile eliminare la directory

Quando è stata configurata la directory di Azure AD, è possibile che siano state attivate anche le sottoscrizioni basate su licenza per l'organizzazione, ad esempio Azure AD Premium P2, Office 365 Business Premium o Enterprise Mobility + Security E5. Per evitare la perdita accidentale di dati, non è possibile eliminare una directory fino a quando le sottoscrizioni non vengono eliminate completamente. Per consentire l'eliminazione della directory, le sottoscrizioni devono essere in stato **deprovisioning** . Una sottoscrizione **scaduta** o **annullata** viene spostata allo stato **disabilitato** e la fase finale è lo stato di **deprovisioning** .

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni sulla conservazione dei dati di Office 365 dati e il ciclo di vita della sottoscrizione, vedere [Cosa accade ai dati e all'accesso quando la sottoscrizione aziendale a Office 365 termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stato sottoscrizione | Dati | Accesso ai dati
----- | ----- | -----
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale a Microsoft 365 interfaccia di amministrazione e risorse 
Scaduto (30 giorni) | Dati accessibili a tutti| Gli utenti hanno accesso normale ai file di Office 365 o alle app<br>Gli amministratori hanno accesso normale a Microsoft 365 interfaccia di amministrazione e risorse
Disattivato (30 giorni) | Dati accessibili solo all'amministratore | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere al Microsoft 365 interfaccia di amministrazione, ma non possono assegnare licenze o aggiornare gli utenti
Deprovisioning eseguito (30 giorni dopo la disattivazione) | Dati eliminati (eliminati automaticamente se nessun altro servizio è in funzione) | Gli utenti non possono accedere ai file di Office 365 o alle app<br>Gli amministratori possono accedere al centro di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioni

## <a name="delete-a-subscription"></a>Eliminare una sottoscrizione

È possibile inserire una sottoscrizione nello stato di **deprovisioning** da eliminare tra tre giorni usando l'interfaccia di amministrazione di Microsoft 365.

1. Accedere al centro di [amministrazione di Microsoft 365](https://admin.microsoft.com) con un account che sia un amministratore globale dell'organizzazione. Se si sta provando a eliminare la directory "contoso" con il dominio predefinito iniziale contoso.onmicrosoft.com, effettuare l'accesso con un UPN, ad esempio admin@contoso.onmicrosoft.com.

2. Visualizzare l'anteprima del nuovo Microsoft 365 interfaccia di amministrazione verificando che l'interruttore **prova il nuovo centro di amministrazione** sia abilitato.

   ![Anteprima della nuova esperienza dell'interfaccia di amministrazione di M365](./media/directory-delete-howto/preview-toggle.png)

3. Quando il nuovo centro di amministrazione è abilitato, è necessario annullare una sottoscrizione prima di poterla eliminare. Selezionare **fatturazione** e selezionare **prodotti & Servizi**, quindi selezionare **Annulla sottoscrizione** per la sottoscrizione che si vuole annullare. Verrà visualizzata una pagina di feedback.

   ![Scegliere una sottoscrizione da annullare](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Completare il modulo feedback e selezionare **Annulla sottoscrizione** per annullare la sottoscrizione.

   ![Annulla il comando nell'anteprima della sottoscrizione](./media/directory-delete-howto/cancel-command.png)

5. È ora possibile eliminare la sottoscrizione. Selezionare **Elimina** per la sottoscrizione che si vuole eliminare. Se la sottoscrizione non è stata trovata nella pagina **prodotti & Servizi** , assicurarsi che lo **stato della sottoscrizione** sia impostato su **tutti**.

   ![Eliminare il collegamento per l'eliminazione della sottoscrizione](./media/directory-delete-howto/delete-command.png)

6. Selezionare **Elimina sottoscrizione** per eliminare la sottoscrizione e accettare i termini e le condizioni. Tutti i dati verranno definitivamente eliminati entro tre giorni. Se si cambia idea, è possibile [riattivare la sottoscrizione](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante il periodo di tre giorni.
  
   ![leggere attentamente i termini e le condizioni](./media/directory-delete-howto/delete-terms.png)

7. A questo punto lo stato della sottoscrizione è stato modificato e la sottoscrizione è contrassegnata per l'eliminazione. La sottoscrizione passa allo stato **Deprovisioning eseguito** 72 ore dopo.

8. Una volta eliminata una sottoscrizione nella directory e sono trascorse 72 ore, è possibile accedere di nuovo all'interfaccia di amministrazione di Azure AD e non deve esistere alcuna azione necessaria e nessuna sottoscrizione blocca l'eliminazione della directory. Dovrebbe essere possibile eliminare correttamente la directory Azure AD.
  
   ![superare il controllo della sottoscrizione nella schermata di eliminazione](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ho una sottoscrizione di valutazione che blocca l'eliminazione

Sono disponibili [prodotti di iscrizione self-service](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) come Microsoft Power BI, Rights Management Services, Microsoft Power Apps o Dynamics 365, i singoli utenti possono iscriversi tramite Office 365, che crea anche un utente Guest per l'autenticazione nella directory Azure ad. Questi prodotti self-service bloccano le eliminazioni di directory fino a quando non vengono eliminati completamente dalla directory, per evitare la perdita di dati. Possono essere eliminati solo dall'amministratore Azure AD se l'utente ha effettuato l'iscrizione singolarmente o è stato assegnato al prodotto.

Esistono due tipi di prodotti di iscrizione self-service in modalità di assegnazione: 

* Assegnazione a livello di organizzazione: un amministratore di Azure AD assegna il prodotto all'intera organizzazione e un utente può usare attivamente il servizio con questa assegnazione a livello di organizzazione anche se non è concesso in licenza individualmente.
* Assegnazione a livello di utente: un singolo utente durante l'iscrizione self-service essenzialmente assegna il prodotto a se stesso senza un amministratore. Quando l'organizzazione viene gestita da un amministratore (vedere [acquisizione di una directory non gestita](domains-admin-takeover.md)da parte dell'amministratore), l'amministratore può assegnare il prodotto direttamente agli utenti senza iscrizione self-service.  

Quando si inizia l'eliminazione del prodotto di iscrizione self-service, l'azione Elimina definitivamente i dati e rimuove tutti gli utenti che accedono al servizio. A tutti gli utenti a cui è stata assegnata l'offerta singolarmente o a livello di organizzazione viene quindi impedito l'accesso o l'accesso a tutti i dati esistenti. Se si desidera impedire la perdita di dati con il prodotto di iscrizione self-service, ad esempio i [dashboard di Microsoft Power bi](https://docs.microsoft.com/power-bi/service-export-to-pbix) o la [configurazione dei criteri di Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), assicurarsi che i dati vengano sottoposti a backup e salvati altrove.

Per ulteriori informazioni sui prodotti e i servizi di iscrizione self-service attualmente disponibili, vedere la pagina relativa ai [programmi self-service disponibili](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Per sapere come comportarsi quando una sottoscrizione della versione di prova gratuita di Office 365 scade (esclusi Partner/CSP a pagamento, Enterprise Agreement o Contratti multilicenza), vedere la tabella seguente. Per altre informazioni sulla conservazione dei dati di Office 365 dati e il ciclo di vita della sottoscrizione, vedere [Cosa accade ai dati e all'accesso quando la sottoscrizione aziendale a Office 365 termina?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stato del prodotto | Dati | Accesso ai dati
------------- | ---- | --------------
Attivo (30 giorni per la versione di prova gratuita) | Dati accessibili a tutti | Gli utenti hanno accesso normale a prodotto, file o app di iscrizione self-service<br>Gli amministratori hanno accesso normale a Microsoft 365 interfaccia di amministrazione e risorse
Deleted | Dati eliminati | Gli utenti non possono accedere al prodotto, ai file o alle app di iscrizione self-service<br>Gli amministratori possono accedere al centro di amministrazione di Microsoft 365 per acquistare e gestire altre sottoscrizioni

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Come è possibile eliminare un prodotto di iscrizione self-service nel portale di Azure?

È possibile inserire un prodotto di iscrizione self-service, ad esempio Microsoft Power BI o Azure Rights Management Services in uno stato di eliminazione, da **eliminare** immediatamente nel portale di Azure ad.

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account che sia un amministratore globale dell'organizzazione. Se si sta tentando di eliminare la directory "contoso" con il dominio predefinito iniziale contoso.onmicrosoft.com, effettuare l'accesso con un UPN, ad esempio admin@contoso.onmicrosoft.com.

2. Selezionare **licenze**e quindi selezionare **prodotti di iscrizione self-service**. È possibile visualizzare tutti i prodotti di iscrizione self-service separatamente dalle sottoscrizioni basate su postazione. Scegliere il prodotto che si desidera eliminare definitivamente. Di seguito è riportato un esempio in Microsoft Power BI:

    ![il nome utente non è tipizzato o non è stato trovato](./media/directory-delete-howto/licenses-page.png)

3. Selezionare **Elimina** per eliminare il prodotto e accettare le condizioni che i dati vengono eliminati immediatamente e irrevocabilmente. Questa azione di eliminazione rimuove tutti gli utenti e rimuove l'accesso dell'organizzazione al prodotto. Fare clic su Sì per procedere con l'eliminazione.  

    ![il nome utente non è tipizzato o non è stato trovato](./media/directory-delete-howto/delete-product.png)

4. Quando si seleziona **Sì**, l'eliminazione del prodotto self-service verrà avviata. È presente una notifica che indica che l'eliminazione è in corso.  

    ![il nome utente non è tipizzato o non è stato trovato](./media/directory-delete-howto/progress-message.png)

5. A questo punto lo stato del prodotto di iscrizione self-service è stato modificato in **eliminato**. Quando si aggiorna la pagina, il prodotto deve essere rimosso dalla pagina dei **prodotti di iscrizione self-service** .  

    ![il nome utente non è tipizzato o non è stato trovato](./media/directory-delete-howto/product-deleted.png)

6. Una volta eliminati tutti i prodotti, è possibile accedere di nuovo all'interfaccia di amministrazione di Azure AD e non è necessario eseguire alcuna azione obbligatoria e nessun prodotto blocca l'eliminazione della directory. Dovrebbe essere possibile eliminare correttamente la directory Azure AD.

    ![il nome utente non è tipizzato o non è stato trovato](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passaggi successivi

[Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
