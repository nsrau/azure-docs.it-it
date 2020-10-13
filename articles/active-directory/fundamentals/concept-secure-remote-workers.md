---
title: Rispondi rapidamente a identità sicure con Azure Active Directory
description: Rispondi rapidamente alle minacce con Azure AD identità basate sul cloud
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f4d9d1a2a4c88601e7dd7e0d6f56025b79aaac1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705368"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Rispondere rapidamente a identità sicure con Azure AD

Può sembrare scoraggiante provare ad assicurare la protezione dei lavoratori nel mondo odierno, specialmente quando è necessario rispondere rapidamente e fornire rapidamente l'accesso a molti servizi. Questo articolo ha lo scopo di fornire un elenco conciso di tutte le azioni da intraprendere, consentendo di identificare e classificare in ordine di priorità l'ordine di distribuzione delle funzionalità di Azure AD in base al tipo di licenza di cui si è proprietari. Azure AD offre molte funzionalità e offre molti livelli di sicurezza per le identità, la navigazione tra le funzionalità rilevanti può talvolta risultare eccessiva. Molte organizzazioni si trovano già nel cloud o si avvicinano rapidamente al cloud, questo documento è destinato a consentire la distribuzione rapida dei servizi, con la protezione delle identità come principale considerazione. 

Ogni tabella fornisce una raccomandazione di sicurezza coerente, proteggendo le identità degli amministratori e degli utenti dagli attacchi principali alla sicurezza (violazione della riproduzione, del phishing e dello spray delle password) riducendo al minimo l'effetto dell'utente e migliorando l'esperienza utente. 

Le linee guida consentono inoltre agli amministratori di configurare l'accesso alle applicazioni SaaS e locali in modo sicuro e protetto ed è applicabile alle identità cloud o ibride (sincronizzate) e si applicano agli utenti che lavorano in modalità remota o in ufficio.

Questo elenco di controllo permette di distribuire rapidamente le azioni critiche consigliate per proteggere subito l'organizzazione spiegando in che modo:

- Rafforzare le credenziali.
- Ridurre la superficie di attacco.
- Automatizzare la risposta alle minacce.
- Usare l'intelligenza del cloud.
- Abilitare il self-service per gli utenti finali.

## <a name="prerequisites"></a>Prerequisiti

Questa guida presuppone che le identità solo cloud o ibride siano state stabilite in Azure AD già. Per informazioni sulla scelta del tipo di identità, vedere l'articolo [scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida Azure Active Directory](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Riepilogo

Ci sono molti aspetti di un'infrastruttura di identità sicura, ma questo elenco di controllo è incentrato su un'infrastruttura di identità sicura e sicura che consente agli utenti di lavorare in modalità remota. La protezione dell'identità è solo una parte della storia della sicurezza, ma anche la protezione dei dati, delle applicazioni e dei dispositivi.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Linee guida per i clienti Azure AD Free, Office 365 o Microsoft 365.

Per proteggere le proprie identità utente, è necessario che i clienti di Azure AD Free, Office 365 o Microsoft 365 app si occupino di una serie di raccomandazioni, nella tabella seguente vengono evidenziate le azioni principali per le seguenti sottoscrizioni di licenza:

- Office 365 (Office 365 E1, E3, E5, F1, a1, a3, a5)
- Microsoft 365 (Business Basic, Apps for business, business standard, Business Premium, a1)
- Azure AD Free (incluso con Azure, Dynamics 365, Intune e Power Platform)

| Azione consigliata | Dettaglio |
| --- | --- |
| [Abilita impostazioni predefinite sicurezza](concept-fundamentals-security-defaults.md) | Proteggi tutte le identità utente e le applicazioni abilitando l'autenticazione a più fattori e bloccando l'autenticazione legacy |
| [Abilitare la sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md) (se si usano le identità ibride) | Fornire ridondanza per l'autenticazione e migliorare la sicurezza (inclusi il blocco intelligente, il blocco IP e la possibilità di individuare le credenziali perse). |
| [Abilita blocco intelligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se applicabile) | Protegge gli utenti dall'esperienza di blocco degli account Extranet da attività dannose. |
| [Abilita Azure Active Directory blocco intelligente](../authentication/howto-password-smart-lockout.md) (se si usano le identità gestite) | Il blocco Smart consente di bloccare gli attori malintenzionati che tentano di indovinare le password degli utenti o di usare metodi di forza bruta per ottenere. |
| [Disabilitare il consenso dell'utente finale per le applicazioni](../manage-apps/configure-user-consent.md) | Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore, in modo che gli utenti finali non espongano dati aziendali. Per ridurre l’area di azione e mitigare questo rischio, Microsoft consiglia di disabilitare le future operazioni di consenso degli utenti. |
| [Integrare le applicazioni SaaS supportate dalla raccolta per Azure AD e abilitare Single Sign-on](../manage-apps/add-application-portal.md) | Azure AD offre una raccolta contenente migliaia di applicazioni preintegrate. Alcune applicazioni usate dall'organizzazione sono probabilmente incluse nella raccolta accessibile direttamente dal portale di Azure. Fornire l'accesso alle applicazioni SaaS aziendali in modalità remota e sicura con esperienza utente migliorata (SSO) |
| [Automatizzare il provisioning e il deprovisioning utenti dalle applicazioni SaaS](../app-provisioning/user-provisioning.md) (se applicabile) | Creare automaticamente identità e ruoli utente nelle applicazioni cloud (SaaS) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente durante la modifica dello stato o dei ruoli, aumentando la sicurezza dell'organizzazione. |
| [Abilitare l'accesso ibrido sicuro: proteggere le app legacy con i controller e le reti di distribuzione delle app esistenti](../manage-apps/secure-hybrid-access.md) (se applicabile) | Consente di pubblicare e proteggere le applicazioni di autenticazione legacy locali e cloud connettendosi a Azure AD con la rete o il controller di distribuzione delle applicazioni esistente. |
| [Abilitare la reimpostazione della password self-service](../authentication/tutorial-enable-sspr.md) (applicabile solo agli account cloud) | Questa possibilità riduce le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione. |
| [Dove possibile, usare ruoli amministrativi non globali](../users-groups-roles/directory-assign-admin-roles.md) | Concedere agli amministratori solo l'accesso necessario esclusivamente alle aree a cui devono accedere. Non tutti gli amministratori devono essere amministratori globali. |
| [Implementare le linee guida per le password di Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Smettere di richiedere agli utenti di modificare la password in base a una pianificazione specifica e disabilitare i requisiti di complessità: gli utenti saranno più propensi a ricordare le loro password e a mantenerle in sicurezza. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Linee guida per i clienti Azure AD Premium piano 1.

Nella tabella seguente vengono evidenziate le azioni principali per le seguenti sottoscrizioni di licenza:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Azione consigliata | Dettaglio |
| --- | --- |
| [Abilitare l'esperienza di registrazione combinata per Azure multi-factor authentication e SSPR per semplificare l'esperienza di registrazione utente](../authentication/howto-registration-mfa-sspr-combined.md) | Consentire agli utenti di registrarsi da un'esperienza comune sia per Azure Multi-Factor Authentication che per la reimpostazione della password self-service. |
| [Configurare le impostazioni dell'autenticazione a più fattori per l'organizzazione](../authentication/howto-mfa-getstarted.md) | Assicurarsi che gli account siano protetti da compromessi con l'autenticazione a più fattori |
| [Abilitare la reimpostazione self-service delle password](../authentication/tutorial-enable-sspr.md) | Questa possibilità riduce le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione |
| [Implementare il writeback delle password](../authentication/tutorial-enable-sspr-writeback.md) (se si usano le identità ibride) | Consentire il writeback delle modifiche delle password nel cloud in un ambiente di Windows Server Active Directory locale. |
| Creare e abilitare i criteri di accesso condizionale | [Autenticazione a più fattori per gli amministratori per proteggere gli account a cui sono assegnati diritti amministrativi.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blocca i protocolli di autenticazione legacy a causa del maggiore rischio associato ai protocolli di autenticazione legacy.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Autenticazione a più fattori per tutti gli utenti e le applicazioni per creare criteri di autenticazione a più fattori bilanciati per l'ambiente, proteggendo utenti e applicazioni.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Richiedi autenticazione a più fattori per la gestione di Azure per proteggere le tue risorse con privilegi richiedendo l'autenticazione a più fattori per tutti gli utenti che accedono alle risorse di Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Abilitare la sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md) (se si usano le identità ibride) | Fornire ridondanza per l'autenticazione e migliorare la sicurezza (inclusi il blocco intelligente, il blocco IP e la possibilità di individuare le credenziali perse). |
| [Abilita blocco intelligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se applicabile) | Protegge gli utenti dall'esperienza di blocco degli account Extranet da attività dannose. |
| [Abilita Azure Active Directory blocco intelligente](../authentication/howto-password-smart-lockout.md) (se si usano le identità gestite) | Il blocco Smart consente di bloccare gli attori malintenzionati che tentano di indovinare le password degli utenti o di usare metodi di forza bruta per ottenere. |
| [Disabilitare il consenso dell'utente finale per le applicazioni](../manage-apps/configure-user-consent.md) | Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore, in modo che gli utenti finali non espongano dati aziendali. Per ridurre l’area di azione e mitigare questo rischio, Microsoft consiglia di disabilitare le future operazioni di consenso degli utenti. |
| [Abilitare l'accesso remoto alle applicazioni legacy locali con il proxy di applicazione](../manage-apps/application-proxy-add-on-premises-application.md) | Abilitare Azure AD proxy di applicazione e integrarsi con le app legacy per consentire agli utenti di accedere in modo sicuro alle applicazioni locali accedendo con il proprio account di Azure AD. |
| [Abilitare l'accesso ibrido sicuro: proteggere le app legacy con i controller e le reti di distribuzione delle app esistenti](../manage-apps/secure-hybrid-access.md) (se applicabile). | Consente di pubblicare e proteggere le applicazioni di autenticazione legacy locali e cloud connettendosi a Azure AD con la rete o il controller di distribuzione delle applicazioni esistente. |
| [Integrare le applicazioni SaaS supportate dalla raccolta per Azure AD e abilitare Single Sign-on](../manage-apps/add-application-portal.md) | Azure AD offre una raccolta contenente migliaia di applicazioni preintegrate. Alcune applicazioni usate dall'organizzazione sono probabilmente incluse nella raccolta accessibile direttamente dal portale di Azure. Fornire l'accesso alle applicazioni SaaS aziendali in modalità remota e sicura con l'esperienza utente migliorata (SSO). |
| [Automatizzare il provisioning e il deprovisioning utenti dalle applicazioni SaaS](../app-provisioning/user-provisioning.md) (se applicabile) | Creare automaticamente identità e ruoli utente nelle applicazioni cloud (SaaS) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente durante la modifica dello stato o dei ruoli, aumentando la sicurezza dell'organizzazione. |
| [Abilitare l'accesso condizionale-basato su dispositivo](../conditional-access/require-managed-devices.md) | Migliorare la sicurezza e l'esperienza utente con l'accesso condizionale basato su dispositivo. Questo passaggio garantisce che gli utenti possano accedere solo da dispositivi che soddisfano gli standard di sicurezza e conformità. I dispositivi di questo tipo sono noti anche come dispositivi gestiti. I dispositivi gestiti possono essere conformi a Intune o Azure AD ibrido dispositivi aggiunti. |
| [Abilitare la password di protezione](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Impedire agli utenti di usare password vulnerabili e facili da indovinare. |
| [Designare più di un amministratore globale](../users-groups-roles/directory-emergency-access.md) | Assegnare almeno due account amministratore globale permanenti solo cloud da usare in caso di emergenza. Questi account non sono destinati all'uso quotidiano e devono usare password lunghe e complesse. Gli account break Glass assicurano che sia possibile accedere al servizio in situazioni di emergenza. |
| [Dove possibile, usare ruoli amministrativi non globali](../users-groups-roles/directory-assign-admin-roles.md) | Concedere agli amministratori solo l'accesso necessario esclusivamente alle aree a cui devono accedere. Non tutti gli amministratori devono essere amministratori globali. |
| [Implementare le linee guida per le password di Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Smettere di richiedere agli utenti di modificare la password in base a una pianificazione specifica e disabilitare i requisiti di complessità: gli utenti saranno più propensi a ricordare le loro password e a mantenerle in sicurezza. |
| [Creare un piano per l'accesso degli utenti guest](../external-identities/what-is-b2b.md) | Collaborare con gli utenti Guest consentendo loro di accedere alle app e ai servizi con le proprie identità aziendali, scolastiche o sociali. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Linee guida per i clienti Azure AD Premium piano 2.

Nella tabella seguente vengono evidenziate le azioni principali per le seguenti sottoscrizioni di licenza:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, a5)

| Azione consigliata | Dettaglio |
| --- | --- |
| [Abilitare l'esperienza di registrazione combinata per Azure multi-factor authentication e SSPR per semplificare l'esperienza di registrazione utente](../authentication/howto-registration-mfa-sspr-combined.md) | Consentire agli utenti di registrarsi da un'esperienza comune sia per Azure Multi-Factor Authentication che per la reimpostazione della password self-service. |
| [Configurare le impostazioni dell'autenticazione a più fattori per l'organizzazione](../authentication/howto-mfa-getstarted.md) | Assicurarsi che gli account siano protetti da compromessi con l'autenticazione a più fattori |
| [Abilitare la reimpostazione self-service delle password](../authentication/tutorial-enable-sspr.md) | Questa possibilità riduce le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione |
| [Implementare il writeback delle password](../authentication/tutorial-enable-sspr-writeback.md) (se si usano le identità ibride) | Consentire il writeback delle modifiche delle password nel cloud in un ambiente di Windows Server Active Directory locale. |
| [Abilitare i criteri di Identity Protection per applicare la registrazione a più fattori](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Gestire l'implementazione del Multi-Factor Authentication di Azure (multi-factor authentication). |
| [Abilitare gli utenti di Identity Protection e i criteri di rischio di accesso](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Abilitare l'utente e i criteri di accesso di Identity Protection. Il criterio di accesso consigliato è quello di individuare gli accessi a rischio medio e richiedere l'autenticazione a più fattori. Per i criteri utente dovrebbe essere destinato a utenti ad alto rischio che richiedono l'azione di modifica della password. |
| Creare e abilitare i criteri di accesso condizionale | [Autenticazione a più fattori per gli amministratori per proteggere gli account a cui sono assegnati diritti amministrativi.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blocca i protocolli di autenticazione legacy a causa del maggiore rischio associato ai protocolli di autenticazione legacy.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Richiedi autenticazione a più fattori per la gestione di Azure per proteggere le tue risorse con privilegi richiedendo l'autenticazione a più fattori per tutti gli utenti che accedono alle risorse di Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Abilitare la sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md) (se si usano le identità ibride) | Fornire ridondanza per l'autenticazione e migliorare la sicurezza (inclusi il blocco intelligente, il blocco IP e la possibilità di individuare le credenziali perse). |
| [Abilita blocco intelligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se applicabile) | Protegge gli utenti dall'esperienza di blocco degli account Extranet da attività dannose. |
| [Abilita Azure Active Directory blocco intelligente](../authentication/howto-password-smart-lockout.md) (se si usano le identità gestite) | Il blocco Smart consente di bloccare gli attori malintenzionati che tentano di indovinare le password degli utenti o di usare metodi di forza bruta per ottenere. |
| [Disabilitare il consenso dell'utente finale per le applicazioni](../manage-apps/configure-user-consent.md) | Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore, in modo che gli utenti finali non espongano dati aziendali. Per ridurre l’area di azione e mitigare questo rischio, Microsoft consiglia di disabilitare le future operazioni di consenso degli utenti. |
| [Abilitare l'accesso remoto alle applicazioni legacy locali con il proxy di applicazione](../manage-apps/application-proxy-add-on-premises-application.md) | Abilitare Azure AD proxy di applicazione e integrarsi con le app legacy per consentire agli utenti di accedere in modo sicuro alle applicazioni locali accedendo con il proprio account di Azure AD. |
| [Abilitare l'accesso ibrido sicuro: proteggere le app legacy con i controller e le reti di distribuzione delle app esistenti](../manage-apps/secure-hybrid-access.md) (se applicabile). | Consente di pubblicare e proteggere le applicazioni di autenticazione legacy locali e cloud connettendosi a Azure AD con la rete o il controller di distribuzione delle applicazioni esistente. |
| [Integrare le applicazioni SaaS supportate dalla raccolta per Azure AD e abilitare Single Sign-on](../manage-apps/add-application-portal.md) | Azure AD offre una raccolta contenente migliaia di applicazioni preintegrate. Alcune applicazioni usate dall'organizzazione sono probabilmente incluse nella raccolta accessibile direttamente dal portale di Azure. Fornire l'accesso alle applicazioni SaaS aziendali in modalità remota e sicura con l'esperienza utente migliorata (SSO). |
| [Automatizzare il provisioning e il deprovisioning utenti dalle applicazioni SaaS](../app-provisioning/user-provisioning.md) (se applicabile) | Creare automaticamente identità e ruoli utente nelle applicazioni cloud (SaaS) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente durante la modifica dello stato o dei ruoli, aumentando la sicurezza dell'organizzazione. |
| [Abilitare l'accesso condizionale-basato su dispositivo](../conditional-access/require-managed-devices.md) | Migliorare la sicurezza e l'esperienza utente con l'accesso condizionale basato su dispositivo. Questo passaggio garantisce che gli utenti possano accedere solo da dispositivi che soddisfano gli standard di sicurezza e conformità. I dispositivi di questo tipo sono noti anche come dispositivi gestiti. I dispositivi gestiti possono essere conformi a Intune o Azure AD ibrido dispositivi aggiunti. |
| [Abilitare la password di protezione](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Impedire agli utenti di usare password vulnerabili e facili da indovinare. |
| [Designare più di un amministratore globale](../users-groups-roles/directory-emergency-access.md) | Assegnare almeno due account amministratore globale permanenti solo cloud da usare in caso di emergenza. Questi account non sono destinati all'uso quotidiano e devono usare password lunghe e complesse. Gli account break Glass assicurano che sia possibile accedere al servizio in situazioni di emergenza. |
| [Dove possibile, usare ruoli amministrativi non globali](../users-groups-roles/directory-assign-admin-roles.md) | Concedere agli amministratori solo l'accesso necessario esclusivamente alle aree a cui devono accedere. Non tutti gli amministratori devono essere amministratori globali. |
| [Implementare le linee guida per le password di Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Smettere di richiedere agli utenti di modificare la password in base a una pianificazione specifica e disabilitare i requisiti di complessità: gli utenti saranno più propensi a ricordare le loro password e a mantenerle in sicurezza. |
| [Creare un piano per l'accesso degli utenti guest](../external-identities/what-is-b2b.md) | Collaborare con gli utenti Guest consentendo loro di accedere alle app e ai servizi con le proprie identità aziendali, scolastiche o sociali. |
| [Abilita Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Consente di gestire, controllare e monitorare l'accesso a risorse importanti all'interno dell'organizzazione, garantendo agli amministratori l'accesso solo quando necessario e con approvazione |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulla distribuzione per le singole funzionalità di Azure AD, vedere i [piani di distribuzione del progetto Azure ad](active-directory-deployment-plans.md).

- Per un elenco di controllo della distribuzione Azure AD end-to-end, vedere l'articolo [Azure Active Directory Guida alla distribuzione delle funzionalità](active-directory-deployment-checklist-p2.md)