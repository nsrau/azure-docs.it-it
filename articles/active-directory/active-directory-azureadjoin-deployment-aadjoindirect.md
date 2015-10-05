<properties 
	pageTitle="Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD | Microsoft Azure" 
	description="Vengono elencati e illustrati i diversi scenari di distribuzione disponibili per l'unione di Azure AD." 
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

# Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD 

## Scenari di utilizzo per Aggiunta ad Azure AD
Scenario 1: Aziende che operano prevalentemente nel cloud
--------------------------------------------------------
La funzionalità di aggiunta ad Azure AD può risultare utile per le aziende che già operano e gestiscono le identità nel cloud o che prevedono di eseguire la migrazione al cloud a breve. È possibile usare gli account creati in Azure AD per accedere a Windows 10. Gli utenti possono aggiungere i propri computer e dispositivi ad Azure AD tramite la [procedura di configurazione iniziale guidata](active-directory-azureadjoin-user-frx.md) o tramite l'[esperienza Impostazioni](active-directory-azureadjoin-user-upgrade.md) e usufruire dell'accesso Single Sign-On per accedere alle proprie risorse cloud, ad esempio Office 365, sia dal browser che dalle applicazioni Office.

Scenario 2: Istituti di istruzione ---------------------------------------------------------------------------------- Gli istituti di istruzione hanno in genere due tipi di utenti: docenti e studenti. I docenti sono considerati membri dell'organizzazione a più lungo termine, quindi è consigliabile creare e assegnare loro account locali. Gli studenti invece sono membri dell'organizzazione a più breve termine e possono quindi essere gestiti in Azure AD, con un livello di directory esteso nel cloud anziché in locale. Gli studenti possono accedere a Windows usando l'account Azure AD e accedere alle risorse di Office 365 nelle applicazioni Office.

Scenario 3: Negozi
---------------------------------------------------------------------------------------
I negozi impiegano lavoratori stagionali e dipendenti a lungo termine. È quindi possibile creare account locali per i dipendenti a tempo pieno non stagionali che usano computer aggiunti a un dominio. I lavoratori stagionali sono invece membri dell'organizzazione a più breve termine e quindi è preferibile gestirli con licenze utente che possono essere spostate con maggiore facilità. La creazione di questi utenti nel cloud con licenze di Office 365 permette loro di ottenere i vantaggi dell'accesso alle applicazioni di Windows e Office con un account Azure AD usufruendo allo stesso tempo di una maggiore mobilità delle licenze al termine del rapporto di lavoro.

Scenario 4: Scenari aggiuntivi
------------------------------------------------------------------------------------------
Oltre agli scenari specifici descritti sopra, si può decidere di usare comunque la funzionalità di aggiunta ad Azure AD perché offre un'esperienza di aggiunta e di gestione dei dispositivi semplificata in Azure AD, la registrazione MDM automatica e l'accesso Single Sign-On alle risorse di Azure AD e locali.


##Considerazioni sulla distribuzione per Aggiunta di Azure AD

Abilitazione degli utenti per l'aggiunta di dispositivi di proprietà della società direttamente ad Azure AD
-----------------------------------------------------------------------------------------

Le aziende possono fornire account di tipo solo cloud a organizzazioni e società partner. Questi partner possono quindi usufruire dell'accesso Single Sign-On semplificato alle app e alle risorse della propria azienda. Questo scenario si applica agli utenti che usano i propri dispositivi per accedere principalmente alle risorse nel cloud, ad esempio Office 365 e app SaaS che si basano su Azure AD per l'autenticazione.

### Prerequisiti
**A livello aziendale (amministratore)**

*	Sottoscrizione di con Azure Active Directory  

**A livello dell'utente**

*	Windows 10 (SKU Professional ed Enterprise)

### Attività dell'amministratore
* [Configurazione della registrazione del dispositivo e dell'autenticazione a più fattori](active-directory-azureadjoin-setup.md)

### Attività dell'utente
* [Configurazione di un nuovo dispositivo Windows 10 con Azure AD durante l'installazione](active-directory-azureadjoin-user-frx.md)
* [Configurazione di un dispositivo Windows 10 con Azure AD da Impostazioni](active-directory-azureadjoin-user-upgrade.md)
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)
  


## Abilitazione delle funzionalità BYOD per Windows 10 all'interno dell'organizzazione
È possibile configurare utenti e dipendenti in modo che usino i propri dispositivi Windows per accedere alle app e alle risorse aziendali. Gli utenti possono aggiungere i propri account Azure AD (account aziendali) a un dispositivo Windows personale per accedere alle risorse aziendali garantendo la sicurezza e la conformità.

### Prerequisiti
**A livello aziendale (amministratore)**

*	Sottoscrizione di Azure AD

**A livello dell'utente**

*	Windows 10 (SKU Professional ed Enterprise)


### Attività dell'amministratore

* [Configurazione della registrazione del dispositivo e dell'autenticazione a più fattori](active-directory-azureadjoin-setup.md)

### Attività dell'utente
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)


## Informazioni aggiuntive
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->