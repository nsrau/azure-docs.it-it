---
title: Documentazione tecnica sull&quot;accesso condizionale in Azure Active Directory | Documentazione Microsoft
description: Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l&quot;autenticazione dell&quot;utente e prima di consentire l&quot;accesso all&quot;applicazione. Se tali condizioni vengono soddisfatte, l&quot;utente viene autenticato e gli viene consentito l&quot;accesso all&quot;applicazione.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/20/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: a5c84d7ef67c49042eb85965593f44915a666df9


---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Documentazione tecnica sull'accesso condizionale di Azure Active Directory
## <a name="services-enabled-with-conditional-access"></a>Servizi abilitati con l'accesso condizionale
Le regole di accesso condizionale sono supportate in diversi tipi di applicazioni di Azure AD, inclusi i seguenti:

* Applicazioni federate dalla raccolta di applicazioni di Azure AD
* Applicazioni con accesso Single Sign-On basato su password dalla raccolta di applicazioni di Azure AD
* Applicazioni registrate con il proxy di applicazione di Azure
* Applicazioni line-of-business e multi-tenant sviluppate registrate con Azure AD
* Visual Studio Online
* App remote di Azure
* Dynamics CRM
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (include OneDrive for Business)

## <a name="enable-access-rules"></a>Abilitare le regole di accesso
Ogni regola può essere abilitata o disabilitata sulla base delle singole applicazioni. Quando le regole sono impostate su **ON** , verranno abilitate e applicate per gli utenti che accedono all'applicazione. Quando sono impostate su **OFF** , non verranno usate e non avranno alcun impatto sull'esperienza di accesso degli utenti.

## <a name="applying-rules-to-specific-users"></a>Applicazione di regole a utenti specifici
Le regole possono essere applicate a specifici set di utenti in base al gruppo di sicurezza impostando **Applica a**. L'opzione **Applica a** può essere impostata su **Tutti gli utenti** o su **Gruppi**. Se è impostata su **Tutti gli utenti**, le regole verranno applicate a qualsiasi utente autorizzato ad accedere all'applicazione. L'opzione **Gruppi** consente di selezionare specifici gruppi di sicurezza e distribuzione. Le regole verranno applicate solo a questi gruppi.

Quando si distribuisce una regola, tale regola viene in genere applicata inizialmente a un set limitato di utenti appartenenti a gruppi pilota. Una volta completata, la regola può essere applicata a **Tutti gli utenti**. In questo modo la regola verrà applicata a tutti gli utenti dell'organizzazione.

È anche possibile escludere gruppi selezionati dai criteri usando l'opzione **Escludi** . I membri di questi gruppi saranno esentati, anche se appartengono a un gruppo incluso.

## <a name="at-work-networks"></a>Reti di tipo "ufficio"
Le regole di accesso condizionale che utilizzano una rete di tipo "ufficio" si basano su intervalli di indirizzi IP attendibili configurati in Azure AD oppure utilizzano l'attestazione "rete aziendale". Queste regole includono:

* Richiedi autenticazione a più fattori quando non al lavoro
* Blocca l'accesso quando non al lavoro

Opzioni per specificare le reti di tipo "ufficio"

1. Configurare gli intervalli IP attendibili nella [pagina di configurazione dell'autenticazione Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md). I criteri di accesso condizionale useranno gli intervalli configurati in ogni richiesta di autenticazione e in ogni emissione di token per valutare le regole. 
2. Configurare l'utilizzo dell'attestazione "rete aziendale" con AD FS; questa opzione può essere utilizzata con la directory federata. [Ulteriori informazioni sulle attestazioni "rete aziendale"](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurare gli intervalli di indirizzi IP pubblici. Nella scheda di configurazione, è possibile impostare gli indirizzi IP pubblici per la propria directory. L'accesso condizionale li utilizzerà come indirizzi IP "ufficio"; in questo modo sarà possibile configurare intervalli aggiuntivi, al di sopra del limite di 50 indirizzi IP applicato dalla pagina di impostazione dell'autenticazione a più fattori.

## <a name="rules-based-on-application-sensitivity"></a>Regole basate sulla sensibilità dell'applicazione
Le regole vengono configurate per le singole applicazioni, consentendo la protezione di servizi ad alto valore, senza alcun impatto sull'accesso ad altri servizi. Le regole di accesso condizionale possono essere configurate nella scheda **Configura** dell'applicazione. 

Le regole attualmente disponibili sono le seguenti:

* **Richiedi autenticazione a più fattori**
  
  * Tutti gli utenti a cui viene applicato questo criterio devono effettuare almeno una volta l'autenticazione a più fattori.
* **Richiedi autenticazione a più fattori quando non al lavoro**
  
  * Se viene applicato questo criterio, tutti gli utenti devono aver effettuato almeno una volta l'autenticazione a più fattori in caso di accesso al servizio da una posizione remota non lavorativa. Se si spostano da una posizione in sede a una posizione remota, dovranno effettuare l'autenticazione a più fattori all'accesso al servizio.
* **Blocca l'accesso quando non al lavoro** 
  
  * Quando gli utenti si spostano dalla posizione in sede a una posizione remota, verranno bloccati se viene applicato il criterio "Blocca l'accesso quando non al lavoro".  Nella posizione in sede, saranno nuovamente autorizzati ad accedere.

## <a name="related-topics"></a>Argomenti correlati
* [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Jan17_HO1-->


