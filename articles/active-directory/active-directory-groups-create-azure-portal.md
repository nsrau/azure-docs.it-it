<properties
	pageTitle="Creare un gruppo in anteprima di Azure Active Directory | Microsoft Azure"
	description="Come creare un gruppo in Azure Active Directory e aggiungere utenti (membri) al gruppo"
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


# Creare un nuovo gruppo in anteprima di Azure Active Directory

Questo articolo illustra come creare e popolare un nuovo gruppo in anteprima di Azure Active Directory (Azure AD). [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) Usare un gruppo per eseguire attività di gestione come l'assegnazione di licenze o autorizzazioni per diversi utenti o dispositivi contemporaneamente.

## Come si crea un gruppo?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

  ![Apertura di Gestione utenti](./media/active-directory-groups-create-azure-portal/search-user-management.png)  

3. Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

  ![Apertura del pannello Gruppi](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)  

4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare il comando **Aggiungi**.

  ![Selezione del comando Aggiungi](./media/active-directory-groups-create-azure-portal/add-group-command.png)  

5. Nel pannello **Gruppo** aggiungere un nome e una descrizione per il gruppo.

6. Per selezionare i membri da aggiungere al gruppo, selezionare **Assegnato** nella casella **Tipo di appartenenza** e quindi selezionare **Membri**. Per altre informazioni su come gestire l'appartenenza di un gruppo in modo dinamico, vedere [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-users-azure-portal.md) o [Gestire le regole dinamiche per i dispositivi in un gruppo](active-directory-groups-dynamic-devices-azure-portal.md).

  ![Selezione dei membri da aggiungere](./media/active-directory-groups-create-azure-portal/select-members.png)  

5. Nel pannello **Membri** selezionare uno o più utenti o dispositivi da aggiungere al gruppo e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello per aggiungerli al gruppo. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.

6. Dopo avere aggiunto i membri al gruppo, selezionare **Crea** nel pannello **Gruppo**.

  ![Creare una conferma del gruppo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)  




## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->