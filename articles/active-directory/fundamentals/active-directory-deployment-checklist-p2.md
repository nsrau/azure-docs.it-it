---
title: Elenco di controllo per la distribuzione di Azure AD
description: Elenco di controllo per la distribuzione delle funzionalità di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9ee0d6fab96c84eee8a520d01d97faddab49f2
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904186"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guida alla distribuzione delle funzionalità di Azure Active Directory

Distribuire Azure Active Directory (Azure AD) per l'organizzazione e garantirne la sicurezza può sembrare un'impresa ardua. Questo articolo illustra le attività comuni che i clienti trovano utile completare in varie fasi, nel corso di 30, 60, 90 o più giorni, per migliorare le condizioni di sicurezza. Anche le organizzazioni che hanno già distribuito Azure AD possono usare questa guida per assicurarsi di sfruttare al meglio i loro investimenti.

Un'infrastruttura delle identità ben pianificata ed eseguita pone le basi per un accesso più sicuro ai carichi di lavoro e ai dati di produttività, solo da utenti e dispositivi noti.

I clienti possono inoltre controllare il loro [punteggio di sicurezza delle identità](identity-secure-score.md) per verificare quanto sono in linea con le procedure consigliate di Microsoft. Controllare il punteggio di sicurezza prima e dopo l'implementazione di questi consigli, per scoprire qual è la situazione rispetto ad altri utenti nel settore e ad altre organizzazioni di dimensioni simili.

## <a name="prerequisites"></a>Prerequisiti

Molti dei consigli in questa guida possono essere implementati con Azure AD Free, Basic o senza alcuna licenza. Nei casi in cui le licenze sono richieste, viene indicata la licenza minima necessaria per completare l'attività.

Altre informazioni sulle licenze sono reperibili nelle pagine seguenti:

* [Licenze di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Informazioni aggiuntive sulle licenze di Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Creare la struttura di base per la sicurezza

In questa fase, gli amministratori abilitano le funzionalità di sicurezza di base per creare una struttura più sicura e facile da usare in Azure AD prima di importare o creare gli account utente normali. Questa fase garantisce l'attivazione di uno stato più sicuro sin all'inizio e che sia necessario presentare i nuovi concetti agli utenti finali una sola volta.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Designare più di un amministratore globale](../users-groups-roles/directory-emergency-access.md) | Assegnare almeno due account amministratore globale permanenti solo cloud da usare in caso di emergenza. Questi account non sono destinati all'uso quotidiano e devono usare password lunghe e complesse. | Azure AD Free |
| [Usare i ruoli amministrativi non globali, laddove possibile](../users-groups-roles/directory-assign-admin-roles.md) | Concedere agli amministratori solo l'accesso necessario esclusivamente alle aree a cui devono accedere. Non tutti gli amministratori devono essere amministratori globali. | Azure AD Free |
| [Abilitare Privileged Identity Management per il rilevamento di utilizzo dei ruoli di amministratore](../privileged-identity-management/pim-getting-started.md) | Abilitare Privileged Identity Management per iniziare a tenere traccia dell'utilizzo dei ruoli amministrativi. | Azure AD Premium P2 |
| [È possibile distribuire la reimpostazione della password self-service](../authentication/howto-sspr-deployment.md) | Ridurre le chiamate all'help desk per la reimpostazione delle password consentendo al personale di reimpostare la propria password usando criteri sotto il controllo di un amministratore. | Azure AD Basic |
| [Creare un elenco di password vietate personalizzate specifiche dell'organizzazione](../authentication/howto-password-ban-bad-configure.md) | Impedire agli utenti di creare password che includono parole o frasi comuni per l'organizzazione o l'area. | Azure AD Basic |
| [Abilitare l'integrazione locale con la protezione con password Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Estendere l'elenco di password escluse alla directory locale, per assicurarsi che le password impostate in locale siano anche conformi con gli elenchi di password escluse globale e specifico del tenant. | Azure AD Premium P1 |
| [Abilitare le linee guida password di Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Smettere di richiedere agli utenti di modificare la password in base a una pianificazione specifica e disabilitare i requisiti di complessità: gli utenti saranno più propensi a ricordare le loro password e a mantenerle in sicurezza. | Azure AD Free |
| [Disabilitare la reimpostazione della password periodici per gli account utente basati sul cloud](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | La reimpostazione della password periodica incoraggia gli utenti a incrementare le password esistenti. Usare le linee guida nel documento dedicato alle password di Microsoft e applicare criteri simili a quelli locali per gli utenti che operano solo nel cloud. | Azure AD Free |
| [Personalizzare il blocco smart Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Evitare che i blocchi per gli utenti basati sul cloud vengano replicati per gli utenti di Active Directory locali. | Azure AD Basic |
| [Abilitare il blocco Smart Extranet per ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Il blocco della Extranet di AD FS protegge contro gli attacchi di forza bruta per l'individuazione delle password, consentendo al contempo agli utenti di AD FS validi di continuare a usare i propri account. | |
| [Distribuire usando i criteri di accesso condizionale di Azure AD multi-Factor Authentication](../authentication/howto-mfa-getstarted.md) | Richiedere agli utenti di eseguire la verifica in due passaggi quando accedono ad applicazioni sensibili tramite criteri di accesso condizionale. | Azure AD Premium P1 |
| [Abilitare Azure Active Directory Identity Protection](../identity-protection/enable.md) | Abilitare il rilevamento degli accessi rischiosi e di violazione delle credenziali per gli utenti dell'organizzazione. | Azure AD Premium P2 |
| [Usare gli eventi di rischio per attivare l'autenticazione a più fattori e le modifiche delle password](../authentication/tutorial-risk-based-sspr-mfa.md) | Abilitare procedure di automazione per attivare eventi come l'autenticazione a più fattori, la reimpostazione della password e il blocco degli accessi in base al rischio. | Azure AD Premium P2 |
| [Abilitare la registrazione per la convergenza per la reimpostazione password self-service e multi-Factor Authentication di Azure AD (anteprima)](../authentication/concept-registration-mfa-sspr-converged.md) | Consentire agli utenti di registrarsi da un'esperienza comune sia per Azure Multi-Factor Authentication che per la reimpostazione della password self-service. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Importare gli utenti, abilitare la sincronizzazione e gestire i dispositivi

Si procede sulla base della struttura definita nella fase 1 con l'importazione degli utenti e l'abilitazione della sincronizzazione, la pianificazione dell'accesso guest e la preparazione per il supporto di funzionalità aggiuntive.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Installare Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Preparare la sincronizzazione degli utenti dalla directory locale esistente al cloud. | Azure AD Free |
| [Implementare la sincronizzazione degli Hash Password](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Sincronizzare gli hash delle password per consentire la replica delle modifiche delle password, il rilevamento e la correzione di password non valide e la segnalazione di credenziali perse. | Azure AD Premium P1 |
| [Implementare il writeback delle Password](../authentication/howto-sspr-writeback.md) | Consentire il writeback delle modifiche delle password nel cloud in un ambiente di Windows Server Active Directory locale. | Azure AD Premium P1 |
| [Implementare Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Abilitare il monitoraggio delle statistiche di integrità della chiave per i server Azure AD Connect, i server AD FS e i controller di dominio. | Azure AD Premium P1 |
| [Assegnare licenze agli utenti in base all'appartenenza ai gruppi in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Risparmiare tempo e lavoro con la creazione di gruppi di licenze che abilitano o disabilitano le funzionalità in base al gruppo anziché impostarle per ogni utente. | |
| [Creare un piano per l'accesso degli utenti guest](../b2b/what-is-b2b.md) | Collaborare con gli utenti guest consentendo loro di accedere alle app e ai servizi dell'organizzazione con le proprie identità aziendali, dell'istituto di istruzione o di social networking. | [Informazioni aggiuntive sulle licenze di Azure AD B2B](../b2b/licensing-guidance.md) |
| [Scelta della strategia di gestione di dispositivi](../devices/overview.md) | Decidere cosa consente l'organizzazione riguardo ai dispositivi. Registrazione o aggiunta, BYOD (Bring Your Own Device) o dispositivi forniti dall'azienda. | |
| [Distribuire Windows Hello for Business nell'organizzazione](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Eseguire i preparativi per l'autenticazione senza password con Windows Hello. | |

## <a name="phase-3-manage-applications"></a>Fase 3: Gestire le applicazioni

Continuando dalle fasi precedenti, in questa fase vengono identificate le applicazioni candidate per la migrazione e integrazione con Azure AD e viene completata la configurazione di tali applicazioni.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| Identificare le applicazioni | Identificare le applicazioni in uso nell'organizzazione: in locale, applicazioni SaaS nel cloud e altre applicazioni line-of-business. Stabilire se queste applicazioni possono e devono essere gestite con Azure AD. | Nessuna licenza richiesta |
| [Integrare applicazioni SaaS supportate nella raccolta](../manage-apps/add-application-portal.md) | Azure AD offre una raccolta contenente migliaia di applicazioni preintegrate. Alcune applicazioni usate dall'organizzazione sono probabilmente incluse nella raccolta accessibile direttamente dal portale di Azure. | Azure AD Free |
| [Usare il Proxy di applicazione per integrare le applicazioni locali](../manage-apps/application-proxy-add-on-premises-application.md) | Il proxy di applicazione consente agli utenti di accedere alle applicazioni locali effettuando l'accesso con il proprio account Azure AD. | Azure AD Basic |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Controllare le identità con privilegi, completare una verifica di accesso e gestire il ciclo di vita degli utenti

Nella fase 4 gli amministratori applicano i principi dei privilegi minimi per l'amministrazione, completano le prime verifiche di accesso e abilitano l'automazione delle attività comuni del ciclo di vita degli utenti.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Imporre l'uso di Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Rimuovere i ruoli amministrativi dagli account utente che devono svolgere normali attività quotidiane. Consentire agli utenti amministratori di usare il proprio ruolo dopo il completamento di un controllo di autenticazione a più fattori, l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte dei responsabili designati. | Azure AD Premium P2 |
| [Completare una verifica di accesso per i ruoli della directory di Azure AD in PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Collaborare con i dirigenti e i team dedicati alla sicurezza per creare criteri di verifica di accesso per controllare l'accesso amministrativo in base alle politiche dell'organizzazione. | Azure AD Premium P2 |
| [Implementare i criteri di appartenenza al gruppo dinamico](../users-groups-roles/groups-dynamic-membership.md) | Usare i gruppi dinamici per assegnare automaticamente gli utenti ai gruppi in base agli attributi definiti dal reparto risorse umane (o altra fonte di fiducia), ad esempio reparto, posizione, area geografica e altri attributi. |  |
| [Implementare il provisioning dell'applicazione di gruppo basato](../manage-apps/what-is-access-management.md) | Usare il provisioning di gestione dell'accesso basato sui gruppi per eseguire automaticamente il provisioning degli utenti per le applicazioni SaaS. |  |
| [Automatizzare il provisioning degli utenti e il deprovisioning utenti](../manage-apps/user-provisioning.md) | Rimuovere i passaggi manuali dal ciclo di vita degli account dei dipendenti per evitare accessi non autorizzati. Sincronizzare le identità dall'origine dei dati (sistema HR) ad Azure AD. |  |

## <a name="next-steps"></a>Passaggi successivi

[I dettagli sui prezzi e licenze di AD Azure](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurazioni di accesso di identità e dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Comuni consigliati i criteri di accesso di identità e dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
