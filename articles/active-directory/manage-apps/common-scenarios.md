---
title: Scenari comuni di gestione delle applicazioni per Azure Active Directory | Microsoft Docs
description: Centralizzare la gestione delle applicazioni con Azure AD
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
ms.openlocfilehash: 1874a2f2cf96aaa905616bddcc6cb83c60c1d279
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115609"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizzare la gestione delle applicazioni con Azure AD

Password, sia un incubo IT che un dolore per i dipendenti in tutto il mondo. Questo è il motivo per cui un maggior numero di aziende si sta trasformando in Azure Active Directory, la soluzione di gestione delle identità e degli accessi di Microsoft per il cloud e tutte le altre risorse. Passare dall'applicazione all'applicazione senza dover immettere una password per ciascuna di esse. Passa da Outlook, alla giornata lavorativa, ad ADP con la massima rapidità possibile per aprirli, in modo rapido e sicuro. Collaborare quindi con i partner e anche con altri utenti esterni all'organizzazione senza doverli chiamare. Azure AD aiuta a gestire i rischi garantendo la protezione delle app usate, ad esempio l'autenticazione a più fattori, per verificare chi sei, usando l'apprendimento automatico e l'Intelligence per la sicurezza in modo continuo per rilevare accessi sospetti, garantendo l'accesso sicuro alle app necessarie, ovunque ci si trovi. Non solo per gli utenti, ma anche per l'IT. Con le verifiche di accesso just-in-time e una suite di governance con scalabilità completa, Azure AD ti aiuta a rimanere in conformità e a applicare i criteri. Per ottenere questo valore, è anche possibile automatizzare il provisioning degli account utente, rendendo la gestione degli accessi una brezza. esaminare alcuni degli scenari comuni che i clienti utilizzano le funzionalità di gestione delle applicazioni di Azure Active Directory per.

**Scenari comuni**


> [!div class="checklist"]
> * SSO per tutte le applicazioni
> * Automatizzare il provisioning e il deprovisioning 
> * Proteggere le applicazioni
> * Governare l'accesso alle applicazioni
> * Accesso protetto ibrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: configurare l'accesso SSO per tutte le applicazioni

Non è più possibile gestire la password. Accedi in modo sicuro a tutte le risorse che ti servono con le credenziali aziendali. 

|Funzionalità  | Descrizione | Recommendation |
|---------|---------|---------|
|SSO|SSO federato basato su standard che usa standard di settore attendibili.|Usare sempre [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) per abilitare l'accesso SSO quando l'applicazione lo supporta.|
|Pannello di accesso|Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consentire loro di essere più produttivi con le funzionalità self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso alle risorse per conto di altri utenti.| Distribuire il [Pannello di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) dell'organizzazione dopo aver integrato le app con Azure ad per SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: automatizzare il provisioning e il deprovisioning 


Per la maggior parte delle applicazioni è necessario eseguire il provisioning di un utente nell'applicazione prima di accedere alle risorse necessarie. L'uso di file CSV o di script complessi può essere costoso e difficile da gestire. Inoltre, i clienti devono assicurarsi che gli account vengano rimossi quando un utente non è più in grado di accedere. Utilizzare gli strumenti seguenti per automatizzare il provisioning e il deprovisioning. 


|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|Provisioning di SCIM|[Scim](https://aka.ms/SCIMOverview) è una procedura consigliata di settore per l'automazione del provisioning degli utenti. Tutte le applicazioni conformi a SCIM possono essere integrate con Azure AD. Consente di creare, aggiornare ed eliminare automaticamente gli account utente senza dover gestire file CSV, script personalizzati o soluzioni locali.|Vedere l'elenco in continua crescita di app [preintegrate](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) nella raccolta di app Azure ad|
|Microsoft Graph|Sfruttare il respiro e la profondità dei dati che Azure AD necessario per arricchire l'applicazione con i dati necessari.|Sfrutta [Microsoft Graph](https://developer.microsoft.com/graph/) per ottenere i dati da tutto l'ecosistema Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: proteggere le applicazioni
Identity è il fulcro della sicurezza. Se un'identità viene compromessa, è incredibilmente difficile arrestare l'effetto domino prima che sia troppo tardi. In media passano oltre 100 giorni prima che le organizzazioni rilevino che si è verificato un compromesso. Usare gli strumenti forniti da Azure AD per migliorare il comportamento di sicurezza delle applicazioni. 

|Funzionalità  |Descrizione| Recommendation |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Con i metodi di autenticazione approvati dall'amministratore, l'autenticazione a più fattori di Azure consente di proteggere l'accesso ai dati e alle applicazioni, soddisfacendo la richiesta di un processo di accesso semplice.| [Abilitare](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) l'autenticazione a più fattori per gli utenti.  |
|Accesso condizionale|Con l'accesso condizionale è possibile implementare decisioni automatiche di controllo degli accessi per gli utenti che possono accedere alle app cloud in base alle condizioni.| Esaminare i [valori predefiniti di sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) e i [criteri comuni](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) utilizzati dai clienti. | 
|Identity Protection|Identity Protection usa le informazioni acquisite da Microsoft dalla propria posizione in organizzazioni che usano Azure AD, nello spazio consumer con account Microsoft e nelle piattaforme di gioco con Xbox per proteggere gli utenti. Microsoft analizza 6,5 mila miliardi di segnali al giorno per identificare le minacce e proteggere i clienti.|Abilitare i [criteri di Identity Protection predefiniti](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) forniti dal servizio. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: governare l'accesso alle applicazioni
La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra la produttività e la rapidità con cui un utente può accedere alle applicazioni necessarie, ad esempio quando partecipa alla propria organizzazione? e sicurezza (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo). 

|Funzionalità  |Descrizione|Recommendation |
|---------|---------| ---------|
|Olmo|Azure AD la gestione dei diritti può aiutare gli utenti all'interno e all'esterno dell'organizzazione a gestire in modo più efficiente l'accesso alle applicazioni.| Consentire a non amministratori di gestire l'accesso alle applicazioni con i [pacchetti di accesso](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Verifiche di accesso|L'accesso dell'utente alle app può essere esaminato a intervalli regolari per assicurarsi che solo le persone giuste abbiano accesso continuo.| [Verificare l'accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) alle applicazioni più sensibili. |
|Log Analytics|Generare report sugli utenti che accedono alle applicazioni e archiviarle nello strumento SIEM scelto per correlare i dati tra origini dati e nel tempo.| Abilitare [log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) e configurare gli avvisi per gli eventi critici correlati alle applicazioni. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: accesso protetto ibrido
L'identità può essere il piano di controllo solo se è in grado di connettere tutti gli elementi tra applicazioni cloud e locali. Sfrutta gli strumenti forniti da Azure AD e dai suoi partner per proteggere l'accesso alle applicazioni basate su autenticazione legacy.

|Funzionalità  |Descrizione|Recommendation |
|---------|---------|---------|
|Application Proxy|Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Devono accedere alle app SaaS nel cloud e nelle app aziendali locali. Azure AD proxy di applicazione consente questo accesso affidabile senza reti private virtuali (VPN) o zone demilitarizzata (reti perimetrali) costose e complesse.|Configurare [l'accesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) per le app locali. |
|F5, Akamai, zscaler|Usando la rete e il controller di distribuzione esistente, è possibile proteggere facilmente le applicazioni legacy ancora critiche per i processi aziendali, ma che non era possibile proteggere in passato con Azure AD. È probabile che siano già presenti tutti gli elementi necessari per iniziare a proteggere le applicazioni.| Con Akamai, Citrix, F5 o zScaler? Scopri le nostre [soluzioni](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)predefinite. | 

## <a name="related-articles"></a>Articoli correlati

- [Gestione delle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Provisioning di applicazioni](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Accesso protetto ibrido](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Governance delle identità](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Sicurezza delle identità](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
