<properties
	pageTitle="Gestire i gruppi ai quali appartiene il proprio gruppo in anteprima di Azure Active Directory | Microsoft Azure"
	description="I gruppi possono contenere altri gruppi in Azure Active Directory. Di seguito viene illustrato come gestire queste appartenenze."
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


# Gestire i gruppi di cui il proprio gruppo è membro in anteprima di Azure Active Directory

I gruppi possono contenere altri gruppi in anteprima di Azure Active Directory. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) Di seguito viene illustrato come gestire queste appartenenze.

## Come è possibile trovare i gruppi di cui il gruppo dell'utente è un membro?

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2.  Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

  ![Apertura di Gestione utenti](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

  ![Apertura del pannello Gruppi](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare un gruppo.

5. Nel pannello **Gruppo - *nomegruppo*** selezionare **Appartenenza ai gruppi**.

  ![Apertura del pannello Appartenenza ai gruppi](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Per aggiungere il gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare il comando **Aggiungi**.

7. Selezionare un gruppo nel pannello **Seleziona gruppo** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del pannello. È possibile aggiungere il gruppo a un solo gruppo alla volta. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.

  ![Aggiungere l'appartenenza a un gruppo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Per rimuovere il proprio gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare un gruppo.

9. Nel pannello ***nomegruppo*** selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.

  ![Comando Rimuovi appartenenza](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Al termine della modifica delle appartenenze dei gruppi per il proprio gruppo, selezionare **Salva**.


## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->