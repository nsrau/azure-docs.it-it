---
title: Guida di riferimento alle operazioni di gestione di identità e accessi di Azure Active DirectoryAzure Active Directory Identity and access management operations reference guide
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da eseguire per proteggere le operazioni di gestione di identità e accessi
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298615"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guida di riferimento alle operazioni di gestione di identità e accessi di Azure Active DirectoryAzure Active Directory Identity and access management operations reference guide

Questa sezione della guida di riferimento alle operazioni di [Azure AD](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da considerare per proteggere e gestire il ciclo di vita delle identità e le relative assegnazioni.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità man mano che i prodotti e i servizi Microsoft si evolvono nel tempo.

## <a name="key-operational-processes"></a>Processi operativi chiave

### <a name="assign-owners-to-key-tasks"></a>Assegnare proprietari ad attività chiave

La gestione di Azure Active Directory richiede l'esecuzione continua di attività e processi operativi chiave che potrebbero non far parte di un progetto di implementazione. È comunque importante impostare queste attività per mantenere l'ambiente. Le attività chiave e i loro proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Definire il processo di creazione delle sottoscrizioni di AzureDefine the process how to create Azure subscriptions | Varia in base all'organizzazione |
| Decidere chi ottiene le licenze Enterprise Mobility e sicurezza | Team operativo IAM |
| Decidere chi ottiene le licenze di Office 365 | Team per la produttività |
| Decidere chi ottiene altre licenze, ad esempio Dynamics, VSO | Proprietario dell'applicazione |
| Assegnare licenze | Team operativo IAM |
| Risolvere gli errori di assegnazione delle licenze | Team operativo IAM |
| Effettuare il provisioning delle identità alle applicazioni in Azure ADProvision identities to applications in Azure AD | Team operativo IAM |

Durante la revisione dell'elenco, potrebbe essere necessario assegnare un proprietario alle attività in cui manca un proprietario o modificare la proprietà per le attività con proprietari non allineati con i suggerimenti precedenti.

#### <a name="assigning-owners-recommended-reading"></a>Assegnazione lettura consigliata ai proprietari

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Sincronizzazione delle identità locale

### <a name="identify-and-resolve-synchronization-issues"></a>Identificare e risolvere i problemi di sincronizzazione

Microsoft consiglia di disporre di una buona linea di base e di comprendere i problemi dell'ambiente locale che possono causare problemi di sincronizzazione nel cloud. Poiché gli strumenti automatizzati come [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) possono generare un volume elevato di falsi positivi, è consigliabile identificare gli errori di sincronizzazione che non sono stati risolti per più di 100 giorni pulendo tali oggetti per errore. Gli errori di sincronizzazione non risolti a lungo termine possono generare eventi imprevisti di supporto. [La risoluzione degli errori durante](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) la sincronizzazione fornisce una panoramica dei diversi tipi di errori di sincronizzazione, alcuni dei possibili scenari che causano tali errori e potenziali modi per correggere gli errori.

### <a name="azure-ad-connect-sync-configuration"></a>Configurazione della sincronizzazione di Azure AD ConnectAzure AD Connect Sync configuration

Per abilitare tutte le esperienze ibride, la sicurezza basata sui dispositivi e l'integrazione con Azure AD, è necessario sincronizzare gli account utente utilizzati dai dipendenti per accedere ai desktop.

Se non si sincronizzano gli utenti della foresta accedere, è necessario modificare la sincronizzazione in modo che provenga dalla foresta appropriata.

#### <a name="synchronization-scope-and-object-filtering"></a>Ambito di sincronizzazione e filtro oggetti

La rimozione di bucket noti di oggetti che non devono essere sincronizzati comporta i seguenti vantaggi operativi:

- Meno origini di errori di sincronizzazione
- Cicli di sincronizzazione più rapidi
- Meno "spazzatura" da portare avanti dall'ambiente locale, ad esempio, l'inquinamento dell'elenco indirizzi globale per gli account di servizio locali che non sono rilevanti nel cloud

> [!NOTE]
> Se si importano molti oggetti che non vengono esportati nel cloud, è necessario filtrare in base all'unità organizzativa o a attributi specifici.

Esempi di oggetti da escludere sono:

- Account di servizio non utilizzati per le applicazioni cloudService Accounts that aren't used for cloud applications
- Gruppi che non devono essere usati in scenari cloud come quelli usati per concedere l'accesso alle risorse
- Utenti o contatti che sono identità esterne che devono essere rappresentati con la collaborazione B2B di Azure ADUsers or contacts that are external identities that are meant to be represented with Azure AD B2B Collaboration
- Account computer in cui i dipendenti non devono accedere alle applicazioni cloud, ad esempio dai server

> [!NOTE]
> Se una singola identità umana ha più account di cui è stato eseguito il provisioning da un account, ad esempio una migrazione, una fusione o un'acquisizione di dominio legacy, è consigliabile sincronizzare l'account utilizzato dall'utente solo giorno per giorno, ad esempio ciò che utilizzano per accedere al computer .

Idealmente, si desidera raggiungere un equilibrio tra la riduzione del numero di oggetti da sincronizzare e la complessità delle regole. In genere, una combinazione tra [il filtro](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) unità organizzativa/contenitore e un semplice mapping di attributi all'attributo cloudFiltered è una combinazione di filtro efficace.

> [!IMPORTANT]
> Se si utilizza il filtro di gruppo nell'ambiente di produzione, è necessario passare a un altro approccio di filtro.

#### <a name="sync-failover-or-disaster-recovery"></a>Failover di sincronizzazione o ripristino di emergenza

Azure AD Connect svolge un ruolo chiave nel processo di provisioning. Se il server di sincronizzazione non è in linea per qualsiasi motivo, le modifiche apportate all'ambiente locale non possono essere aggiornate nel cloud e possono causare problemi di accesso per gli utenti. Pertanto, è importante definire una strategia di failover che consenta agli amministratori di riprendere rapidamente la sincronizzazione dopo la disconnessione del server di sincronizzazione. Tali strategie possono rientrare nelle seguenti categorie:

- **Distribuire server Di Azure AD Connect in modalità di gestione temporanea:** consente a un amministratore di "promuovere" il server di gestione temporanea nell'ambiente di produzione tramite un semplice commutatore di configurazione.
- **Usare la virtualizzazione:** se la connessione di Azure AD viene distribuita in una macchina virtuale (VM), gli amministratori possono sfruttare lo stack di virtualizzazione per eseguire la migrazione in tempo reale o ridistribuire rapidamente la macchina virtuale e quindi riprendere la sincronizzazione.

Se l'organizzazione non dispone di una strategia di ripristino di emergenza e failover per la sincronizzazione, non esitate a distribuire Azure AD Connect in modalità di gestione temporanea. Analogamente, se si verifica una mancata corrispondenza tra la configurazione di produzione e la configurazione di gestione temporanea, è consigliabile riutilizzare la modalità di gestione temporanea di Azure AD Connect di base in modo che corrisponda alla configurazione di produzione, incluse le versioni e le configurazioni software.

![Schermata della configurazione della modalità di gestione temporanea di Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Mantenersi aggiornati

Microsoft aggiorna Azure AD Connect regolarmente. Rimani aggiornato per sfruttare i miglioramenti delle prestazioni, le correzioni di bug e le nuove funzionalità offerte da ogni nuova versione.

Se la versione di Azure AD Connect è più indietro di sei mesi, è necessario eseguire l'aggiornamento alla versione più recente.

#### <a name="source-anchor"></a>Ancoraggio di origine

L'utilizzo di **ms-DS-consistencyguid** come [ancoraggio](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) di origine consente una più semplice migrazione di oggetti tra foreste e domini, che è comune in consolidamento/pulizia dei domini di Active Directory, fusioni, acquisizioni e cessioni.

Se attualmente si utilizza **ObjectGuid** come ancoraggio di origine, è consigliabile passare all'utilizzo di **ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Regole personalizzate

Le regole personalizzate di Azure AD Connect consentono di controllare il flusso di attributi tra oggetti locali e oggetti cloud. Tuttavia, l'uso eccessivo o l'utilizzo improprio di regole personalizzate può introdurre i seguenti rischi:

- Risoluzione dei problemi di complessitàTroubleshooting complexity
- Riduzione delle prestazioni durante l'esecuzione di operazioni complesse tra oggetti
- Maggiore probabilità di divergenza di configurazione tra il server di produzione e il server di gestione temporanea
- Sovraccarico aggiuntivo durante l'aggiornamento di Azure AD Connect se le regole personalizzate vengono create con la precedenza maggiore di 100 (usata dalle regole predefinite)Additional overhead when upgrading Azure AD Connect if custom rules are created within the precedence greater than 100 (used by built-in rules)

Se si utilizzano regole eccessivamente complesse, è necessario esaminare i motivi della complessità e trovare opportunità di semplificazione. Analogamente, se sono state create regole personalizzate con valore di precedenza superiore a 100, è necessario correggere le regole in modo che non siano a rischio o in conflitto con il set predefinito.

Di seguito sono riportati alcuni esempi di utilizzo improprio delle regole personalizzate:

- **Compensare i dati sporchi nella directory:** in questo caso, si consiglia di lavorare con i proprietari del team di Active Directory e pulire i dati nella directory come attività di correzione e regolare i processi per evitare la reintroduzione di dati errate.
- **Correzione una tantum di singoli utenti** - È comune trovare regole che outlier caso speciale, in genere a causa di un problema con un utente specifico.
- **"CloudFiltering" troppo complicato:** la riduzione del numero di oggetti è una procedura consigliata, esiste il rischio di creare e complicare troppo l'ambito di sincronizzazione utilizzando molte regole di sincronizzazione. Se esiste una logica complessa per includere/escludere oggetti oltre il filtro dell'unità organizzativa, è consigliabile gestire questa logica all'esterno della sincronizzazione ed etichettare gli oggetti con un semplice attributo "cloudFiltered" che può fluire con una semplice regola di sincronizzazione.

#### <a name="azure-ad-connect-configuration-documenter"></a>Documentatore di configurazione di Azure AD ConnectAzure AD Connect configuration documenter

[Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) è uno strumento che è possibile usare per generare la documentazione di un'installazione di Azure AD Connect per consentire una migliore comprensione della configurazione di sincronizzazione, rafforzare la sicurezza nel ottenere le cose giuste e sapere cosa è stato modificato quando è stata applicata una nuova build o una nuova configurazione di Azure AD Connect o sono state aggiunte o aggiornate regole di sincronizzazione personalizzate. Le funzionalità attuali dello strumento includono:

- Documentazione della configurazione completa della sincronizzazione di Azure AD Connect.Documentation of the complete configuration of Azure AD Connect sync.
- Documentazione di eventuali modifiche nella configurazione di due server di sincronizzazione di Azure AD Connect o le modifiche da una determinata linea di base di configurazione.
- Generazione di uno script di distribuzione di PowerShell per eseguire la migrazione delle differenze o delle personalizzazioni delle regole di sincronizzazione da un server a un altro.

## <a name="assignment-to-apps-and-resources"></a>Assegnazione ad app e risorse

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenze basate su gruppo per i servizi cloud MicrosoftGroup-based licensing for Microsoft cloud services

Azure Active Directory semplifica la gestione delle licenze tramite licenze basate su gruppo per i servizi cloud Microsoft.Azure Active Directory streamlines the management of licenses through [group-based licensing](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) for Microsoft cloud services. In questo modo, IAM fornisce l'infrastruttura del gruppo e la gestione delegata di tali gruppi ai team appropriati nelle organizzazioni. Esistono diversi modi per configurare l'appartenenza ai gruppi in Azure AD, tra cui:There are multiple ways to set up the membership of groups in Azure AD, including:

- **Sincronizzato da locale:** i gruppi possono provenire da directory locali, che potrebbero essere adatte per le organizzazioni che hanno stabilito processi di gestione dei gruppi che possono essere estesi per assegnare licenze in Office 365.

- **Basato su attributi/dinamico:** i gruppi possono essere creati nel cloud in base a un'espressione basata sugli attributi utente, ad esempio Reparto uguale a "vendite". Azure AD gestisce i membri del gruppo, mantenendolo coerente con l'espressione definita. L'utilizzo di questo tipo di gruppo per l'assegnazione delle licenze consente l'assegnazione di una licenza basata su attributi, che è ideale per le organizzazioni con una qualità dei dati elevata nella directory.

- **Proprietà delegata:** i gruppi possono essere creati nel cloud e possono essere designati come proprietari. In questo modo, è possibile consentire ai proprietari di aziende, ad esempio, Team di collaborazione o team di BI, di definire chi deve avere accesso.

Se attualmente si utilizza un processo manuale per assegnare licenze e componenti agli utenti, è consigliabile implementare licenze basate su gruppo. Se il processo corrente non monitora gli errori di licenza o gli elementi assegnati rispetto a quelli disponibili, è necessario definire miglioramenti al processo per risolvere gli errori di licenza e l'assegnazione delle licenze.

Un altro aspetto della gestione delle licenze è la definizione dei piani di servizio (componenti della licenza) che devono essere abilitati in base alle funzioni lavorative nell'organizzazione. La concessione dell'accesso a piani di servizio non necessari può consentire agli utenti di visualizzare gli strumenti nel portale di Office per i cui non sono stati addestrati o che non devono essere utilizzati. Può aumentare il volume dell'help desk, il provisioning non necessario e mettere a rischio la conformità e la governance, ad esempio quando si esegue il provisioning di OneDrive for Business a utenti che potrebbero non essere autorizzati a condividere contenuti.

Usare le linee guida seguenti per definire i piani di servizio per gli utenti:Use the following guidelines to define service plans to users:

- Gli amministratori devono definire "pacchetti" di piani di servizio da offrire agli utenti in base al loro ruolo, ad esempio, impiegati contro operaio da pavimento.
- Creare gruppi in base al cluster e assegnare la licenza con il piano di servizio.
- Facoltativamente, è possibile definire un attributo per contenere i pacchetti per gli utenti.

> [!IMPORTANT]
> La licenza basata su gruppo in Azure AD introduce il concetto di utenti in uno stato di errore di licenza. Se si notano errori di licenza, è necessario [identificare e risolvere](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) immediatamente eventuali problemi di assegnazione delle licenze.

![Uno screenshot di uno schermo del computer Descrizione generata automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gestione del ciclo di vita

Se attualmente si utilizza uno strumento, ad esempio [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) o un sistema di terze parti, che si basa su un'infrastruttura locale, è consigliabile eseguire l'offload dell'assegnazione dallo strumento esistente, implementare licenze basate su gruppo e definire una gestione del ciclo di vita di gruppo in base ai [gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups). Analogamente, se il processo esistente non tiene conto dei nuovi dipendenti o dipendenti che lasciano l'organizzazione, è necessario distribuire le licenze basate su gruppo in base a gruppi dinamici e definire un ciclo di vita dell'appartenenza al gruppo. Infine, se le licenze basate su gruppo vengono distribuite su gruppi locali privi di gestione del ciclo di vita, è consigliabile usare i gruppi cloud per abilitare funzionalità quali la proprietà delegata o l'appartenenza dinamica basata su attributi.

### <a name="assignment-of-apps-with-all-users-group"></a>Assegnazione di app con il gruppo "Tutti gli utenti"

È possibile che i proprietari delle risorse ritengano che il gruppo **Tutti gli utenti** contenga solo dipendenti **dell'organizzazione** quando possono effettivamente contenere sia I **dipendenti dell'organizzazione** che **gli ospiti**. Di conseguenza, è necessario prestare particolare attenzione quando si utilizza il gruppo **Tutti gli utenti** per l'assegnazione delle applicazioni e si concede l'accesso a risorse quali il contenuto di SharePoint o le applicazioni.

> [!IMPORTANT]
> Se il gruppo **Tutti gli utenti** è abilitato e utilizzato per i criteri di accesso condizionale, l'assegnazione di app o risorse, assicurarsi di proteggere il [gruppo](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) se non si desidera includere gli utenti guest. Inoltre, è consigliabile correggere le assegnazioni di licenza creando e assegnando a gruppi che contengono solo **dipendenti dell'organizzazione.** D'altra parte, se si rileva che il gruppo **Tutti gli utenti** è abilitato ma non utilizzato per concedere l'accesso alle risorse, assicurarsi che le linee guida operative dell'organizzazione utilizzino intenzionalmente tale gruppo (che include sia Enterprise **Employees** che **Guests**).

### <a name="automated-user-provisioning-to-apps"></a>Provisioning automatico degli utenti nelle app

[Il provisioning automatico degli](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) utenti nelle applicazioni è il modo migliore per creare un provisioning, un deprovisioning e un ciclo di vita coerenti delle identità su più sistemi.

Se il provisioning delle app viene attualmente eseguito in modo ad hoc o si usano elementi come file CSV, JIT o una soluzione locale che non riguarda la gestione del ciclo di vita, è [consigliabile implementare](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) il provisioning delle applicazioni con Azure AD per le applicazioni supportate e definire un modello coerente per le applicazioni non ancora supportate da Azure AD.

![Servizio di provisioning di Azure ADAzure AD provisioning service](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Linea di base del ciclo di sincronizzazione delta di Azure AD ConnectAzure AD Connect delta sync baseline

È importante comprendere il volume delle modifiche nell'organizzazione e assicurarsi che non richieda troppo tempo per avere un tempo di sincronizzazione prevedibile.

La frequenza [di sincronizzazione delta predefinita](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) è 30 minuti. Se la sincronizzazione delta richiede più di 30 minuti in modo coerente o sono presenti discrepanze significative tra le prestazioni di sincronizzazione delta di gestione temporanea e produzione, è necessario esaminare ed esaminare i fattori che [influenzano le prestazioni di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Lettura consigliata per la risoluzione dei problemi di Azure AD ConnectAzure AD Connect troubleshooting recommended reading

- [Preparare gli attributi di directory per la sincronizzazione con Office 365 utilizzando lo strumento IdFix - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Risoluzione degli errori durante la sincronizzazioneAzure AD Connect: Troubleshooting Errors during synchronization](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Riepilogo

Esistono cinque aspetti per un'infrastruttura di identità protetta. Questo elenco consente di individuare e intraprendere rapidamente le azioni necessarie per proteggere e gestire il ciclo di vita delle identità e i relativi diritti nell'organizzazione.

- Assegnare i proprietari alle attività chiave.
- Individuare e risolvere i problemi di sincronizzazione.
- Definire una strategia di failover per il ripristino di emergenza.
- Semplifica la gestione delle licenze e l'assegnazione delle app.
- Automatizza il provisioning degli utenti nelle app.

## <a name="next-steps"></a>Passaggi successivi

Introduzione alle azioni e ai controlli di [gestione dell'autenticazione](active-directory-ops-guide-auth.md).
