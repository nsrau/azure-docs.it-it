
<properties
	pageTitle="Riferimento tecnico: accesso condizionale alle app di Azure AD | Microsoft Azure"
	description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="03/30/2016"
	ms.author="femila"/>

# Riferimento tecnico: accesso condizionale alle app di Azure AD

## Servizi abilitati con l'accesso condizionale
Le regole di accesso condizionale sono supportate in diversi tipi di applicazioni di Azure AD, inclusi i seguenti:

- Applicazioni federate dalla raccolta di applicazioni di Azure AD
- Applicazioni con accesso Single Sign-On basato su password dalla raccolta di applicazioni di Azure AD
- Applicazioni registrate con il proxy di applicazione di Azure
- Applicazioni line-of-business e multi-tenant sviluppate registrate con Azure AD
- Visual Studio Online
- App remote di Azure

## Abilitare le regole di accesso

Ogni regola può essere abilitata o disabilitata sulla base delle singole applicazioni. Quando le regole sono impostate su **ON** , verranno abilitate e applicate per gli utenti che accedono all'applicazione. Quando sono impostate su **OFF**, non verranno usate e non avranno alcun impatto sull'esperienza di accesso degli utenti.

## Applicazione di regole a utenti specifici
Le regole possono essere applicate a specifici set di utenti in base al gruppo di sicurezza impostando **Applica a**. **Applica a** può essere impostata su **Tutti gli utenti** o **Gruppi**. Se è impostata su **Tutti gli utenti**, le regole verranno applicate a qualsiasi utente autorizzato ad accedere all'applicazione. L'opzione **Gruppi** consente di selezionare gruppi di sicurezza e di distribuzione specifici. Le regole verranno applicate solo a questi gruppi.

  Quando si distribuisce una regola, la regola viene in genere applicata inizialmente a un set limitato di utenti appartenenti a gruppi pilota. Una volta completata, la regola può essere applicata a **Tutti gli utenti**. In questo modo la regola verrà applicata a tutti gli utenti dell'organizzazione.

È anche possibile escludere gruppi selezionati dai criteri usando l'opzione **Escludi**. I membri di questi gruppi saranno esentati, anche se appartengono a un gruppo incluso.

## Reti di tipo "ufficio"


Le regole di accesso condizionale che usano una rete di tipo "ufficio" si basano su intervalli IP attendibili configurati in Azure AD. Queste regole includono:

- Richiedi autenticazione a più fattori quando non al lavoro
- Blocca l'accesso quando non al lavoro

Gli intervalli IP attendibili possono essere configurati nella [pagina di configurazione dell'autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication-whats-next.md). I criteri di accesso condizionale useranno gli intervalli configurati in ogni richiesta di autenticazione e in ogni emissione di token per valutare le regole. L'attestazione relativa al solo interno della rete aziendale non viene usata, perché non è disponibile per sessioni di durata maggiore, ad esempio l'aggiornamento di token nelle applicazioni per dispositivi mobili.

## Regole per singole applicazioni
Le regole vengono configurate per le singole applicazioni, consentendo la protezione di servizi ad alto valore, senza alcun impatto sull'accesso ad altri servizi. Le regole di accesso condizionale possono essere configurate nella scheda **Configura** dell'applicazione.

Le regole attualmente disponibili sono le seguenti:

- **Richiedi autenticazione a più fattori**
 - Tutti gli utenti a cui viene applicato questo criterio devono effettuare almeno una volta l'autenticazione a più fattori.
 
- **Richiedi autenticazione a più fattori quando non al lavoro**
 - Se viene applicato questo criterio, tutti gli utenti devono aver effettuato almeno una volta l'autenticazione a più fattori in caso di accesso al servizio da una posizione remota non lavorativa. Se si spostano da una posizione in sede a una posizione remota, dovranno effettuare l'autenticazione a più fattori all'accesso al servizio.
 
- **Blocca l'accesso quando non al lavoro**
 - Quando gli utenti si spostano dalla posizione in sede a una posizione remota, verranno bloccati se viene applicato il criterio "Blocca l'accesso quando non al lavoro". Nella posizione in sede, saranno nuovamente autorizzati ad accedere.


## Argomenti correlati

- [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md)
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0406_2016-->