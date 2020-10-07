---
title: Scenari di gestione delle applicazioni comuni per Azure Active Directory | Microsoft Docs
description: Centralizzare la gestione delle applicazioni con Azure AD
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ce3819ff1f9b0c61f7738f90ff17c2798fe888b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88642113"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizzare la gestione delle applicazioni con Azure AD

Password, un incubo per il personale IT e una problematica per i dipendenti in tutto il mondo. Per questo motivo, un numero sempre crescente di aziende sta adottando Azure Active Directory, la soluzione di gestione delle identità e degli accessi di Microsoft per il cloud e tutte le altre risorse. Passare da un'applicazione all'altra senza dover immettere una password per ognuna di esse. Passare da Outlook, a Workday, ad ADP semplicemente aprendo le app, in modo rapido e sicuro. Collaborare quindi con i partner e anche con altri utenti esterni all'organizzazione senza doversi rivolgere al reparto IT. Inoltre, Azure AD aiuta a gestire i rischi assicurando la protezione delle app usate con varie funzionalità, ad esempio l'autenticazione a più fattori per verificare l'identità degli utenti, usando l'apprendimento automatico e l'intelligence sulla sicurezza in modalità continua per rilevare gli accessi sospetti e garantire al contempo l'accesso sicuro alle app necessarie, ovunque ci si trovi. È una soluzione straordinaria, non solo per gli utenti ma anche per il personale IT. Con le verifiche di accesso JIT e una suite di governance su larga scala, Azure AD consente di mantenere la conformità e anche di imporre i criteri. Ed è persino possibile automatizzare il provisioning degli account utente per alleggerire ulteriormente la gestione degli accessi. Di seguito sono elencati alcuni degli scenari comuni per cui i clienti usano le funzionalità dei gestione delle applicazioni di Azure Active Directory.

**Scenari comuni**


> [!div class="checklist"]
> * Accesso Single Sign-On (SSO) per tutte le applicazioni
> * Automazione di provisioning e deprovisioning 
> * Protezione delle applicazioni
> * Regolamentazione dell'accesso alle applicazioni
> * Accesso sicuro ibrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: Configurare l'accesso Single Sign-On (SSO) per tutte le applicazioni

Gestione delle password non più necessaria. Accesso sicuro a tutte le risorse necessarie con le credenziali aziendali. 

|Funzionalità  | Descrizione | Recommendation |
|---------|---------|---------|
|SSO|SSO federato basato su standard che usa standard di settore attendibili.|Usare sempre [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) per abilitare l'accesso SSO quando l'applicazione lo supporta.|
|App personali|Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consente loro di aumentare la produttività grazie alle funzionalità self-service, ad esempio per richiedere l'accesso ad app e gruppi o per gestire l'accesso alle risorse per conto di altri.| Distribuire [App personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) nell'organizzazione dopo aver integrato le app con Azure AD per l'accesso Single Sign-On.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Automazione di provisioning e deprovisioning 


Per la maggior parte delle applicazioni è necessario effettuare il provisioning di un utente nell'applicazione prima di accedere alle risorse necessarie. L'uso di file CSV o di script complessi può essere un'attività costosa e difficile da gestire. Inoltre, i clienti devono assicurarsi che gli account vengano rimossi quando un utente non è più autorizzato ad accedere alle risorse. Sfruttare gli strumenti seguenti per automatizzare il provisioning e il deprovisioning. 


|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|Provisioning SCIM|[SCIM](https://aka.ms/SCIMOverview) è una procedura consigliata del settore per l'automazione del provisioning degli utenti. È possibile integrare con Azure AD qualsiasi applicazione conforme a SCIM. Consente di creare, aggiornare ed eliminare automaticamente gli account utente senza dover gestire file CSV, script personalizzati o soluzioni locali.|Vedere l'elenco in continua evoluzione di app [pre-integrate](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) nella raccolta di app Azure AD|
|Microsoft Graph|È possibile sfruttare l'ampiezza e la profondità dei dati di Azure AD per arricchire l'applicazione con i dati necessari,|usando [Microsoft Graph](https://developer.microsoft.com/graph/) per recuperare i dati da tutto l'ecosistema Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Protezione delle applicazioni
L'identità è il cardine della sicurezza. Se un'identità viene compromessa, è incredibilmente difficile arrestare l'effetto domino prima che sia troppo tardi. In media passano oltre 100 giorni prima che le organizzazioni scoprano che si è verificata una compromissione. Usare gli strumenti forniti da Azure AD per migliorare il comportamento di sicurezza delle applicazioni. 

|Funzionalità  |Descrizione| Recommendation |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Usando metodi di autenticazione approvati dell'amministratore, Azure MFA contribuisce a salvaguardare l'accesso a dati e applicazioni, rispondendo al contempo all'esigenza di offrire un processo di accesso facile.| [Abilitare l'autenticazione a più fattori (MFA)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) per gli utenti.  |
|Accesso condizionale|Con l'accesso condizionale è possibile implementare decisioni di controllo di accesso automatizzate relative a chi può accedere alle app cloud, in base a determinate condizioni.| Esaminare le [impostazioni predefinite per la sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) e i [criteri comuni](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) adottati dai clienti. | 
|Identity Protection|Identity Protection usa le informazioni acquisite da Microsoft dalla propria posizione in organizzazioni che usano Azure AD, nello spazio consumer con account Microsoft e nelle piattaforme di gioco con Xbox per proteggere gli utenti. Microsoft analizza 6,5 mila miliardi di segnali al giorno per identificare le minacce e proteggere i clienti.|Abilitare i [criteri predefiniti di protezione delle identità](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) forniti dal servizio. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: Regolamentazione dell'accesso alle applicazioni
La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra produttività (la velocità con cui una persona può accedere alle applicazioni necessarie, ad esempio quando inizia a collaborare con un'azienda) e sicurezza (come deve cambiare l'accesso nel tempo, ad esempio in seguito a variazioni della posizione lavorativa di tale persona). 

|Funzionalità  |Descrizione|Recommendation |
|---------|---------| ---------|
|ELM|Gestione entitlement di Azure AD consente agli utenti interni ed esterni all'organizzazione di gestire l'accesso alle applicazioni in modo più efficiente.| Consentire agli utenti non amministratori di gestire l'accesso alle applicazioni con [pacchetti di accesso](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Verifiche di accesso|È possibile verificare regolarmente l'accesso degli utenti alle app per assicurarsi che solo le persone appropriate dispongano di accesso continuo.| [Verificare l'accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) alle applicazioni più sensibili. |
|Log Analytics|È possibile generare report sugli utenti che accedono alle applicazioni e archiviarli nello strumento SIEM desiderato per correlare i dati tra origini dati e nel tempo.| Abilitare [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) e configurare gli avvisi per gli eventi critici correlati alle applicazioni. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Accesso sicuro ibrido
L'identità può essere il piano di controllo solo se può connettere tutti gli elementi tra applicazioni cloud e locali. Sfruttare gli strumenti forniti da Azure AD e dai suoi partner per proteggere l'accesso alle applicazioni basate su autenticazione legacy.

|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|Proxy dell'applicazione|Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Devono accedere alle app SaaS nel cloud e alle app aziendali nell'ambiente locale. Azure AD Application Proxy offre questo accesso affidabile senza reti private virtuali (VPN) o reti perimetrali costose e complesse.|Configurare l'[accesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) per le app locali. |
|F5, Akamai, Zscaler|Usando la rete e il controller di distribuzione esistente, è possibile proteggere facilmente le applicazioni legacy ancora critiche per i processi aziendali, ma che non era possibile proteggere in passato con Azure AD. È probabile che siano già presenti tutti gli elementi necessari per iniziare a proteggere le applicazioni.| Si usa Akamai, Citrix, F5 o Zscaler? Vedere qui le [soluzioni predefinite](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). | 

## <a name="related-articles"></a>Articoli correlati

- [Gestione delle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Provisioning delle applicazioni](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Accesso sicuro ibrido](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Sicurezza delle identità](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
