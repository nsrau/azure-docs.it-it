---
title: Guida di riferimento alle operazioni di governance di Azure Active DirectoryAzure Active Directory governance operations reference guide
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da intraprendere per proteggere la gestione della governance
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535457"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guida di riferimento alle operazioni di governance di Azure Active DirectoryAzure Active Directory governance operations reference guide

Questa sezione della guida di riferimento alle operazioni di [Azure AD](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per valutare e attestare l'accesso concesso a identità senza privilegi e privilegiati, il controllo e le modifiche di controllo all'ambiente.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie pratiche di governance man mano che i prodotti e i servizi Microsoft si evolvono nel tempo.

## <a name="key-operational-processes"></a>Processi operativi chiave

### <a name="assign-owners-to-key-tasks"></a>Assegnare proprietari ad attività chiave

La gestione di Azure Active Directory richiede l'esecuzione continua di processi e attività operative chiave, che potrebbero non far parte di un progetto di implementazione. È comunque importante impostare queste attività per ottimizzare l'ambiente. Le attività chiave e i loro proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Archiviare i log di controllo di Azure AD nel sistema SIEMArchive Azure AD audit logs in SIEM system | Team operativo InfoSec |
| Individuazione delle applicazioni gestite in base alla conformità | Team operativo IAM |
| Rivedere regolarmente l'accesso alle applicazioni | Team di architettura InfoSec |
| Esaminare regolarmente l'accesso alle identità esterne | Team di architettura InfoSec |
| Esaminare regolarmente chi ha ruoli privilegiati | Team di architettura InfoSec |
| Definire i gate di sicurezza per attivare i ruoli con privilegiDefine security gates to activate privileged roles | Team di architettura InfoSec |
| Esaminare regolarmente le sovvenzioni di consenso | Team di architettura InfoSec |
| Progettare cataloghi e pacchetti di accessi per applicazioni e risorse in base ai dipendenti dell'organizzazione | Proprietari di app |
| Definire i criteri di sicurezza per assegnare gli utenti ai pacchetti di accessoDefine Security Policies to assign users to access packages | Team InfoSec - Proprietari di app |
| Se i criteri includono flussi di lavoro di approvazione, esaminare regolarmente le approvazioni dei flussi di lavoro | Proprietari di app |
| Esaminare le eccezioni nei criteri di sicurezza, ad esempio i criteri di accesso condizionale, utilizzando le verifiche di accessoReview exceptions in security policies, such as conditional access policies, using access reviews | Team operativo InfoSec |

Durante la revisione dell'elenco, potrebbe essere necessario assegnare un proprietario alle attività in cui manca un proprietario o modificare la proprietà per le attività con proprietari non allineati con i suggerimenti precedenti.

#### <a name="owner-recommended-reading"></a>Lettura consigliata dal proprietario

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Test delle modifiche alla configurazione

Esistono modifiche che richiedono considerazioni speciali durante il test, da tecniche semplici, ad esempio l'implementazione di un sottoinsieme di utenti di destinazione alla distribuzione di una modifica in un tenant di test parallelo. Se non è stata implementata una strategia di test, è necessario definire un approccio di test in base alle linee guida riportate nella tabella seguente:If you haven't implemented a testing strategy, you should define a test approach based on the guidelines in the table below:

| Scenario| Recommendation |
|-|-|
|Modifica del tipo di autenticazione da federato a PHS/PTA o viceversa| Usare [l'implementazione in fasi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) per verificare l'impatto della modifica del tipo di autenticazione.|
|Implementazione di un nuovo criterio di accesso condizionale (CA) o di criteri di protezione delle identità|Creare un nuovo criterio CA e assegnarlo agli utenti di test.|
|Onboarding di un ambiente di test di un'applicazioneOnboarding a test environment of an application|Aggiungere l'applicazione a un ambiente di produzione, nasconderla dal pannello MyApps e assegnarla agli utenti di test durante la fase di controllo qualità (QA).|
|Modifica delle regole di sincronizzazione|Eseguire le modifiche in un Azure AD Connect di test con la stessa configurazione attualmente in produzione, nota anche come modalità di gestione temporanea, e analizzare i risultati CSExport.Perform the changes in a test Azure AD Connect with the same configuration that is currently in production, also known as staging mode, and analyze CSExport Results. Se soddisfatto, passare alla produzione quando è pronto.|
|Cambio di branding|Eseguire il test in un tenant di test separato.|
|Implementazione di una nuova funzionalità|Se la funzionalità supporta l'implementazione in un set di utenti di destinazione, identificare gli utenti pilota e compilare. Ad esempio, la reimpostazione della password self-service e l'autenticazione a più fattori possono essere destinati a utenti o gruppi specifici.|
|Tagliare un'applicazione da un provider di identità (IdP) locale, ad esempio Active Directory, ad Azure ADCutover an application from an on-premises Identity provider (IdP), for example, Active Directory, to Azure AD|Se l'applicazione supporta più configurazioni IdP, ad esempio Salesforce, configurare entrambe e testare Azure AD durante una finestra di modifica (nel caso in cui l'applicazione introducono la pagina HRD). Se l'applicazione non supporta più IdP, pianificare il test durante una finestra di controllo delle modifiche e i tempi di inattività del programma.|
|Aggiornare le regole dei gruppi dinamici|Creare un gruppo dinamico parallelo con la nuova regola. Confrontare con il risultato calcolato, ad esempio, eseguire PowerShell con la stessa condizione.<br>Se il test è stato superato, scambiare i luoghi in cui è stato utilizzato il gruppo precedente (se possibile).|
|Eseguire la migrazione delle licenze dei prodotti|Fare riferimento a [Modificare la licenza per un singolo utente in un gruppo con licenza in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Modificare le regole di ADFS, ad esempio autorizzazione, rilascio, autenticazione a più fattoriChange AD FS rules such as Authorization, Issuance, MFA|Utilizzare l'attestazione di gruppo per assegnare un sottoinsieme di utenti.|
|Modificare l'esperienza di autenticazione di ADFS o modifiche simili a livello di farmChange AD FS authentication experience or similar farm-wide changes|Creare una farm parallela con lo stesso nome host, implementare le modifiche di configurazione, eseguire test dai client utilizzando il file HOSTS, le regole di routing di Bilanciamento n.<br>Se la piattaforma di destinazione non supporta i file HOSTS (ad esempio i dispositivi mobili), il controllo cambia.|

## <a name="access-reviews"></a>Verifiche di accesso

### <a name="access-reviews-to-applications"></a>Accedere alle recensioni alle applicazioni

Nel corso del tempo, gli utenti possono accumulare l'accesso alle risorse mentre si spostano in diversi team e posizioni. È importante che i proprietari delle risorse esaminino regolarmente l'accesso alle applicazioni e rimuovano i privilegi che non sono più necessari per tutto il ciclo di vita degli utenti. Le [verifiche](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) di accesso di Azure AD consentono alle organizzazioni di gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. I proprietari delle risorse devono esaminare regolarmente l'accesso degli utenti per assicurarsi che solo le persone giuste abbiano accesso continuo. Idealmente, è consigliabile usare le verifiche di accesso di Azure AD per questa attività.

![Pagina iniziale delle verifiche di accesso](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Ogni utente che interagisce con le verifiche di accesso deve disporre di una licenza di Azure AD Premium P2 a pagamento.

### <a name="access-reviews-to-external-identities"></a>Accedere alle revisioni per le identità esterne

È fondamentale mantenere l'accesso alle identità esterne limitato solo alle risorse necessarie, durante il tempo necessario. Stabilire un processo di verifica automatica dell'accesso regolare per tutte le identità esterne e l'accesso alle applicazioni tramite [le verifiche](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)di accesso di Azure AD. Se un processo esiste già in locale, è consigliabile usare le verifiche di accesso di Azure AD. Una volta che un'applicazione viene ritirata o non viene più utilizzata, rimuovere tutte le identità esterne che avevano accesso all'applicazione.

> [!NOTE]
> Ogni utente che interagisce con le verifiche di accesso deve disporre di una licenza di Azure AD Premium P2 a pagamento.

## <a name="privileged-account-management"></a>Gestione degli account con privilegi

### <a name="privileged-account-usage"></a>Utilizzo dell'account con privilegi

Gli hacker spesso prendono di mira gli account amministratore e altri elementi di accesso privilegiato per ottenere rapidamente l'accesso a dati e sistemi sensibili.Poiché gli utenti con ruoli privilegiati tendono ad accumularsi nel tempo, è importante esaminare e gestire regolarmente l'accesso di amministratore e fornire l'accesso con privilegi just-in-time alle risorse di Azure AD e Azure.Since users with privileged roles tend to accumulate in time, it is important to review and manage admin access on a regular basis and provide just-in-time privileged access to Azure AD and Azure resources.

Se nell'organizzazione non esiste alcun processo per gestire gli account con privilegi o se attualmente si dispone di amministratori che utilizzano i loro account utente regolari per gestire servizi e risorse, è necessario iniziare immediatamente a utilizzare account separati, ad esempio uno per i normali aggiornamenti giornalieri attività; l'altro per l'accesso con privilegi e configurato con l'autenticazione a più fattori. Meglio ancora, se l'organizzazione ha una sottoscrizione di Azure AD Premium P2, è necessario distribuire immediatamente [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). Nello stesso token, è inoltre necessario esaminare tali account con privilegi e [assegnare ruoli meno privilegiati,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) se applicabile.

Un altro aspetto della gestione degli account con privilegi che deve essere implementato è la definizione delle verifiche di [accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) per tali account, manualmente o [automatizzati tramite PIM.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)

#### <a name="privileged-account-management-recommended-reading"></a>La gestione degli account con privilegi ha raccomandato la lettura

- [Ruoli in Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Le organizzazioni devono creare account di [emergenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) per essere preparati a gestire Azure AD per casi come le interruzioni di autenticazione, ad esempio:Organizations must create emergency accounts to be prepared to manage Azure AD for cases such as authentication outages like:

- Componenti di interruzione delle infrastrutture di autenticazione (ADFS, AD locale, servizio Multiautente)Outage components of authentication infrastructures (AD FS, On-premises AD, MFA service)
- Turnover del personale amministrativo

Per evitare di essere inavvertitamente bloccato dal tenant perché non è possibile accedere o attivare l'account di un singolo utente esistente come amministratore, è necessario creare due o più account di emergenza e assicurarsi che siano implementati e allineati con [le procedure consigliate](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) di Microsoft e le procedure di interruzione del [vetro](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Accesso con privilegi al portale di Azure EA

Il [portale Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) consente di creare sottoscrizioni di Azure rispetto a un contratto Enterprise Master, che è un ruolo potente all'interno dell'azienda. È comune eseguire il bootstrap della creazione di questo portale prima ancora di ottenere Azure AD sul posto, pertanto è necessario usare le identità di Azure AD per bloccarlo, rimuovere gli account personali dal portale, assicurarsi che sia presente la delega corretta e ridurre il rischio di blocco .

Per essere chiari, se il livello di autorizzazione del portale EA è attualmente impostato su "modalità mista", è necessario rimuovere tutti gli account Microsoft da tutti gli [accessi](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) con privilegi nel portale EA e configurare il portale EA per l'utilizzo solo degli account di Azure AD. Se i ruoli delegati del portale EA non sono configurati, è necessario trovare e implementare anche i ruoli delegati per i reparti e gli account.

#### <a name="privileged-access-recommended-reading"></a>Accesso privilegiato lettura consigliata

- [Autorizzazioni del ruolo di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Gestione entitlement

[La gestione dei diritti (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) consente ai proprietari di app di raggruppare le risorse e di assegnarle a persone specifiche nell'organizzazione (sia interne che esterne). EM consente l'iscrizione self-service e la delega ai proprietari aziendali, mantenendo i criteri di governance per concedere l'accesso, impostare la durata dell'accesso e consentire flussi di lavoro di approvazione. 

> [!NOTE]
> La gestione dei diritti di Azure AD richiede licenze di Azure AD Premium P2.

## <a name="summary"></a>Riepilogo

Ci sono otto aspetti per una governance sicura dell'identità. Questo elenco consente di identificare le azioni da intraprendere per valutare e attestare l'accesso concesso alle identità non privilegiate e con privilegi, al controllo e alle modifiche di controllo all'ambiente.

- Assegnare i proprietari alle attività chiave.
- Implementare una strategia di test.
- Usare le verifiche di accesso di Azure AD per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo.
- Stabilire un processo di verifica dell'accesso regolare e automatizzato per tutti i tipi di identità esterne e l'accesso alle applicazioni.
- Stabilire un processo di verifica dell'accesso per esaminare e gestire regolarmente l'accesso di amministratore e fornire l'accesso con privilegi just-in-time alle risorse di Azure AD e Azure.Establish an access review process to review and manage admin access on a regular basis and provide just-in-time privileged access to Azure AD and Azure resources.
- Effettuare il provisioning degli account di emergenza per essere preparati a gestire Azure AD per interruzioni impreviste.
- Bloccare l'accesso al portale di Azure EA.Lock down access to the Azure EA portal.
- Implementare Gestione diritti per fornire l'accesso gestito a una raccolta di risorse.

## <a name="next-steps"></a>Passaggi successivi

Introduzione [ai controlli operativi e](active-directory-ops-guide-ops.md)alle azioni di Azure AD.
