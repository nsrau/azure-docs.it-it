---
title: Guida di riferimento per le operazioni di Azure Active Directory governance
description: Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere la gestione della governance
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
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535457"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Guida di riferimento per le operazioni di Azure Active Directory governance

Questa sezione della [Guida di riferimento alle operazioni Azure ad](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per valutare e attestare l'accesso concesso alle identità senza privilegi e con privilegi, controllare e controllare le modifiche apportate all'ambiente.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di governance, perché i prodotti e i servizi Microsoft evolvono nel tempo.

## <a name="key-operational-processes"></a>Principali processi operativi

### <a name="assign-owners-to-key-tasks"></a>Assegnare i proprietari alle attività principali

La gestione di Azure Active Directory richiede l'esecuzione continua delle attività e dei processi operativi principali, che potrebbero non essere parte di un progetto di implementazione. È ancora importante configurare queste attività per ottimizzare l'ambiente. Le attività principali e i proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Archiviare i log di controllo Azure AD nel sistema SIEM | Team operativo di InfoSec |
| Individuare le applicazioni che sono gestite fuori conformità | Team operativo IAM |
| Verifica regolarmente l'accesso alle applicazioni | Team di architettura InfoSec |
| Verifica regolarmente l'accesso alle identità esterne | Team di architettura InfoSec |
| Esaminare periodicamente chi ha ruoli con privilegi | Team di architettura InfoSec |
| Definire i controlli di sicurezza per l'attivazione dei ruoli con privilegi | Team di architettura InfoSec |
| Verifica regolarmente le concessioni di consenso | Team di architettura InfoSec |
| Progettare cataloghi e accedere ai pacchetti per le applicazioni e le risorse in base ai dipendenti dell'organizzazione | Proprietari dell'app |
| Definire i criteri di sicurezza per assegnare gli utenti per l'accesso ai pacchetti | InfoSec team + proprietari app |
| Se i criteri includono i flussi di lavoro di approvazione, controllare regolarmente le approvazioni del flusso | Proprietari dell'app |
| Esaminare le eccezioni nei criteri di sicurezza, ad esempio i criteri di accesso condizionale, usando le verifiche di accesso | Team operativo di InfoSec |

Quando si esamina l'elenco, è possibile che sia necessario assegnare un proprietario per le attività che non dispongono di un proprietario o modificare la proprietà per le attività che non sono allineate alle raccomandazioni precedenti.

#### <a name="owner-recommended-reading"></a>Lettura consigliata dal proprietario

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Test delle modifiche di configurazione

Sono presenti modifiche che richiedono considerazioni speciali durante i test, da semplici tecniche, ad esempio la distribuzione di un subset di destinazione di utenti alla distribuzione di una modifica in un tenant di test parallelo. Se non è stata implementata una strategia di test, è necessario definire un approccio di test in base alle linee guida riportate nella tabella seguente:

| Scenario| Recommendation |
|-|-|
|Modifica del tipo di autenticazione da federato a pH/PTA o viceversa| Usare l' [implementazione temporanea](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) per verificare l'effetto della modifica del tipo di autenticazione.|
|Implementazione di nuovi criteri di accesso condizionale (CA) o di protezione delle identità|Creare un nuovo criterio CA e assegnarlo agli utenti di test.|
|Onboarding di un ambiente di test di un'applicazione|Aggiungere l'applicazione a un ambiente di produzione, nasconderla dal pannello app e assegnarla agli utenti di test durante la fase di controllo di qualità (QA).|
|Modifica delle regole di sincronizzazione|Eseguire le modifiche in un Azure AD Connect di test con la stessa configurazione attualmente in produzione, nota anche come modalità di staging, e analizzare i risultati di CSExport. Se soddisfatta, lo scambio in produzione è pronto.|
|Modifica della personalizzazione|Eseguire il test in un tenant di test separato.|
|Implementazione di una nuova funzionalità|Se la funzionalità supporta la distribuzione a un set di utenti di destinazione, identificare gli utenti pilota e compilare. Ad esempio, la reimpostazione della password self-service e l'autenticazione a più fattori possono avere come destinazione utenti o gruppi specifici.|
|Cutover un'applicazione da un provider di identità locale (IdP), ad esempio Active Directory, a Azure AD|Se l'applicazione supporta più configurazioni IdP, ad esempio Salesforce, configurare e testare Azure AD durante una finestra di modifica (nel caso in cui l'applicazione introduca la pagina HRD). Se l'applicazione non supporta più IDP, pianificare i test durante la finestra di controllo delle modifiche e il tempo di inattività del programma.|
|Aggiornare le regole del gruppo dinamico|Creare un gruppo dinamico parallelo con la nuova regola. Confrontare con il risultato calcolato, ad esempio, eseguire PowerShell con la stessa condizione.<br>Se il test viene superato, scambiare le posizioni in cui è stato usato il gruppo precedente (se possibile).|
|Esegui migrazione licenze prodotto|Fare riferimento a [modificare la licenza per un singolo utente in un gruppo con licenza in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Modificare AD FS regole, ad esempio autorizzazione, emissione, autenticazione a più fattori|Usare l'attestazione del gruppo per fare riferimento a subset di utenti.|
|Modificare AD FS esperienza di autenticazione o modifiche simili a livello di farm|Creazione di una farm parallela con lo stesso nome host, implementazione delle modifiche di configurazione, test da client con file host, regole di routing NLB o routing analogo.<br>Se la piattaforma di destinazione non supporta i file host, ad esempio i dispositivi mobili, modificare il controllo.|

## <a name="access-reviews"></a>Verifiche di accesso

### <a name="access-reviews-to-applications"></a>Verifiche di accesso alle applicazioni

Nel corso del tempo, gli utenti possono accumulare l'accesso alle risorse man mano che si spostano in team e posizioni differenti. È importante che i proprietari delle risorse rivedano l'accesso alle applicazioni a intervalli regolari e rimuovano i privilegi che non sono più necessari per tutto il ciclo di vita degli utenti. Azure AD le verifiche di [accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. I proprietari delle risorse devono verificare l'accesso degli utenti a intervalli regolari per assicurarsi che solo le persone giuste abbiano accesso continuo. Idealmente, è consigliabile usare Azure AD le verifiche di accesso per questa attività.

![Pagina iniziale delle verifiche di accesso](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Ogni utente che interagisce con le verifiche di accesso deve disporre di una licenza a pagamento Azure AD Premium P2.

### <a name="access-reviews-to-external-identities"></a>Verifiche di accesso a identità esterne

È fondamentale garantire l'accesso alle identità esterne vincolate solo alle risorse necessarie, durante il periodo di tempo necessario. Stabilire un processo di verifica dell'accesso automatico normale per tutte le identità esterne e l'accesso alle applicazioni usando le verifiche di [accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)Azure ad. Se un processo esiste già in locale, provare a usare Azure AD le verifiche di accesso. Quando un'applicazione viene ritirata o non più utilizzata, rimuovere tutte le identità esterne che hanno accesso all'applicazione.

> [!NOTE]
> Ogni utente che interagisce con le verifiche di accesso deve disporre di una licenza a pagamento Azure AD Premium P2.

## <a name="privileged-account-management"></a>Gestione degli account con privilegi

### <a name="privileged-account-usage"></a>Utilizzo account con privilegi

Gli hacker hanno spesso come destinazione gli account di amministratore e altri elementi di accesso con privilegi per ottenere rapidamente l'accesso a dati e sistemi sensibili. Poiché gli utenti con ruoli con privilegi tendono a accumularsi nel tempo, è importante rivedere e gestire l'accesso amministrativo a intervalli regolari e fornire l'accesso con privilegi JIT per Azure AD e le risorse di Azure.

Se nell'organizzazione non è presente alcun processo per la gestione degli account con privilegi o se al momento sono presenti amministratori che usano gli account utente normali per gestire i servizi e le risorse, è consigliabile iniziare immediatamente a usare account distinti, ad esempio uno per la normalità giornaliera attività l'altro per l'accesso con privilegi e configurato con l'autenticazione a più fattori. Meglio ancora, se l'organizzazione ha una sottoscrizione Azure AD Premium P2, è necessario distribuire immediatamente [Azure ad Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). Nello stesso token è inoltre consigliabile esaminare gli account con privilegi e [assegnare i ruoli con privilegi di minore](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) , se applicabile.

Un altro aspetto della gestione degli account con privilegi da implementare è la definizione delle verifiche di [accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) per tali account, sia manualmente che [automatizzati tramite PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Lettura consigliata per la gestione degli account con privilegi

- [Ruoli in Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Le organizzazioni devono creare [account di emergenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) per prepararsi alla gestione di Azure ad per casi come le interruzioni dell'autenticazione come:

- Interruzioni dei componenti delle infrastrutture di autenticazione (AD FS, AD locale, servizio multi-factor authentication)
- Turnover del personale amministrativo

Per impedire che il tenant venga bloccato inavvertitamente perché non è possibile accedere o attivare un account utente singolo esistente come amministratore, è necessario creare due o più account di emergenza e assicurarsi che siano implementati e allineati con le procedure consigliate di [Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) e [le procedure per le interruzioni](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Accesso con privilegi al portale EA di Azure

Il [portale di azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) consente di creare sottoscrizioni di Azure per un Enterprise Agreement Master, che è un ruolo potente all'interno dell'azienda. È comune avviare la creazione di questo portale prima di Azure AD sul posto, quindi è necessario usare Azure AD identità per bloccarlo, rimuovere gli account personali dal portale, assicurarsi che sia presente la delega appropriata e mitigare il rischio di blocco .

Per chiarire, se il livello di autorizzazione del portale EA è attualmente impostato su "modalità mista", è necessario rimuovere tutti gli [account Microsoft](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) da tutti gli accessi con privilegi nel portale EA e configurare il portale EA per l'uso di Azure ad solo account. Se i ruoli delegati del portale EA non sono configurati, è inoltre necessario trovare e implementare i ruoli delegati per i reparti e gli account.

#### <a name="privileged-access-recommended-reading"></a>Lettura consigliata per l'accesso con privilegi

- [Autorizzazioni del ruolo di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Gestione entitlement

[Gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) consente ai proprietari di app di aggregare le risorse e di assegnarle a utenti specifici nell'organizzazione (sia interne che esterne). EM consente l'iscrizione self-service e la delega ai proprietari aziendali, mantenendo al tempo stesso i criteri di governance per concedere l'accesso, impostare la durata dell'accesso e consentire i flussi di lavoro di approvazione. 

> [!NOTE]
> Azure AD la gestione dei diritti richiede Azure AD Premium le licenze P2.

## <a name="summary"></a>Summary

Ci sono otto aspetti per una governance di identità sicura. Questo elenco consente di identificare le azioni da intraprendere per valutare e attestare l'accesso concesso alle identità senza privilegi e con privilegi, controllare e controllare le modifiche apportate all'ambiente.

- Assegnare i proprietari alle attività principali.
- Implementare una strategia di test.
- Usare le verifiche di accesso Azure AD per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo.
- Definire un processo di verifica dell'accesso automatizzato regolare per tutti i tipi di identità esterne e l'accesso alle applicazioni.
- Stabilire un processo di verifica dell'accesso per rivedere e gestire l'accesso amministrativo a intervalli regolari e fornire accesso con privilegi JIT per Azure AD e risorse di Azure.
- Effettuare il provisioning di account di emergenza per prepararsi a gestire Azure AD per interruzioni impreviste.
- Bloccare l'accesso al portale EA di Azure.
- Implementare la gestione dei diritti per fornire l'accesso regolamentato a una raccolta di risorse.

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare i [controlli e le azioni Azure ad operativi](active-directory-ops-guide-ops.md).
