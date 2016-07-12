<properties
	pageTitle="Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni| Microsoft Azure"
	description="Illustra come gli utenti possono partecipare ad Azure AD tramite il menu Impostazioni."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni
Se si sta usando Windows 7 o Windows 8 e il computer o il dispositivo è stato aggiornato a Windows 10, è possibile eseguire le aggiunte ad Azure Active Directory (Azure AD) tramite il menu Impostazioni.

## Per eseguire le aggiunte ad Azure AD dal menu Impostazioni


1. Dal menu **Start** fare clic sul charm **Impostazioni**.
2. Da **Impostazioni** selezionare **Sistema**->**Informazioni**->**Aggiungi ad Azure AD**.
<center>
![Join Azure AD from the Settings menu](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Fare clic su **Continua** nella finestra del messaggio Aggiungi ad Azure AD.
<center>
![Join Azure AD message window](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Fornire le credenziali di accesso. Questa esperienza di accesso includerà tutti i passaggi necessari per completare l'autenticazione. Se si fa parte di un tenant federato, l'amministratore fornirà l’esperienza di federazione ospitata dall'organizzazione.
<center>
![Provide sign-in credentials](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se l'organizzazione ha configurato Azure Multi-Factor Authentication per l'aggiunta ad Azure AD, fornire il secondo fattore prima di continuare.
6. Fare clic su **Accetta** nella schermata **Consenti la gestione di questo dispositivo**.
7. Verrà visualizzato il messaggio "Il dispositivo risulta ora aggiunto all'organizzazione in Azure AD".


## Informazioni aggiuntive
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0629_2016-->