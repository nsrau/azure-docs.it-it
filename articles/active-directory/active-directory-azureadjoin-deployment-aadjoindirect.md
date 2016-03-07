<properties
	pageTitle="Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD | Microsoft Azure"
	description="Questo argomento illustra come gli amministratori possono configurare la funzionalità Aggiunta ad Azure AD per gli utenti finali (dipendenti, studenti o altri utenti). Vengono inoltre illustrati i diversi scenari reali per l'utilizzo di Azure AD Join."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="02/10/2016"

	ms.author="femila"/>

# Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD

## Scenari di utilizzo per Aggiunta ad Azure AD
### Scenario 1: Aziende che operano prevalentemente nel cloud

Azure Active Directory Join (Azure AD Join) può risultare utile per le aziende che già operano e gestiscono le identità nel cloud o che prevedono di passare al cloud a breve. È possibile usare gli account creati in Azure AD per accedere a Windows 10. Gli utenti possono aggiungere i propri computer ad Azure AD tramite la [procedura di configurazione iniziale guidata](active-directory-azureadjoin-user-frx.md) o tramite [il menu Impostazioni](active-directory-azureadjoin-user-upgrade.md). Gli utenti possono anche usufruire dell'accesso Single Sign-On (SSO) a risorse cloud come Office 365, nel browser o nelle applicazioni Office.

### Scenario 2: Istituti di istruzione

Gli istituti di istruzione hanno in genere due tipi di utenti: docenti e studenti. I docenti sono considerati membri dell'organizzazione a lungo termine. Per loro è consigliabile creare account locali. Gli studenti sono invece membri dell'organizzazione a breve termine e i loro account possono essere gestiti in Azure AD. Ciò significa che è possibile eseguire il push della scalabilità della directory nel cloud anziché archiviarla in locale. Significa anche che gli studenti possono accedere a Windows con i propri account Azure AD e alle risorse di Office 365 nelle applicazioni Office.

### Scenario 3: Negozi

I negozi impiegano lavoratori stagionali e dipendenti a lungo termine. In genere si creano account locali e si usano computer aggiunti a un dominio per i dipendenti a tempo pieno e a lungo termine. I lavoratori stagionali sono invece membri dell'organizzazione a breve termine ed è quindi preferibile gestire i relativi account in posizioni dove è possibile spostare le licenze utente con maggiore facilità. Quando si creano account utente nel cloud con licenze di Office 365, i relativi utenti ottengono i vantaggi dell'accesso alle applicazioni di Windows e Office con un account Azure AD e si usufruisce allo stesso tempo di una maggiore flessibilità delle licenze al termine del rapporto di lavoro.

### Scenario 4: Scenari aggiuntivi

Oltre ai vantaggi descritti sopra, si usufruisce della funzionalità di aggiunta dei dispositivi degli utenti ad Azure AD grazie all'esperienza di aggiunta semplificata, all'esperienza di gestione dei dispositivi efficiente, alla registrazione della gestione automatica dei dispositivi mobili, alle risorse per l'accesso Single Sign-On ad Azure AD e alle risorse locali.


## Considerazioni sulla distribuzione per Azure AD Join

### Abilitare gli utenti per l'aggiunta di dispositivi di proprietà della società direttamente ad Azure AD


Le aziende possono fornire account di tipo solo cloud a organizzazioni e società partner. Questi partner possono quindi accedere facilmente alle app e alle risorse aziendali tramite l'accesso Single Sign-On. Questo scenario si applica agli utenti che accedono alle risorse principalmente nel cloud, ad esempio Office 365 e app SaaS che si basano su Azure AD per l'autenticazione.

### Prerequisiti
**A livello aziendale (amministratore)**

*	Sottoscrizione di Azure con Azure Active Directory  

**A livello dell'utente**

*	Windows 10 (Professional ed Enterprise)

### Attività dell'amministratore
* [Configurare la registrazione dei dispositivi](active-directory-azureadjoin-setup.md)

### Attività dell'utente
* [Configurare un nuovo dispositivo Windows 10 con Azure AD durante l'installazione](active-directory-azureadjoin-user-frx.md)
* [Configurare un dispositivo Windows 10 con Azure AD dal menu Impostazioni](active-directory-azureadjoin-user-upgrade.md)
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)



## Abilitare le funzionalità BYOD per Windows 10 nell'organizzazione
È possibile configurare utenti e dipendenti in modo che usino i dispositivi Windows personali (BYOD, Bring Your Own Device) per accedere alle app e alle risorse aziendali. Gli utenti possono aggiungere i propri account Azure AD (account aziendali o dell'istituto di istruzione) a un dispositivo personale Windows per accedere alle risorse in totale sicurezza e conformità.

### Prerequisiti
**A livello aziendale (amministratore)**

*	Sottoscrizione di Azure AD

**A livello dell'utente**

*	Windows 10 (Professional ed Enterprise)


### Attività dell'amministratore

* [Configurare la registrazione dei dispositivi](active-directory-azureadjoin-setup.md)

### Attività dell'utente
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)


## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0224_2016-->