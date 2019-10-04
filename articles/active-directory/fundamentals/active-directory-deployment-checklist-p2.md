---
title: Elenco di controllo per la distribuzione di Azure AD
description: Elenco di controllo per la distribuzione delle funzionalità di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b5678fb0edcecae9ffa6a3c27f3dc6334ecf505
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125334"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guida alla distribuzione delle funzionalità di Azure Active Directory

Distribuire Azure Active Directory (Azure AD) per l'organizzazione e garantirne la sicurezza può sembrare un'impresa ardua. Questo articolo illustra le attività comuni che i clienti trovano utile completare in varie fasi, nel corso di 30, 60, 90 o più giorni, per migliorare le condizioni di sicurezza. Anche le organizzazioni che hanno già distribuito Azure AD possono usare questa guida per assicurarsi di sfruttare al meglio i loro investimenti.

Un'infrastruttura delle identità ben pianificata ed eseguita pone le basi per un accesso più sicuro ai carichi di lavoro e ai dati di produttività, solo da utenti e dispositivi noti.

I clienti possono inoltre controllare il loro [punteggio di sicurezza delle identità](identity-secure-score.md) per verificare quanto sono in linea con le procedure consigliate di Microsoft. Controllare il punteggio di sicurezza prima e dopo l'implementazione di questi consigli, per scoprire qual è la situazione rispetto ad altri utenti nel settore e ad altre organizzazioni di dimensioni simili.

## <a name="prerequisites"></a>Prerequisiti

Molti dei consigli in questa guida possono essere implementati con Azure AD Free o nessuna licenza. Nei casi in cui le licenze sono richieste, viene indicata la licenza minima necessaria per completare l'attività.

Altre informazioni sulle licenze sono reperibili nelle pagine seguenti:

* [Licenze di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Linee guida per le licenze di Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Creare la struttura di base per la sicurezza

In questa fase, gli amministratori abilitano le funzionalità di sicurezza di base per creare una struttura più sicura e facile da usare in Azure AD prima di importare o creare gli account utente normali. Questa fase garantisce l'attivazione di uno stato più sicuro sin all'inizio e che sia necessario presentare i nuovi concetti agli utenti finali una sola volta.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Designare più di un amministratore globale](../users-groups-roles/directory-emergency-access.md) | Assegnare almeno due account amministratore globale permanenti solo cloud da usare in caso di emergenza. Questi account non sono destinati all'uso quotidiano e devono usare password lunghe e complesse. | Azure AD Gratuito |
| [Dove possibile, usare ruoli amministrativi non globali](../users-groups-roles/directory-assign-admin-roles.md) | Concedere agli amministratori solo l'accesso necessario esclusivamente alle aree a cui devono accedere. Non tutti gli amministratori devono essere amministratori globali. | Azure AD Gratuito |
| [Abilitare Privileged Identity Management per il rilevamento dell'uso dei ruoli di amministratore](../privileged-identity-management/pim-getting-started.md) | Abilitare Privileged Identity Management per iniziare a tenere traccia dell'utilizzo dei ruoli amministrativi. | Azure AD P2 Premium |
| [Roll out self-service password reset](../authentication/howto-sspr-deployment.md) (Implementare la reimpostazione della password self-service) | Ridurre le chiamate all'help desk per la reimpostazione delle password consentendo al personale di reimpostare la propria password usando criteri sotto il controllo di un amministratore. | |
| [Creare un elenco di password vietate personalizzate specifiche dell'organizzazione](../authentication/howto-password-ban-bad-configure.md) | Impedire agli utenti di creare password che includono parole o frasi comuni per l'organizzazione o l'area. | |
| [Abilitare l'integrazione locale con password di protezione di Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Estendere l'elenco di password escluse alla directory locale, per assicurarsi che le password impostate in locale siano anche conformi con gli elenchi di password escluse globale e specifico del tenant. | Azure AD P1 Premium |
| [Implementare le linee guida per le password di Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Smettere di richiedere agli utenti di modificare la password in base a una pianificazione specifica e disabilitare i requisiti di complessità: gli utenti saranno più propensi a ricordare le loro password e a mantenerle in sicurezza. | Azure AD Gratuito |
| [Disabilitare le reimpostazioni delle password periodiche per gli account utente basati sul cloud](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | La reimpostazione della password periodica incoraggia gli utenti a incrementare le password esistenti. Usare le linee guida nel documento dedicato alle password di Microsoft e applicare criteri simili a quelli locali per gli utenti che operano solo nel cloud. | Azure AD Gratuito |
| [Personalizzare il blocco intelligente di Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Evitare che i blocchi per gli utenti basati sul cloud vengano replicati per gli utenti di Active Directory locali. | |
| [Abilitare il blocco intelligente della Extranet di AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Il blocco della Extranet di AD FS protegge contro gli attacchi di forza bruta per l'individuazione delle password, consentendo al contempo agli utenti di AD FS validi di continuare a usare i propri account. | |
| [Distribuire Azure AD Multi-Factor Authentication usando i criteri di accesso condizionale](../authentication/howto-mfa-getstarted.md) | Richiedere agli utenti di eseguire la verifica in due passaggi per l'accesso alle applicazioni sensibili mediante i criteri di accesso condizionale. | Azure AD P1 Premium |
| [Abilitazione di Azure Active Directory Identity Protection](../identity-protection/enable.md) | Abilitare il rilevamento degli accessi rischiosi e di violazione delle credenziali per gli utenti dell'organizzazione. | Azure AD P2 Premium |
| [Usare i rilevamenti di rischio per attivare l'autenticazione a più fattori e le modifiche delle password](../authentication/tutorial-risk-based-sspr-mfa.md) | Abilitare procedure di automazione per attivare eventi come l'autenticazione a più fattori, la reimpostazione della password e il blocco degli accessi in base al rischio. | Azure AD P2 Premium |
| [Registrazione convergente per la reimpostazione password self-service e Azure Multi-Factor Authentication (anteprima pubblica)](../authentication/concept-registration-mfa-sspr-converged.md) | Consentire agli utenti di registrarsi da un'esperienza comune sia per Azure Multi-Factor Authentication che per la reimpostazione della password self-service. | Azure AD P1 Premium |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Importare gli utenti, abilitare la sincronizzazione e gestire i dispositivi

Si procede sulla base della struttura definita nella fase 1 con l'importazione degli utenti e l'abilitazione della sincronizzazione, la pianificazione dell'accesso guest e la preparazione per il supporto di funzionalità aggiuntive.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Installare Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Preparare la sincronizzazione degli utenti dalla directory locale esistente al cloud. | Azure AD Gratuito |
| [Implementare la sincronizzazione dell'hash delle password](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Sincronizzare gli hash delle password per consentire la replica delle modifiche delle password, il rilevamento e la correzione di password non valide e la segnalazione di credenziali perse. | Azure AD Gratuito |
| [Procedura: Configurare il writeback delle password](../authentication/howto-sspr-writeback.md) | Consentire il writeback delle modifiche delle password nel cloud in un ambiente di Windows Server Active Directory locale. | Azure AD P1 Premium |
| [Implementare Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Abilitare il monitoraggio delle statistiche di integrità della chiave per i server Azure AD Connect, i server AD FS e i controller di dominio. | Azure AD P1 Premium |
| [Assegnare licenze agli utenti in base all'appartenenza ai gruppi in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Risparmiare tempo e lavoro con la creazione di gruppi di licenze che abilitano o disabilitano le funzionalità in base al gruppo anziché impostarle per ogni utente. | |
| [Creare un piano per l'accesso degli utenti guest](../b2b/what-is-b2b.md) | Collaborare con gli utenti guest consentendo loro di accedere alle app e ai servizi dell'organizzazione con le proprie identità aziendali, dell'istituto di istruzione o di social networking. | [Linee guida per le licenze di Azure AD B2B](../b2b/licensing-guidance.md) |
| [Stabilire la strategia di gestione dei dispositivi](../devices/overview.md) | Decidere cosa consente l'organizzazione riguardo ai dispositivi. Registrazione o aggiunta, BYOD (Bring Your Own Device) o dispositivi forniti dall'azienda. | |
| [Distribuire Windows Hello for Business nell'organizzazione](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Prepararsi per l'autenticazione con password con Windows Hello | |
| [Distribuire metodi di autenticazione con password per gli utenti](../authentication/concept-authentication-passwordless.md) | Fornire agli utenti un pratico metodo di autenticazione senza password | Azure AD P1 Premium |

## <a name="phase-3-manage-applications"></a>Fase 3: Gestisci le applicazioni

Continuando dalle fasi precedenti, in questa fase vengono identificate le applicazioni candidate per la migrazione e integrazione con Azure AD e viene completata la configurazione di tali applicazioni.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| Identificare le applicazioni | Identificare le applicazioni in uso nell'organizzazione: in locale, applicazioni SaaS nel cloud e altre applicazioni line-of-business. Stabilire se queste applicazioni possono e devono essere gestite con Azure AD. | Nessuna licenza richiesta |
| [Integrare applicazioni SaaS supportate nella raccolta](../manage-apps/add-application-portal.md) | Azure AD offre una raccolta contenente migliaia di applicazioni preintegrate. Alcune applicazioni usate dall'organizzazione sono probabilmente incluse nella raccolta accessibile direttamente dal portale di Azure. | Azure AD Gratuito |
| [Usare il proxy di applicazione per integrare le applicazioni locali](../manage-apps/application-proxy-add-on-premises-application.md) | Il proxy di applicazione consente agli utenti di accedere alle applicazioni locali effettuando l'accesso con il proprio account Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Controllare le identità con privilegi, completare una verifica di accesso e gestire il ciclo di vita degli utenti

Nella fase 4 gli amministratori applicano i principi dei privilegi minimi per l'amministrazione, completano le prime verifiche di accesso e abilitano l'automazione delle attività comuni del ciclo di vita degli utenti.

| Attività | Dettagli | Licenza richiesta |
| ---- | ------ | ---------------- |
| [Imporre l'uso di Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Rimuovere i ruoli amministrativi dagli account utente che devono svolgere normali attività quotidiane. Consentire agli utenti amministratori di usare il proprio ruolo dopo il completamento di un controllo di autenticazione a più fattori, l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte dei responsabili designati. | Azure AD P2 Premium |
| [Completare una verifica di accesso per i ruoli della directory di Azure AD in PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Collaborare con i dirigenti e i team dedicati alla sicurezza per creare criteri di verifica di accesso per controllare l'accesso amministrativo in base alle politiche dell'organizzazione. | Azure AD P2 Premium |
| [Implementare i criteri di appartenenza a gruppi dinamica](../users-groups-roles/groups-dynamic-membership.md) | Usare i gruppi dinamici per assegnare automaticamente gli utenti ai gruppi in base agli attributi definiti dal reparto risorse umane (o altra fonte di fiducia), ad esempio reparto, posizione, area geografica e altri attributi. |  |
| [Implementare il provisioning delle applicazioni basato sui gruppi](../manage-apps/what-is-access-management.md) | Usare il provisioning di gestione dell'accesso basato sui gruppi per eseguire automaticamente il provisioning degli utenti per le applicazioni SaaS. |  |
| [Automatizzare il provisioning e il deprovisioning degli utenti](../manage-apps/user-provisioning.md) | Rimuovere i passaggi manuali dal ciclo di vita degli account dei dipendenti per evitare accessi non autorizzati. Sincronizzare le identità dall'origine dei dati (sistema HR) ad Azure AD. |  |

## <a name="next-steps"></a>Passaggi successivi

[Dettagli su prezzi e licenze per Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurazioni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Criteri comuni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
