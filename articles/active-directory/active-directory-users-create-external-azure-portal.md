<properties
	pageTitle="Aggiungere utenti da altre directory o società partner in anteprima di Azure Active Directory | Microsoft Azure"
	description="Illustra come aggiungere utenti o modificare le informazioni sugli utenti in Azure Active Directory, inclusi gli utenti esterni e guest."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Aggiungere utenti da altre directory o società partner in anteprima di Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-users-create-external-azure-portal.md)
- [Portale di Azure classico](active-directory-create-users-external.md)

Questo articolo illustra come aggiungere utenti da altre directory in anteprima di Azure Active Directory (Azure AD) o da società partner. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) Per informazioni sull'aggiunta di nuovi utenti nell'organizzazione e di utenti che possiedono account Microsoft, vedere [Aggiungere nuovi utenti o utenti con account Microsoft in Azure Active Directory](active-directory-users-create-azure-portal.md). Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## Aggiungere un utente

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2.  Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

    ![Apertura di Gestione utenti](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  Nel pannello **Utenti e gruppi** selezionare **Utenti**, quindi selezionare **Aggiungi**.

    ![Selecting the Add command](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Nel pannello **Utente** fornire un nome visualizzato in **Nome** e il nome di accesso dell'utente in **Nome utente**.

5. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.

6. Facoltativamente, selezionare **Profilo** per aggiungere nome e cognome, posizione e nome del reparto.

	![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- Selezionare **Gruppi** per aggiungere l'utente a uno o più gruppi.

		![Adding a user to groups](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- Selezionare **Ruolo aziendale** per assegnare l'utente a un ruolo dall'elenco **Ruoli**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

		![Assigning a user to a role](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Selezionare **Create**.

8. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

> [AZURE.IMPORTANT] Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si aggiunge un account utente:
>
> - Per aggiungere account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, aggiungere **prima**, ad esempio, geoffgrisso@contoso.onmicrosoft.com **seguito** da geoffgrisso@contoso.com.
> - **Non** aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com. Quest'ordine è importante e può essere complesso da annullare.

Se si modificano le informazioni per un utente la cui identità è sincronizzata con il servizio di Active Directory locale, non sarà possibile modificare le informazioni sull'utente nel portale di Azure classico. Per modificare le informazioni sull'utente, usare gli strumenti di gestione del servizio Active Directory locale.


## Passaggi successivi

- [Aggiungere un utente](active-directory-users-create-azure-portal.md)
- [Reimpostare la password di un utente nel nuovo portale di Azure](active-directory-users-reset-password-azure-portal.md)
- [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Modificare le informazioni di lavoro di un utente](active-directory-users-work-info-azure-portal.md)
- [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
- [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)

<!----HONumber=AcomDC_0914_2016-->