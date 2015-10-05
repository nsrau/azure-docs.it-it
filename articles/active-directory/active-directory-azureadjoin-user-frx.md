<properties 
	pageTitle="Configurazione di un nuovo dispositivo con Azure AD durante l'installazione| Microsoft Azure" 
	description="Argomento che spiega come configurare Aggiunta di Azure AD durante la procedura di configurazione iniziale guidata." 
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
	ms.date="08/02/2015" 
	ms.author="femila"/>

# Configurazione di un nuovo dispositivo con Azure AD durante l'installazione

In Windows 10, gli utenti finali possono aggiungere il proprio dispositivo ad Azure AD nella First Run Experience (FRX). Questo consente alle organizzazioni di distribuire dispositivi con wrapping di riduzione per i loro dipendenti o consente loro di scegliere i propri dispositivi (CYOD). Se si installa l’SKU Professional o Enterprise per Windows 10, l'esperienza utilizza le impostazioni di configurazione predefinite per i dispositivi di proprietà dell'azienda.

Per aggiungere un dispositivo ad Azure AD
-----------------------------------------------------------------------

1. Dopo la **Fase preliminare**, viene richiesta l'Installazione guidata.
2. Iniziare personalizzando il paese e la lingua, accettare il Contratto di Licenza con l'utente finale (EULA) e accedere a Internet.
<center>! [] (. / media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)</center>
3. Selezionare la rete per la connessione a Internet.
4. Selezionare se si tratta di un dispositivo personale o di proprietà dell'azienda:
5. Fare clic su **Questo dispositivo appartiene alla mia organizzazione**. Verrà avviata l'esperienza di aggiunta ad Azure AD. Di seguito è riportata una schermata che verrà visualizzata nell'SKU Professional di Windows 10. 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	Immettere le credenziali fornite dall'organizzazione.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	Dopo aver immesso il nome utente, un tenant corrispondente si trova in Azure AD. Se si è in un dominio federato, si verrà reindirizzati al server del servizio token di sicurezza locale (ad esempio AD FS). Se si è un utente in un dominio non federato, è necessario immettere le credenziali direttamente nella pagina ospitata da Azure AD. Inoltre verrà visualizzato il logo dell'organizzazione e la documentazione di supporto se sono state configurate le informazioni personalizzate distintive dell'azienda.
8.	Verrà quindi richiesto di effettuare una verifica Multi-Factor Authentication. Questa può essere configurata dal reparto IT.
9.	Azure AD quindi controllerà se questo utente/dispositivo richiede la registrazione nella gestione dei dispositivi mobili (MDM). 
10.	Windows quindi registrerà il dispositivo nella directory dell'organizzazione in Azure AD e procederà con la registrazione in MDM.
11.	Al termine dell’operazione, se si è un utente gestito, Windows concluderà la procedura di installazione e porterà l'utente al desktop tramite l’accesso automatico.
12.	Se si è un utente federato, si accederà alla schermata di accesso di Windows e sarà necessario immettere le proprie credenziali per l’accesso.

> [AZURE.NOTE]L’aggiunta a un dominio di Active Directory locale nella configurazione guidata di Windows non è supportata. Pertanto, se si prevede di aggiungere un computer a un dominio, è necessario selezionare il collegamento "Configurare Windows con un account locale". È quindi possibile aggiungere il dominio da Impostazioni PC come in precedenza.

## Informazioni aggiuntive
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->