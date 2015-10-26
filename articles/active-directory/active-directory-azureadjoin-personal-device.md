<properties
	pageTitle="Aggiunta di un dispositivo personale all'organizzazione| Microsoft Azure"
	description="Un argomento che illustra come gli utenti possono registrare i computer Windows 10 personali nella rete aziendale."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="femila"/>

# Aggiunta di un dispositivo personale all'organizzazione

Per aggiungere un dispositivo Windows 10 personale all'organizzazione
--------------------------------------------------------------------------------------------
1.	Nel menu **Start** selezionare **Impostazioni**.
2.	Selezionare **Account**, quindi fare clic su **Account**.
3.	Fare clic su **Aggiungi account aziendale o dell'istituto di istruzione** e quindi digitare l'account dell'organizzazione.
4.	Quindi sarà possibile accedere alla pagina di accesso dell'organizzazione. Immettere nome utente e password e fare clic su **OK**.
5.	Verrà quindi richiesto di effettuare una verifica Multi-Factor Authentication. Questa può essere configurata dal reparto IT.
6.	Azure AD quindi controllerà se questo utente/dispositivo richiede la registrazione nella gestione dei dispositivi mobili (MDM).
7.	Windows quindi registrerà il dispositivo nella directory dell'organizzazione in Azure AD e procederà con la registrazione in MDM.
8.	Al termine dell’operazione, se si è un utente gestito, Windows concluderà la procedura di installazione e porterà l'utente al desktop tramite la schermata di accesso automatico.
9.	Se si è un utente federato, si accederà alla schermata di accesso di Windows e sarà necessario immettere le proprie credenziali.

## Informazioni aggiuntive
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md)
* [Informazioni sugli scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Oct15_HO3-->