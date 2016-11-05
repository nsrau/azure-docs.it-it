---
title: Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD | Microsoft Docs
description: Questo argomento illustra come gli amministratori possono configurare la funzionalità Aggiunta ad Azure AD per gli utenti finali (dipendenti, studenti o altri utenti). Vengono inoltre illustrati i diversi scenari reali per l'utilizzo di Azure AD Join.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD
## <a name="usage-scenarios-for-azure-ad-join"></a>Scenari di utilizzo per Aggiunta ad Azure AD
### <a name="scenario-1:-businesses-largely-in-the-cloud"></a>Scenario 1: Aziende che operano prevalentemente nel cloud
Azure Active Directory Join (Azure AD Join) può risultare utile per le aziende che già operano e gestiscono le identità nel cloud o che prevedono di passare al cloud a breve. È possibile usare gli account creati in Azure AD per accedere a Windows 10. Gli utenti possono aggiungere i propri computer ad Azure AD tramite la [procedura guidata di configurazione iniziale](active-directory-azureadjoin-user-frx.md) o tramite [il menu Impostazioni](active-directory-azureadjoin-user-upgrade.md).  Gli utenti possono anche usufruire dell'accesso Single Sign-On (SSO) a risorse cloud come Office 365, nel browser o nelle applicazioni Office.

### <a name="scenario-2:-educational-institutions"></a>Scenario 2: Istituti di istruzione
Gli istituti di istruzione hanno in genere due tipi di utenti: docenti e studenti. I docenti sono considerati membri dell'organizzazione a lungo termine. Per loro è consigliabile creare account locali. Gli studenti sono invece membri dell'organizzazione a breve termine e i loro account possono essere gestiti in Azure AD. Ciò significa che è possibile eseguire il push della scalabilità della directory nel cloud anziché archiviarla in locale. Significa anche che gli studenti possono accedere a Windows con i propri account Azure AD e alle risorse di Office 365 nelle applicazioni Office.

### <a name="scenario-3:-retail-businesses"></a>Scenario 3: Negozi
I negozi impiegano lavoratori stagionali e dipendenti a lungo termine. In genere si creano account locali e si usano computer aggiunti a un dominio per i dipendenti a tempo pieno e a lungo termine. I lavoratori stagionali sono invece membri dell'organizzazione a breve termine ed è quindi preferibile gestire i relativi account in posizioni dove è possibile spostare le licenze utente con maggiore facilità. Quando si creano account utente nel cloud con licenze di Office 365, i relativi utenti ottengono i vantaggi dell'accesso alle applicazioni di Windows e Office con un account Azure AD e si usufruisce allo stesso tempo di una maggiore flessibilità delle licenze al termine del rapporto di lavoro.

### <a name="scenario-4:-additional-scenarios"></a>Scenario 4: Scenari aggiuntivi
Oltre ai vantaggi descritti sopra, si ottiene anche il vantaggio che gli utenti aggiungeranno i dispositivi ad Azure AD perché l'esperienza di aggiunta è semplificata, la gestione dei dispositivi efficiente, la registrazione della gestione dei dispositivi mobili è automatica ed è disponibile l'accesso Single Sign-On ad Azure AD e alle risorse locali.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Considerazioni sulla distribuzione per Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Abilitare gli utenti per l'aggiunta di dispositivi di proprietà della società direttamente ad Azure AD
Le aziende possono fornire account di tipo solo cloud a organizzazioni e società partner. Questi partner possono quindi accedere facilmente alle app e alle risorse aziendali tramite l'accesso Single Sign-On. Questo scenario si applica agli utenti che accedono alle risorse principalmente nel cloud, ad esempio Office 365 e app SaaS che si basano su Azure AD per l'autenticazione.

### <a name="prerequisites"></a>Prerequisiti
**A livello aziendale (amministratore)**

* Sottoscrizione di Azure con Azure Active Directory  

**A livello dell'utente**

* Windows 10 (Professional ed Enterprise)

### <a name="administrator-tasks"></a>Attività dell'amministratore
* [Configurare la registrazione dei dispositivi](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Attività dell'utente
* [Configurare un nuovo dispositivo Windows 10 con Azure AD durante l'installazione](active-directory-azureadjoin-user-frx.md)
* [Configurare un dispositivo Windows 10 con Azure AD dal menu Impostazioni](active-directory-azureadjoin-user-upgrade.md)
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Abilitare le funzionalità BYOD per Windows 10 nell'organizzazione
È possibile configurare utenti e dipendenti in modo che usino i dispositivi Windows personali (BYOD, Bring Your Own Device) per accedere alle app e alle risorse aziendali. Gli utenti possono aggiungere i propri account Azure AD (account aziendali o dell'istituto di istruzione) a un dispositivo personale Windows per accedere alle risorse in totale sicurezza e conformità.

### <a name="prerequisites"></a>Prerequisiti
**A livello aziendale (amministratore)**

* Sottoscrizione di Azure AD

**A livello dell'utente**

* Windows 10 (Professional ed Enterprise)

### <a name="administrator-tasks"></a>Attività dell'amministratore
* [Configurare la registrazione dei dispositivi](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Attività dell'utente
* [Aggiunta di un dispositivo Windows 10 personale all'organizzazione](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


