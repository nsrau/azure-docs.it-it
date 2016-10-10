<properties
	pageTitle="Usare il tenant di Office 365 con la sottoscrizione di Azure | Microsoft Azure"
	description="Informazioni su come aggiungere una directory (tenant) di Office 365 a una sottoscrizione di Azure per creare l'associazione."
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
	ms.date="09/16/2016"
	ms.author="cjiang"/>

# Associare un tenant di Office 365 con una sottoscrizione di Azure
Scenari: in passato sono state acquistate separatamente la sottoscrizione di Azure e l'abbonamento a Office 365 e si vuole poter accedere al tenant di Office 365 dalla sottoscrizione di Azure. Questo articolo mostra come è facile ottenere questo risultato.

> [AZURE.NOTE] Questo articolo non si applica ai clienti con Contratto Enterprise.

## Indicazioni rapide
Se si vuole associare il tenant di Office 365 alla sottoscrizione di Azure, usare l'account Azure per aggiungere il tenant di Office 365 e quindi associare la sottoscrizione di Azure al tenant di Office 365. Vedere la procedura dettagliata.

## Procedura dettagliata
In questo scenario Kelley Wall è un utente che ha una sottoscrizione di Azure con l'account kelly.wall@outlook.com. Kelley ha anche un abbonamento a Office 365 con l'account kelley.wall@contoso.onmicrosoft.com. Ora Kelley vuole accedere al tenant di Office 365 con la sottoscrizione di Azure.

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**Prerequisiti:**

- Sono necessarie le credenziali dell'amministratore del servizio della sottoscrizione di Azure. I coamministratori non possono eseguire un subset dei passaggi.
- Sono necessarie le credenziali di un amministratore globale del tenant di Office 365.
- L'indirizzo di posta elettronica dell'amministratore del servizio deve essere incluso nel tenant di Office 365.
- L'indirizzo di posta elettronica dell'amministratore del servizio deve essere diverso da quello di un amministratore globale del tenant di Office 365.
- Se si usa un indirizzo di posta elettronica che corrisponde a un account Microsoft e a un account aziendale, è consigliabile modificare temporaneamente le impostazioni dell'amministratore del servizio della sottoscrizione di Azure in modo da usare un altro account Microsoft. Questo ridurrà le limitazioni note della procedura. È possibile creare un nuovo account Microsoft nella [pagina Crea account Microsoft](https://signup.live.com/).

	Per modificare l'amministratore del servizio, seguire questa procedura:

	1. Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions).
	2. Selezionare la sottoscrizione da modificare.
	3. Sul lato destro fare clic su **Modifica i dettagli della sottoscrizione**.

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Per associare il tenant di Office 365 con la sottoscrizione di Azure, seguire questa procedura:

1. 	Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore del servizio.
2.	Nel riquadro sinistro fare clic su **ACTIVE DIRECTORY**.

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] Il tenant di Office 365 non dovrebbe essere visualizzato. Se è presente, andare al passaggio successivo.

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Aggiungere il tenant di Office 365 alla sottoscrizione di Azure.
	1. Fare clic su **NUOVO** > **DIRECTORY** > **CREAZIONE PERSONALIZZATA**.

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. Nella pagina**Aggiungi directory** selezionare **Utilizza directory esistente** in **DIRECTORY**, fare clic per selezionare **È possibile uscire ora** e quindi fare clic su **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. Dopo la disconnessione, accedere con le credenziali dell'amministratore globale del tenant di Office 365.

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. Fare clic su **Continue**.

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. Fare clic su **Esci ora**.

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. Accedere al [portale di gestione degli account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore del servizio.

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. Nel dashboard è visualizzato il tenant di Office 365.

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Cambiare la directory associata alla sottoscrizione di Azure.

	1. Fare clic su **Impostazioni**.

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Selezionare la sottoscrizione di Azure e quindi fare clic su **MODIFICA DIRECTORY**.

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. Fare clic su **Avanti** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] Verrà visualizzato un avviso per indicare che tutti i coamministratori verranno rimossi.

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] Verranno rimossi anche tutti gli utenti con il ruolo [Controllo degli accessi in base al ruolo](./active-directory/role-based-access-control-configure.md) e l'accesso assegnato nei gruppi di risorse esistenti. Tuttavia, il messaggio di avviso visualizzato cita solo la rimozione di coamministratori.

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. Fare clic su **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Ora è possibile aggiungere gli account aziendali di Office 365 come coamministratori del tenant AAD.

	1. Fare clic sulla scheda **AMMINISTRATORI** e quindi su **AGGIUNGI**.

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Immettere un account aziendale del tenant di Office 365, fare clic per selezionare la sottoscrizione di Azure e quindi fare clic su **Completa** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. Tornare alla scheda **AMMINISTRATORI** dove l'account aziendale sarà visualizzato come coamministratore.

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. È quindi possibile testare l'accesso come coamministratore.

	1. Accedere al portale di gestione degli account.
	2. Aprire il [portale di gestione degli account](https://account.windowsazure.com/subscriptions) o il [portale di Azure](https://portal.azure.com/).
	3. Se la pagina di accesso di Azure include un collegamento **Accedere con il proprio account aziendale**, fare clic sul collegamento. In caso contrario, ignorare questo passaggio.

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. Immettere le credenziali del coamministratore e quindi fare clic su **Accedi**.

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## Passaggi successivi
Esistono scenari correlati dove è già disponibile un abbonamento a Office 365 e si è pronti per una sottoscrizione di Azure, ma si vuole usare uno o più account utente di Office 365 esistenti per la sottoscrizione di Azure. Scenario alternativo: è disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente. Per informazioni su come eseguire queste attività, vedere [Usare l'account Office 365 esistente con la sottoscrizione di Azure o viceversa](billing-use-existing-office-365-account-azure-subscription.md).

<!---HONumber=AcomDC_0928_2016-->