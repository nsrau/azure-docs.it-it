<properties
	pageTitle="Aggiungere nuovi utenti ad anteprima di Azure Active Directory | Microsoft Azure"
	description="Illustra come aggiungere nuovi utenti o modificare le informazioni sugli utenti in Azure Active Directory."
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


# Aggiungere nuovi utenti ad anteprima di Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-users-create-azure-portal.md)
- [Portale di Azure classico](active-directory-create-users.md)

Questo articolo illustra come aggiungere nuovi utenti all'organizzazione nell'anteprima di Azure Active Direstory (Azure AD). [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2.  Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

    ![Apertura di Gestione utenti](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Nel pannello **Utenti e gruppi** selezionare **Tutti gli utenti**, quindi selezionare **Aggiungi**.

    ![Selezione del comando Aggiungi](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Immettere i dettagli per l'utente, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "foo.onmicrosoft.com" del nome di dominio predefinito iniziale o un nome di dominio verificato, non federato, ad esempio "contoso.com".

5. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.

6. Eventualmente, è possibile aprire e compilare le informazioni contenute nel pannello **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

7.  Nel pannello **Utente** selezionare **Crea**.

8. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

## Passaggi successivi

- [Aggiungere un utente esterno](active-directory-users-create-external-azure-portal.md)
- [Reimpostare la password di un utente nel nuovo portale di Azure](active-directory-users-reset-password-azure-portal.md)
- [Modificare le informazioni di lavoro di un utente](active-directory-users-work-info-azure-portal.md)
- [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
- [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->