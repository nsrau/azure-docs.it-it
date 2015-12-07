<properties 
	pageTitle="Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni| Microsoft Azure" 
	description="Illustra come gli utenti possono partecipare ad Azure AD tramite il menu Impostazioni." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni
Se si sta già utilizzando Windows 7 o 8 e il computer viene aggiornato a Windows 10, è possibile effettuare l’aggiunta ad Azure AD ad tramite il menu Impostazioni.

Per aggiungere Azure AD nel menu impostazioni
-----------------------------------------------------------------------------------------------

1. Dal menu Start, fare clic sul charm Impostazioni.
2. Da Impostazioni->**Sistema**->**Informazioni**->**Aggiungi ad Azure AD**
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Fare clic su **Continua** nella finestra del messaggio Aggiunta ad Azure AD.
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Fornire le credenziali di accesso. Questa esperienza di accesso includerà tutti i passaggi necessari per completare l'autenticazione. Se si fa parte di un tenant federato, l'amministratore fornirà l’esperienza di federazione ospitata dall'organizzazione.
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Se l'organizzazione ha configurato la Multi-Factor Authentication per la partecipazione a Azure AD, è necessario fornire il secondo fattore prima di poter continuare.
6. Fare clic su **Accetta** nella schermata **Consenti la gestione di questo dispositivo**.
7. Verrà visualizzato il messaggio "Il dispositivo risulta ora aggiunto all'organizzazione in Azure AD".


## Informazioni aggiuntive
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->