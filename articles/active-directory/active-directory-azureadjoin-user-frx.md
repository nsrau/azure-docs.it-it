<properties
	pageTitle="Configurazione di un nuovo dispositivo con Azure AD durante l'installazione| Microsoft Azure"
	description="Argomento che spiega come configurare Aggiunta di Azure AD durante la procedura di configurazione iniziale guidata."
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

# Configurazione di un nuovo dispositivo con Azure AD durante l'installazione

In Windows 10 gli utenti finali possono aggiungere il proprio dispositivo ad Azure Active Directory (Azure AD) al completamento dell'installazione. Questo consente alle organizzazioni di distribuire dispositivi standard per i loro dipendenti o studenti oppure lasciare che scelgano i propri dispositivi (CYOD). Se nel dispositivo è installato Windows 10 Professional o Windows 10 Enterprise Edition, per impostazione predefinita viene avviato il processo di installazione dei dispositivi di proprietà dell'azienda.

## Per aggiungere un dispositivo ad Azure AD


1. Quando si accende il nuovo dispositivo e viene avviato il processo di installazione, viene visualizzato il messaggio **Preparazione**. Seguire le istruzioni per configurare il dispositivo.
2. Iniziare personalizzando il paese e la lingua. Quindi accettare le Condizioni di licenza software Microsoft. ![Personalizzare il paese](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Selezionare la rete che si desidera usare per la connessione a Internet.
4. Selezionare se si usa un dispositivo personale o di proprietà dell'azienda. Se il dispositivo è di proprietà dell'azienda, fare clic su **Questo dispositivo appartiene alla mia azienda**. Verrà avviata l'esperienza di aggiunta ad Azure AD. Di seguito è riportata la schermata che viene visualizzata se si usa Windows 10 Professional.
<center>
![Schermata A chi appartiene questo PC?](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	Immettere le credenziali fornite dall'organizzazione.
<center>
![Schermata di accesso](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	Dopo aver immesso il nome utente, viene trovato il tenant corrispondente in Azure AD. Se si è in un dominio federato, si verrà reindirizzati al server del servizio token di sicurezza locale, ad esempio Active Directory Federation Services (AD FS).
7. Se si è in un dominio non federato, immettere le credenziali direttamente nella pagina ospitata da Azure AD. Se sono state configurate le informazioni personalizzate distintive dell'azienda, vengono visualizzati anche il logo dell'organizzazione e la documentazione di supporto.
8.	Viene richiesto di effettuare un'autenticazione a più fattori. La verifica può essere configurata da un amministratore IT.
9.	Azure AD verifica se l'utente o il dispositivo richiede la registrazione nella gestione dei dispositivi mobili.
10.	Windows registra il dispositivo nella directory dell'organizzazione in Azure AD e lo inserisce nella gestione dei dispositivi mobili, se appropriato.
11.	Se si è un utente gestito, Windows visualizza il desktop tramite l'accesso automatico.
12.	Se si è un utente federato, viene visualizzata la schermata di accesso di Windows che consente di immettere le proprie credenziali.

> [AZURE.NOTE] L'aggiunta a un dominio Active Directory di Windows Server in locale nella configurazione guidata di Windows non è supportata. Se quindi si prevede di aggiungere un computer a un dominio, è necessario selezionare il collegamento **Configurare Windows con un account locale**. Sarà quindi possibile aggiungere il dominio dalle impostazioni del computer come in precedenza.

## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0629_2016-->