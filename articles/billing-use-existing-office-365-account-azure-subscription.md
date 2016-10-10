<properties
	pageTitle="Condividere un unico tenant di Azure AD tra l'abbonamento a Office 365 e la sottoscrizione di Azure | Microsoft Azure"
	description="Informazioni su come condividere il tenant di Azure AD per Office 365 e i relativi utenti con la sottoscrizione di Azure o viceversa"
	services=""
	documentationCenter=""
	authors="JiangChen79"
	manager="mbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="cjiang"/>

# Uso dell'account Office 365 esistente con la sottoscrizione di Azure o viceversa
Scenario: è già disponibile un abbonamento a Office 365 e si è pronti per una sottoscrizione di Azure, ma si vuole usare uno o più account utente di Office 365 esistenti per la sottoscrizione di Azure. Scenario alternativo: è disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente. Questo articolo mostra come è facile ottenere entrambi.

> [AZURE.NOTE] Questo articolo non si applica ai clienti con Contratto Enterprise.

> [AZURE.NOTE] Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## Indicazioni rapide

- Se è già disponibile un abbonamento a Office 365 e ci si vuole iscrivere ad Azure, usare l'opzione **Accedere con il proprio account aziendale** e continuare l'iscrizione ad Azure con l'account Office 365. Vedere la [procedura dettagliata](#s1).
- Se è già disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365, accedere a Office 365 con il proprio account Azure e procedere con l'iscrizione. Al termine, l'abbonamento a Office 365 viene aggiunto alla stessa istanza di Azure Active Directory a cui appartiene la sottoscrizione di Azure. Vedere la [procedura dettagliata](#s2).

>[AZURE.NOTE] Per ottenere un abbonamento a Office 365, l'account usato per l'accesso deve essere un membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure AD. [Informazioni su come conoscere il ruolo di Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

Per comprendere come funziona l'aggiunta di una sottoscrizione a un account, vedere la sezione [Informazioni generali](#background-information) più avanti nell'articolo.

## Procedura dettagliata
<a id="s1"></a>
### Scenario 1: utenti di Office 365 che pianificano l'acquisto di Azure
In questo scenario si presuppone che Kelley Wall sia un utente con un abbonamento a Office 365 che intende acquistare una sottoscrizione di Azure. Ci sono altri due utenti attivi: Jane e Tricia. L'account di Kelley è admin@contoso.onmicrosoft.com.

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Per iscriversi ad Azure, seguire questa procedura:

1. Iscriversi ad Azure alla pagina [Azure.com](https://azure.microsoft.com/). Fare clic su **Inizia gratuitamente**. Nella pagina successiva, fare clic su **Inizia gratuitamente**.

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Fare clic su **Accedere con il proprio account aziendale**.

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Accedere con il proprio account di Office 365. In questo caso, l'account di Office 365 di Kelley.

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Immettere le informazioni e completare la procedura d'accesso.

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. Fare clic su **Inizia a gestire il servizio** per iniziare.

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

A questo punto è tutto pronto. Nel portale di Azure verranno visualizzati gli stessi utenti presenti nella stessa directory. A questo scopo, seguire questa procedura:

1. Fare clic su **Inizia a gestire il servizio** nella schermata precedente.
2. Fare clic su **Esplora** e selezionare **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Fare clic su **UTENTI**.

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Tutti gli utenti inclusa Kelley saranno elencati come previsto.

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### Scenario 2: utenti di Azure che pianificano l'acquisto di Office 365

In questo scenario Kelley Wall è un utente che ha una sottoscrizione di Azure con l'account admin@contoso.onmicrosoft.com. Kelley vuole effettuare la sottoscrizione di Office 365 e usare la stessa directory che possiede già in Azure.

>[AZURE.NOTE] Per ottenere un abbonamento a Office 365, l'account usato per l'accesso deve essere un membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure AD. [Informazioni su come conoscere il ruolo di Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Per eseguire la sottoscrizione di Office 365, seguire questa procedura:

1. Passare alla [pagina del prodotto Office 365](https://products.office.com/business) e selezionare un piano adatto alle proprie esigenze.
2. Dopo aver selezionato il piano viene visualizzata la pagina seguente. Non compilare i dati. Fare clic su **Accedi** nella parte superiore destra della pagina.

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Accedere con le credenziali del proprio account. In questo caso, l'account di Kelley.

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Fare clic su **Prova adesso**.

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Nella pagina di conferma dell'ordine fare clic su **Continua**.

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

A questo punto è tutto pronto. Nell'interfaccia di amministrazione di Office 365 verranno visualizzati gli utenti dalla directory di Contoso presenti come utenti attivi. A questo scopo, seguire questa procedura:

1. Aprire l'interfaccia di amministrazione di Office 365.
2. Espandere **UTENTI** e quindi fare clic su **Utenti attivi**.

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Informazioni su come conoscere il proprio ruolo di Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Esplora** e selezionare **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Fare clic su **UTENTI**.

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Fare clic sull'utente. In questo esempio Kelley Wall.
5. Prendere nota del campo **RUOLO AZIENDALE**.

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## Informazioni generali
Office 365 e Azure usano il servizio Azure Active Directory (AAD) per gestire utenti e sottoscrizioni. Considerare una directory di Azure come un contenitore in cui raggruppare utenti e sottoscrizioni. Per usare lo stesso account utente per la sottoscrizione di Microsoft Azure e l'abbonamento a Office 365, è necessario assicurarsi che vengano creati nella stessa directory.

- Una sottoscrizione viene creata in una directory, non viceversa.
- Gli utenti appartengono alle directory, non viceversa.
- Una sottoscrizione viene inserita nella directory dell'utente che crea la sottoscrizione. Di conseguenza, l'abbonamento a Office 365 è associato allo stesso account della sottoscrizione di Azure usato per creare l'abbonamento a Office 365.

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

**Note:**

- Le sottoscrizioni di Azure appartengono a singoli utenti nella directory.
- Gli abbonamenti a Office 365 appartengono alla directory stessa. Gli utenti all'interno della directory possono agire sulle sottoscrizioni se hanno le autorizzazioni necessarie.

##Passaggi successivi
Esiste uno scenario correlato in cui sono state acquistate separatamente la sottoscrizione di Azure e l'abbonamento a Office 365 e si vuole poter accedere al tenant di Office 365 dalla sottoscrizione di Azure. Per informazioni su come eseguire questa attività, vedere [Associare un tenant di Office 365 con una sottoscrizione di Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

<!---HONumber=AcomDC_0928_2016-->