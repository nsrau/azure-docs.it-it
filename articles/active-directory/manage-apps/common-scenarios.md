---
title: Scenari comuni di gestione delle applicazioni per Azure Active Directory Documenti Microsoft
description: Centralizzare la gestione delle applicazioni con Azure ADCentralize application management with Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657949"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizzare la gestione delle applicazioni con Azure ADCentralize application management with Azure AD

Password, sia un incubo IT che un dolore per i dipendenti di tutto il mondo. Questo è il motivo per cui sempre più aziende si rivolgono ad Azure Active Directory, la soluzione di gestione delle identità e degli accessi di Microsoft per il cloud e tutte le altre risorse. Passare da un'applicazione all'altra senza dover immettere una password per ognuna di esse. Passa da Outlook, a Workday, ad ADP il più velocemente possibile, in modo rapido e sicuro. Quindi collabora con i partner e anche con altri al di fuori della tua organizzazione senza dover chiamare l'IT. Inoltre, Azure AD consente di gestire i rischi proteggendo le app usate con elementi come l'autenticazione a più fattori per verificare chi sei, usando l'apprendimento automatico adattivo continuamente e l'intelligenza di sicurezza per rilevare accessi sospetti che ti consentono di accedere in modo sicuro alle app di cui hai bisogno, ovunque ti trovi. Non è solo ottimo per gli utenti, ma anche per l'IT. Con le verifiche di accesso just-in-time e una suite di governance su larga scala, Azure AD ti aiuta a rimanere in conformità e applicare anche i criteri. E ottieni questo, puoi anche automatizzare il provisioning degli account utente, rendendo la gestione dell'accesso un gioco da ragazzi. Estrarre alcuni degli scenari comuni per cui i clienti usano le funzionalità di gestione delle applicazioni di Azure Active Directory.

**Scenari comuni**


> [!div class="checklist"]
> * SSO per tutte le tue applicazioni
> * Automatizzare il provisioning e il deprovisioningAutomate provisioning and deprovisioning 
> * Proteggi le tue applicazioni
> * Governa l'accesso alle tue applicazioni
> * Accesso sicuro ibrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: configurare SSO per tutte le applicazioniScenario 1: Set up SSO for all your applications

Non è più necessario gestire la password. Accedi in modo sicuro a tutte le risorse di cui hai bisogno con le tue credenziali aziendali. 

|Funzionalità  | Descrizione | Recommendation |
|---------|---------|---------|
|SSO|SSO federato basato su standard utilizzando standard di settore affidabili.|Usa sempre [SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) per abilitare SSO quando l'applicazione lo supporta.|
|Pannello di accesso|Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consenti loro di essere più produttivi con funzionalità self-service, come la richiesta di accesso ad app e gruppi o la gestione dell'accesso alle risorse per conto di altri.| Distribuire il pannello di [accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) nell'organizzazione dopo aver integrato le app con Azure AD per SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Automatizzare il provisioning e il deprovisioningScenario 2: Automate provisioning and deprovisioning 


La maggior parte delle applicazioni richiede il provisioning di un utente nell'applicazione prima di accedere alle risorse necessarie. L'utilizzo di file CSV o script complessi può essere costoso e difficile da gestire. Inoltre, i clienti devono assicurarsi che gli account vengano rimossi quando qualcuno non dovrebbe più avere accesso. Sfrutta gli strumenti riportati di seguito per automatizzare il provisioning e il deprovisioning. 


|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|SCIM Provisioning|[SCIM](https://aka.ms/SICMOverview) è una best practice di settore per automatizzare il provisioning degli utenti. Qualsiasi applicazione conforme a SCIM può essere integrata con Azure AD. Crea, aggiorna ed elimina automaticamente gli account utente senza dover gestire file CSV, script personalizzati o soluzioni locali.|Estrarre l'elenco crescente di app [preintegrate](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) nella raccolta di app di Azure AD|
|Microsoft Graph|Sfrutta il respiro e la profondità dei dati di Azure AD per arricchire l'applicazione con i dati necessari.|Sfrutta il [grafico Microsoft](https://developer.microsoft.com/graph/) per ottenere dati da tutto l'ecosistema Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Proteggere le applicazioniScenario 3: Secure your applications
L'identità è il perno della sicurezza. Se un'identità viene compromessa, è incredibilmente difficile fermare l'effetto domino prima che sia troppo tardi. In media più di 100 giorni passano prima che le organizzazioni scoprano che c'era un compromesso. Usare gli strumenti forniti da Azure AD per migliorare la sicurezza delle applicazioni. 

|Funzionalità  |Descrizione| Recommendation |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Usando i metodi di autenticazione approvati dall'amministratore, Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni soddisfacendo al contempo la richiesta di un semplice processo di accesso.| [Abilitare l'autenticazione a](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) più fattori per gli utenti.  |
|Accesso condizionale|Con l'accesso condizionale, è possibile implementare decisioni di controllo di accesso automatizzate per chi può accedere alle app cloud, in base alle condizioni.| Esaminare [le impostazioni predefinite di sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) e i criteri [comuni](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) che i clienti utilizzano. | 
|Identity Protection|Identity Protection usa le informazioni acquisite da Microsoft dalla propria posizione in organizzazioni che usano Azure AD, nello spazio consumer con account Microsoft e nelle piattaforme di gioco con Xbox per proteggere gli utenti. Microsoft analizza 6,5 mila miliardi di segnali al giorno per identificare le minacce e proteggere i clienti.|Abilitare i [criteri di protezione delle identità predefiniti](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) forniti dal servizio. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: Gestire l'accesso alle applicazioniScenario 4: Govern access to your applications
Identity Governance consente alle organizzazioni di raggiungere un equilibrio tra produttività e produttività: quanto velocemente una persona può avere accesso alle applicazioni di cui ha bisogno, ad esempio quando si unisce all'organizzazione? e sicurezza (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo). 

|Funzionalità  |Descrizione|Recommendation |
|---------|---------| ---------|
|Elm|La gestione dei diritti di Azure AD può aiutare gli utenti sia all'interno che all'esterno dell'organizzazione a gestire in modo più efficiente l'accesso alle applicazioni.| Consentire agli utenti non amministratori di gestire l'accesso alle applicazioni con i pacchetti di [accesso.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)|
|Verifiche di accesso|L'accesso degli utenti alle app può essere esaminato regolarmente per assicurarsi che solo le persone giuste abbiano accesso continuo.| [Esaminare l'accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) alle applicazioni più sensibili. |
|Log Analytics|Genera report su chi accede alle applicazioni e archiviale nello strumento SIEM preferito per correlare i dati tra le origini dati e nel tempo.| Abilitare [l'analisi dei log](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) e impostare avvisi per gli eventi critici relativi alle applicazioni. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Hybrid Secure Access
L'identità può essere il piano di controllo solo se è in grado di connettere tutti gli elementi tra applicazioni cloud e locali. Sfrutta gli strumenti forniti da Azure AD e dai suoi partner per proteggere l'accesso alle applicazioni basate sull'autenticazione legacy.

|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|Proxy dell'applicazione|Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Devono accedere alle app SaaS nel cloud e alle app aziendali in locale. Il proxy di applicazione di Azure AD consente questo accesso affidabile senza reti private virtuali (VPN) costose e complesse o zone demilitarizzate (DM).|Configurare [l'accesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) per le app prem. |
|F5, Akamai, scalare|Usando la rete e il controller di distribuzione esistente, è possibile proteggere facilmente le applicazioni legacy ancora critiche per i processi aziendali, ma che non era possibile proteggere in passato con Azure AD. È probabile che siano già presenti tutti gli elementi necessari per iniziare a proteggere le applicazioni.| Utilizzi Akamai, Citrix, F5 o scaler? Dai un'occhiata alle nostre [soluzioni predefinite.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Articoli correlati

- [Gestione delle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Provisioning di applicazioni](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Accesso sicuro ibrido]()
- [Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Sicurezza dell'identità](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
