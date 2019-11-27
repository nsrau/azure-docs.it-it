---
title: Guida di riferimento alle operazioni di gestione delle identità e degli accessi Azure Active Directory
description: Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere le operazioni di gestione delle identità e degli accessi
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
ms.openlocfilehash: 36b3857f8827f8a33e5fc0981b22a49128f7c193
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535327"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guida di riferimento alle operazioni di gestione delle identità e degli accessi Azure Active Directory

Questa sezione della Guida di riferimento per le [operazioni Azure ad](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da prendere in considerazione per proteggere e gestire il ciclo di vita delle identità e le relative assegnazioni.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità, in quanto i prodotti e i servizi Microsoft evolvono nel tempo.

## <a name="key-operational-processes"></a>Principali processi operativi

### <a name="assign-owners-to-key-tasks"></a>Assegnare i proprietari alle attività principali

La gestione di Azure Active Directory richiede l'esecuzione continua delle attività e dei processi operativi principali che potrebbero non far parte di un progetto di implementazione. È ancora importante configurare queste attività per gestire l'ambiente. Le attività principali e i proprietari consigliati includono:

| attività | Proprietario |
| :- | :- |
| Definire il processo come creare le sottoscrizioni di Azure | Varia in base all'organizzazione |
| Decidere chi ottiene Enterprise Mobility + Security licenze | Team operativo IAM |
| Decidere chi ottiene le licenze di Office 365 | Team di produttività |
| Decidere chi ottiene altre licenze, ad esempio Dynamics, VSO | Proprietario dell'applicazione |
| Assegnare licenze | Team operativo IAM |
| Risoluzione dei problemi e correzione degli errori di assegnazione delle licenze | Team operativo IAM |
| Effettuare il provisioning delle identità nelle applicazioni Azure AD | Team operativo IAM |

Quando si esamina l'elenco, è possibile che sia necessario assegnare un proprietario per le attività che non dispongono di un proprietario o modificare la proprietà per le attività che non sono allineate alle raccomandazioni precedenti.

#### <a name="assigning-owners-recommended-reading"></a>Assegnazione dei proprietari consigliata per la lettura

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Sincronizzazione delle identità locale

### <a name="identify-and-resolve-synchronization-issues"></a>Identificare e risolvere i problemi di sincronizzazione

Microsoft consiglia di disporre di una linea di base e di informazioni sui problemi nell'ambiente locale che possono causare problemi di sincronizzazione nel cloud. Poiché gli strumenti automatici come [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [Azure ad Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) possono generare un volume elevato di falsi positivi, è consigliabile identificare gli errori di sincronizzazione che non sono stati indirizzati per più di 100 giorni, eliminando gli oggetti in errore. Gli errori di sincronizzazione non risolti a lungo termine possono generare interventi di supporto. Per la risoluzione degli errori [durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) viene fornita una panoramica dei diversi tipi di errori di sincronizzazione, alcuni dei possibili scenari che provocano tali errori e potenziali modi per correggere gli errori.

### <a name="azure-ad-connect-sync-configuration"></a>Configurazione della sincronizzazione Azure AD Connect

Per abilitare tutte le esperienze ibride, il comportamento di sicurezza basato su dispositivo e l'integrazione con Azure AD, è necessario sincronizzare gli account utente usati dai dipendenti per accedere ai propri desktop.

Se non si sincronizzano gli utenti della foresta che accedono a, è necessario modificare la sincronizzazione in modo che provenga dalla foresta corretta.

#### <a name="synchronization-scope-and-object-filtering"></a>Ambito di sincronizzazione e filtro oggetti

La rimozione di bucket noti di oggetti che non devono essere sincronizzati presenta i vantaggi operativi seguenti:

- Meno origini di errori di sincronizzazione
- Cicli di sincronizzazione più veloci
- Meno "Garbage" da portare avanti dall'ambiente locale, ad esempio, l'inquinamento dell'elenco di indirizzi globale per gli account di servizio locali che non sono rilevanti nel cloud

> [!NOTE]
> Se si importano molti oggetti che non vengono esportati nel cloud, è consigliabile filtrare in base a OU o attributi specifici.

Esempi di oggetti da escludere sono:

- Account del servizio non usati per le applicazioni cloud
- Gruppi che non sono destinati a essere usati in scenari cloud come quelli usati per concedere l'accesso alle risorse
- Utenti o contatti che sono identità esterne che devono essere rappresentati con Azure AD collaborazione B2B
- Account computer in cui i dipendenti non sono destinati ad accedere alle applicazioni cloud da, ad esempio, server

> [!NOTE]
> Se per una singola identità umana sono presenti più account di cui è stato effettuato il provisioning da elementi come la migrazione, la fusione o l'acquisizione di un dominio legacy, è necessario sincronizzare solo l'account utilizzato dall'utente per un giorno, ad esempio, che cosa utilizzano per accedere al proprio computer .

Idealmente, è opportuno raggiungere un equilibrio tra la riduzione del numero di oggetti da sincronizzare e la complessità delle regole. In genere, una combinazione tra [filtro](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) ou/contenitore e un semplice mapping di attributi all'attributo cloudFiltered è una combinazione di filtri efficace.

> [!IMPORTANT]
> Se si usa il filtro di gruppo in produzione, è consigliabile passare a un altro approccio di filtro.

#### <a name="sync-failover-or-disaster-recovery"></a>Failover di sincronizzazione o ripristino di emergenza

Azure AD Connect svolge un ruolo chiave nel processo di provisioning. Se il server di sincronizzazione passa alla modalità offline per qualsiasi motivo, non è possibile aggiornare le modifiche apportate al sito locale nel cloud e possono verificarsi problemi di accesso per gli utenti. È pertanto importante definire una strategia di failover che consenta agli amministratori di riprendere rapidamente la sincronizzazione dopo che il server di sincronizzazione passa alla modalità offline. Tali strategie possono rientrare nelle categorie seguenti:

- **Distribuisci Azure ad Connect Server in modalità di gestione temporanea** : consente a un amministratore di "alzare di livello" il server di staging alla produzione mediante una semplice opzione di configurazione.
- **Usare la virtualizzazione** : se il Azure ad Connect viene distribuito in una macchina virtuale (VM), gli amministratori possono sfruttare lo stack di virtualizzazione per eseguire la migrazione in tempo reale o ridistribuire rapidamente la macchina virtuale e quindi riprendere la sincronizzazione.

Se l'organizzazione non dispone di una strategia di ripristino di emergenza e di failover per la sincronizzazione, non è necessario distribuire Azure AD Connect in modalità di gestione temporanea. Allo stesso modo, in caso di mancata corrispondenza tra la configurazione di produzione e di gestione temporanea, è necessario ristabilire la linea di base Azure AD Connect modalità di gestione temporanea in modo che corrisponda alla configurazione di produzione, incluse le configurazioni e le versioni

![Screenshot della configurazione della modalità di gestione temporanea Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Mantenersi aggiornati

Microsoft Update Azure AD Connect regolarmente. Rimanere aggiornati per sfruttare i miglioramenti delle prestazioni, le correzioni di bug e le nuove funzionalità disponibili in ogni nuova versione.

Se la versione del Azure AD Connect è superiore a sei mesi dopo, è necessario eseguire l'aggiornamento alla versione più recente.

#### <a name="source-anchor"></a>Ancoraggio di origine

L'uso di **ms-DS-consistencyguid** come [ancoraggio di origine](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) consente una migrazione più semplice di oggetti tra foreste e domini, che è comune nel consolidamento/pulizia di un dominio di Active Directory, fusioni, acquisizioni e dismissioni.

Se attualmente si usa **objectGUID** come ancoraggio di origine, si consiglia di passare all'uso di **ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Regole personalizzate

Azure AD Connect regole personalizzate consentono di controllare il flusso di attributi tra oggetti locali e oggetti cloud. Tuttavia, il sovrautilizzo o l'utilizzo di regole personalizzate può presentare i rischi seguenti:

- Complessità della risoluzione dei problemi
- Riduzione delle prestazioni durante l'esecuzione di operazioni complesse tra oggetti
- Maggiore probabilità di divergenza della configurazione tra il server di produzione e il server di gestione temporanea
- Overhead aggiuntivo durante l'aggiornamento Azure AD Connect se le regole personalizzate vengono create entro la precedenza maggiore di 100 (usato dalle regole predefinite)

Se si utilizzano regole troppo complesse, è necessario esaminare i motivi della complessità e individuare le opportunità di semplificazione. Analogamente, se sono state create regole personalizzate con valore di precedenza superiore a 100, è necessario correggere le regole in modo che non siano a rischio o in conflitto con il set predefinito.

Di seguito sono riportati alcuni esempi di utilizzo delle regole personalizzate:

- **Compensare i dati dirty nella directory** . in questo caso, è consigliabile collaborare con i proprietari del team di ad e pulire i dati nella directory come attività di correzione e modificare i processi per evitare la reintroduzione dei dati non validi.
- **Correzione di singoli utenti** : è comune individuare le regole che causano outlier speciali, in genere a causa di un problema con un utente specifico.
- **"CloudFiltering" complesso** : Sebbene la riduzione del numero di oggetti sia una procedura consigliata, esiste il rischio di creare e complicare l'ambito di sincronizzazione usando numerose regole di sincronizzazione. Se è presente una logica complessa per includere/escludere oggetti oltre il filtro dell'unità organizzativa, è consigliabile gestire questa logica al di fuori della sincronizzazione ed etichettare gli oggetti con un semplice attributo "cloudFiltered" che può fluire con una semplice regola di sincronizzazione.

#### <a name="azure-ad-connect-configuration-documenter"></a>Documento di configurazione Azure AD Connect

Il [documento di Azure ad Connect Configuration](https://github.com/Microsoft/AADConnectConfigDocumenter) è uno strumento che è possibile usare per generare la documentazione di un'installazione di Azure ad Connect per comprendere meglio la configurazione della sincronizzazione, la possibilità di acquisire familiarità e sapere cosa è stato modificato quando è stata applicata una nuova compilazione o configurazione di regole di sincronizzazione personalizzate Azure ad Connect o aggiunte o aggiornate. Le funzionalità correnti dello strumento includono:

- Documentazione relativa alla configurazione completa di Azure AD Connect Sync.
- Documentazione di eventuali modifiche apportate alla configurazione di due server di sincronizzazione Azure AD Connect o modifiche da una linea di base di configurazione specifica.
- Generazione di uno script di distribuzione di PowerShell per migrare le differenze o le personalizzazioni delle regole di sincronizzazione da un server a un altro.

## <a name="assignment-to-apps-and-resources"></a>Assegnazione ad app e risorse

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenze basate sui gruppi per i servizi cloud Microsoft

Azure Active Directory semplifica la gestione delle licenze tramite le [licenze basate sui gruppi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) per i servizi cloud Microsoft. In questo modo, IAM fornisce l'infrastruttura del gruppo e la gestione delegata di tali gruppi ai team appropriati delle organizzazioni. Esistono diversi modi per configurare l'appartenenza dei gruppi in Azure AD, tra cui:

- La **sincronizzazione da gruppi locali** può provenire da directory locali, che possono essere una scelta ottimale per le organizzazioni che hanno stabilito processi di gestione dei gruppi che possono essere estesi per assegnare le licenze in Office 365.

- I gruppi **basati su attributi o dinamici** possono essere creati nel cloud in base a un'espressione basata sugli attributi utente, ad esempio il reparto è uguale a "Sales". Azure AD gestisce i membri del gruppo, mantenendo la coerenza con l'espressione definita. L'uso di questo tipo di gruppo per l'assegnazione delle licenze consente di assegnare una licenza basata su attributi, che rappresenta una scelta ottimale per le organizzazioni che hanno una qualità dei dati elevata nella propria directory.

- **Proprietà delegata** : i gruppi possono essere creati nel cloud e possono essere designati come proprietari. In questo modo, è possibile responsabilizzare i proprietari aziendali, ad esempio il team di collaborazione o il team BI, per definire gli utenti che devono avere accesso.

Se attualmente si usa un processo manuale per assegnare licenze e componenti agli utenti, si consiglia di implementare le licenze basate sui gruppi. Se il processo corrente non monitora gli errori di gestione delle licenze o ciò che viene assegnato rispetto a quello disponibile, è necessario definire miglioramenti al processo per risolvere gli errori di gestione delle licenze e monitorare l'assegnazione delle licenze.

Un altro aspetto della gestione delle licenze è la definizione dei piani di servizio (componenti della licenza) che devono essere abilitati in base alle funzioni di processo nell'organizzazione. Concedendo l'accesso ai piani di servizio che non sono necessari, gli utenti possono visualizzare gli strumenti del portale di Office per i quali non sono stati sottoposti a training o che non devono usare. Può guidare un volume help desk aggiuntivo, il provisioning superfluo e applicare la conformità e la governance a rischio, ad esempio quando si esegue il provisioning di OneDrive for business a singoli utenti che non possono condividere il contenuto.

Usare le linee guida seguenti per definire i piani di servizio agli utenti:

- Gli amministratori devono definire "bundle" dei piani di servizio da offrire agli utenti in base al proprio ruolo, ad esempio, il lavoro di un collante o un lavoro di piano.
- Creare gruppi in base al cluster e assegnare la licenza con il piano di servizio.
- Facoltativamente, è possibile definire un attributo per contenere i pacchetti per gli utenti.

> [!IMPORTANT]
> Le licenze basate sui gruppi in Azure AD introducono il concetto di utenti in stato di errore di licenza. Se si notano errori di licenza, è necessario [identificare e risolvere](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) immediatamente eventuali problemi di assegnazione delle licenze.

![Schermata di una descrizione della schermata del computer generata automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gestione del ciclo di vita

Se attualmente si usa uno strumento, ad esempio [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) o un sistema di terze parti, che si basa su un'infrastruttura locale, è consigliabile eseguire l'offload dell'assegnazione dallo strumento esistente, implementare le licenze basate sui gruppi e definire una gestione del ciclo di vita del gruppo in base ai [gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups). Analogamente, se il processo esistente non tiene conto dei nuovi dipendenti o dipendenti che lasciano l'organizzazione, è consigliabile distribuire le licenze basate sui gruppi in base ai gruppi dinamici e definire il ciclo di vita dell'appartenenza a un gruppo. Infine, se le licenze basate sui gruppi vengono distribuite in gruppi locali che non dispongono di gestione del ciclo di vita, è consigliabile usare i gruppi cloud per abilitare funzionalità come la proprietà delegata o l'appartenenza dinamica basata su attributi.

### <a name="assignment-of-apps-with-all-users-group"></a>Assegnazione delle app con il gruppo "tutti gli utenti"

I proprietari delle risorse possono ritenere che il gruppo **tutti gli utenti** contenga solo i **dipendenti aziendali** quando possono effettivamente contenere sia i **dipendenti aziendali** che i **Guest**. Di conseguenza, è necessario prestare particolare attenzione quando si utilizza il gruppo **tutti gli utenti** per l'assegnazione dell'applicazione e si concede l'accesso a risorse quali il contenuto o le applicazioni di SharePoint.

> [!IMPORTANT]
> Se il gruppo **tutti gli utenti** è abilitato e usato per i criteri di accesso condizionale, l'assegnazione di app o risorse, assicurarsi di [proteggere il gruppo](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups#hardening-the-all-users-dynamic-group) se non si vuole includere utenti guest. Inoltre, è necessario correggere le assegnazioni di licenze creando e assegnando a gruppi che contengono solo **dipendenti aziendali** . D'altra parte, se si rileva che il gruppo **tutti gli utenti** è abilitato ma non usato per concedere l'accesso alle risorse, assicurarsi che le linee guida operative dell'organizzazione usino intenzionalmente tale gruppo, che include i **dipendenti aziendali** e i **Guest**.

### <a name="automated-user-provisioning-to-apps"></a>Provisioning utenti automatizzato per le app

Il [provisioning utenti automatizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) per le applicazioni è il modo migliore per creare un provisioning coerente, il deprovisioning e il ciclo di vita delle identità in più sistemi.

Se si effettua il provisioning di app in modo ad hoc o si usano elementi come file CSV, JIT o una soluzione locale che non risolve la gestione del ciclo di vita, è consigliabile [implementare il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) delle applicazioni con Azure ad per le applicazioni supportate e definire un modello coerente per le applicazioni che non sono ancora supportate da Azure ad.

![Servizio di provisioning Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect baseline del ciclo di sincronizzazione Delta

È importante comprendere il volume delle modifiche all'interno dell'organizzazione e assicurarsi che non si stia impiegando troppo tempo per avere un tempo di sincronizzazione prevedibile.

La frequenza di [sincronizzazione delta predefinita](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) è 30 minuti. Se la sincronizzazione Delta impiega più di 30 minuti in modo coerente o si verificano discrepanze significative tra le prestazioni di sincronizzazione Delta di gestione temporanea e produzione, è consigliabile analizzare e verificare i [fattori che influenzano le prestazioni di Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Lettura consigliata per la risoluzione dei problemi Azure AD Connect

- [Preparare gli attributi di directory per la sincronizzazione con Office 365 usando lo strumento IdFix-Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: risoluzione degli errori durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>summary

Esistono cinque aspetti di un'infrastruttura di identità sicura. Questo elenco ti aiuterà a trovare e intraprendere rapidamente le azioni necessarie per proteggere e gestire il ciclo di vita delle identità e dei rispettivi diritti nell'organizzazione.

- Assegnare i proprietari alle attività principali.
- Individuare e risolvere i problemi di sincronizzazione.
- Definire una strategia di failover per il ripristino di emergenza.
- Semplifica la gestione delle licenze e l'assegnazione delle app.
- Automatizzare il provisioning degli utenti nelle app.

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare i [controlli e le azioni di gestione dell'autenticazione](active-directory-ops-guide-auth.md).
