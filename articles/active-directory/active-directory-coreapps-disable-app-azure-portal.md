<properties
	pageTitle="Disabilitare gli accessi utente per un'app aziendale in anteprima di Azure Active Directory | Microsoft Azure"
	description="Come disabilitare un'applicazione aziendale in modo che gli utenti non possano accedervi in Azure Active Directory"
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


# Disabilitare gli accessi utente per un'app aziendale in anteprima di Azure Active Directory

Disabilitare un'applicazione aziendale in modo da impedire l'accesso degli utenti in anteprima di Azure Active Directory (Azure AD) è un'operazione semplice. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale. Nell'anteprima corrente è necessario essere amministratore globale della directory.

## Come è possibile disabilitare l'accesso degli utenti?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.

3. Nel pannello **Azure Active Directory - *nomedirectory***, vale a dire il pannello Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

	![Apertura di app aziendali](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Nel pannello **Applicazioni aziendali** selezionare **All applications** (Tutte le applicazioni). Viene visualizzato un elenco di app che è possibile gestire.

5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.

6. Nel pannello ***nomeapp***, vale a dire il pannello con il nome dell'app selezionata nel titolo, selezionare **Proprietà**.

	![Selezione del comando All applications (Tutte le applicazioni)](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Nel pannello ***nomeapp*** **-Proprietà** selezionare **No** per **Enabled for users to sign-in?** (Abilitato per l'accesso degli utenti?).

8. Selezionare il comando **Salva**.

## Passaggi successivi

- [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
- [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->