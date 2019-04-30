---
title: Progetto di analisi di Integrità di Azure
description: Indicazioni per la distribuzione di un progetto di analisi della conformità HIPAA/HITRUST per i dati del settore sanitario
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: 70721b8bfbecaf554a9502b9ec3417fc8e561b3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609725"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Progetto per la sicurezza e la conformità di Azure: HIPAA/HITRUST per dati del settore sanitario e intelligenza artificiale

## <a name="overview"></a>Panoramica

**Progetto di sicurezza e conformità di Azure: HIPAA/HITRUST per dati del settore sanitario e intelligenza artificiale offre una distribuzione chiavi in mano di una soluzione PaaS e IaaS di Azure per illustrare come inserire, archiviare, analizzare, usare, identificare e distribuire in modo sicuro soluzioni con dati del settore sanitario rispettando i requisiti di conformità del settore. Il progetto consente di accelerare l'adozione e l'utilizzo del cloud da parte dei clienti con dati regolamentati.**

Il progetto per la sicurezza e la conformità di Azure: HIPAA/HITRUST per dati del settore sanitario e intelligenza artificiale fornisce strumenti e indicazioni per distribuire un ambiente PaaS (piattaforma distribuita come servizio) sicuro, conforme a HIPAA (Health Insurance Portability e Accountability Act) e HITRUST (Health Information Trust Alliance) per inserire, archiviare, analizzare e usare cartelle cliniche personali e non personali in un ambiente cloud multilivello sicuro, distribuito come soluzione end-to-end. 

La soluzione IaaS illustrerà come eseguire la migrazione in Azure di una soluzione locale basata su SQL e come implementare una workstation con accesso con privilegi per gestire in modo sicuro le soluzioni e i servizi basati sul cloud. Il database di SQL Server IaaS aggiunge l'importazione di dati di sperimentazione potenziali in una macchina virtuale IaaS basata su SQL e tale macchina usa l'accesso autenticato dell'identità del servizio gestita per interagire con un servizio PaaS di SQL Azure. Entrambi presentano un'architettura di riferimento comune e sono stati progettati per semplificare l'adozione di Microsoft Azure. Questa architettura illustra una soluzione in grado di soddisfare le esigenze delle organizzazioni alla ricerca di un approccio basato sul cloud per ridurre il carico di lavoro e il costo della distribuzione.

![](images/components.png)

La soluzione è progettata per usare un set di dati di esempio formattato usando FHIR (Healthcare Interoperability Resources), uno standard globale per lo scambio elettronico di informazioni sanitarie, e per archiviare i dati in modo sicuro. I clienti possono quindi usare Azure Machine Learning Studio per sfruttare i potenti strumenti di business intelligence e le funzionalità di analisi per esaminare le stime eseguite sui dati di esempio. Per illustrare il tipo di esperimento che Azure Machine Learning Studio può aiutare a svolgere, il progetto include un set di dati di esempio, script e strumenti per stimare la durata della degenza di un paziente in ospedale. 

Questo progetto è pensato come base modulare per i clienti, che possono apportare modifiche in base ai requisiti specifici, sviluppando nuovi esperimenti di Azure Machine Learning per soddisfare le esigenze di casi d'uso clinici e operativi. Il progetto è pensato in modo da garantire sicurezza e conformità dopo la distribuzione. Tuttavia, i clienti sono responsabili della corretta configurazione dei ruoli e dell'implementazione delle modifiche. Tenere presente quanto segue:

-   Questo progetto offre una soluzione iniziale per aiutare i clienti a usare Microsoft Azure in un ambiente che richiede la conformità a HITRUST e HIPAA.

-   Anche se il progetto è stato preparato per essere allineato con HIPAA e HITRUST tramite il framework di sicurezza comune (CSF, Common Security Framework), non deve essere considerato conforme fino a quando non viene certificato da un revisore esterno in base ai requisiti di certificazione HIPAA e HITRUST.

-   I clienti hanno la responsabilità di svolgere revisioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura di base.

## <a name="deploying-the-automation"></a>Distribuzione dell'automazione

- Per distribuire la soluzione, seguire le istruzioni riportate nelle [linee guida per la distribuzione](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md). 

- Per una rapida panoramica del funzionamento di questa soluzione, guardare questo [video](https://aka.ms/healthblueprintvideo), che ne spiega e descrive la distribuzione.

- Le domande frequenti sono illustrate nell'articolo di [domande frequenti](https://aka.ms/healthblueprintfaq).

-   **Diagramma dell'architettura.** Il diagramma illustra l'architettura di riferimento usata per il progetto e lo scenario del caso d'uso di esempio.

-   [Estensione IaaS](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) questa soluzione illustra come eseguire la migrazione di una soluzione basata su SQL in locale in Azure e implementare una Workstation amministrativa con privilegi per gestire in modo sicuro le soluzioni e servizi basati su cloud. 

## <a name="solution-components"></a>Componenti della soluzione


L'architettura di base è costituita dai componenti seguenti:

-   **[Modello di rischio](https://aka.ms/healththreatmodel)** Viene fornito un modello di rischio completo in formato tm7 da usare con [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168), che illustra i componenti della soluzione, il flusso di dati tra di essi e i limiti di trust. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema durante lo sviluppo di componenti di Machine Learning Studio o quando vengono apportate altre modifiche.

-   **[Matrice di implementazione del cliente](https://aka.ms/healthcrmblueprint)** Una cartella di lavoro di Microsoft Excel che elenca i requisiti di HITRUST pertinenti e spiega in che modo Microsoft e il cliente sono responsabili di soddisfare ognuno di questi requisiti.

-   **[Revisione dei dati sanitari.](https://aka.ms/healthreviewpaper)** La soluzione è stata rivista da Coalfire Systems, Inc. La revisione della conformità (HIPAA e HITRUST) per il settore sanitario e le indicazioni per l'implementazione presentano la revisione della soluzione da parte di un revisore e considerazioni per la trasformazione del piano in una distribuzione pronta per la produzione.

## <a name="architectural-diagram"></a>Diagramma dell'architettura


![](images/ra2.png)

## <a name="roles"></a>Ruoli


Il progetto definisce due ruoli per gli utenti amministratori (operatori) e tre ruoli per gli utenti addetti alla gestione ospedaliera e alla cura dei pazienti. Viene definito un sesto ruolo di revisore incaricato della valutazione della conformità a HIPAA e ad altri regolamenti. Il controllo degli accessi in base al ruolo di Azure consente un'accurata gestione degli accessi per ogni utente della soluzione tramite ruoli predefiniti e personalizzati. Vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) e [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) per informazioni dettagliate su controllo degli accessi in base al ruolo, ruoli e autorizzazioni.

### <a name="site-administrator"></a>Amministratore del sito


L'amministratore del sito è responsabile della sottoscrizione di Azure del cliente. Controlla la distribuzione globale, ma non ha accesso ai record dei pazienti.

-   Assegnazioni di ruolo predefinite: [Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Assegnazioni di ruolo personalizzate: N/D

-   Ambito: Sottoscrizione

### <a name="database-analyst"></a>Analista di database

L'analista di database amministra il database e l'istanza di SQL Server.
Non ha accesso ai record dei pazienti.

-   Assegnazioni di ruolo predefinite: [Collaboratore Database SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [Collaboratore SQL Server](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Assegnazioni di ruolo personalizzate: N/D

-   Ambito: ResourceGroup

### <a name="data-scientist"></a>Data scientist


Il data scientist gestisce Azure Machine Learning Studio. Può importare, esportare e gestire i dati ed eseguire report. Il data scientist ha accesso ai dati dei pazienti, ma non ha privilegi amministrativi.

-   Assegnazioni di ruolo predefinite: [Collaboratore account di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Assegnazioni di ruolo personalizzate: N/D

-   Ambito: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Responsabile dei servizi informatici sanitari (CMIO, Chief Medical Information Officer)


Il CMIO riveste un ruolo a metà strada tra informatica/tecnologia e professionisti del settore sanitario in un'organizzazione sanitaria. Questo ruolo richiede in genere l'uso delle analisi per determinare se le risorse sono allocate in modo appropriato nell'organizzazione.

-   Assegnazioni di ruolo predefinite: Nessuna

### <a name="care-line-manager"></a>Responsabile sanitario


Il responsabile sanitario è coinvolto direttamente nella cura dei pazienti.
Questo ruolo richiede il monitoraggio dello stato dei singoli pazienti, oltre alla verifica della disponibilità del personale per rispondere ai requisiti di assistenza specifici dei pazienti. Il responsabile sanitario si occupa dell'aggiunta e dell'aggiornamento dei record dei pazienti.

-   Assegnazioni di ruolo predefinite: Nessuna

-   Assegnazioni di ruolo personalizzate: ha il privilegio necessario per eseguire HealthcareDemo.ps1 sia per il ricovero che per le dimissioni dei pazienti.

-   Ambito: ResourceGroup

### <a name="auditor"></a>Revisore


Il revisore valuta la soluzione per verificarne la conformità. Non ha accesso diretto alla rete.

-   Assegnazioni di ruolo predefinite: [Lettore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Assegnazioni di ruolo personalizzate: N/D

-   Ambito: Sottoscrizione

## <a name="example-use-case"></a>Esempio di caso d'uso


Il caso d'uso di esempio incluso in questo progetto illustra come usare il progetto per applicare l'apprendimento automatico ed eseguire analisi sui dati sanitari nel cloud. Contosoclinic è un piccolo ospedale che si trova negli Stati Uniti. Gli amministratori di rete dell'ospedale vogliono usare Azure Machine Learning Studio per stimare meglio la durata della degenza del paziente al momento del ricovero, allo scopo di aumentare l'efficienza operativa del carico di lavoro e migliorare la qualità dell'assistenza offerta.

### <a name="predicting-length-of-stay"></a>Previsione della durata della degenza


Lo scenario del caso d'uso di esempio usa Azure Machine Learning Studio per stimare la durata della degenza di un paziente appena ricoverato confrontando i dati medici del paziente al momento del ricovero e i dati cronologici aggregati dei pazienti precedenti.
Il progetto include un vasto set di cartelle cliniche anonime che illustrano le capacità di training e stima della soluzione. In una distribuzione di produzione i clienti useranno i propri record per il training della soluzione, per ottenere stime più accurate che riflettono le caratteristiche dell'ambiente, delle strutture e dei pazienti specifici.

### <a name="users-and-roles"></a>Utenti e ruoli


**Amministratore del sito - Alex**

*Indirizzo di posta elettronica: Alex\_SiteAdmin*

Il lavoro di Alex consiste nel valutare le tecnologie che possono ridurre il carico di lavoro di gestione di una rete locale e i costi per la gestione. Alex sta valutando Azure da un po' di tempo, ma ha difficoltà a configurare i servizi necessari per soddisfare i requisiti di conformità HiTrust per l'archiviazione dei dati dei pazienti nel cloud. Alex ha scelto l'intelligenza artificiale di Integrità di Azure per distribuire una soluzione per il settore sanitario predisposta per la conformità, in grado di soddisfare i requisiti dei clienti per HiTrust.

**Data scientist - Debra**

*Indirizzo di posta elettronica: Debra\_DataScientist*

Debra è responsabile dell'uso e della creazione di modelli che analizzano le cartelle cliniche per fornire informazioni approfondite sulla cura dei pazienti. Debra usa SQL e il linguaggio di programmazione statistica R per creare i modelli.

**Analista di database - Danny**

*Indirizzo di posta elettronica: Danny\_DBAnalyst*

Danny è il contatto principale per tutto ciò che riguarda la soluzione Microsoft SQL Server che archivia tutti i dati dei pazienti per Contosoclinic. Danny è un amministratore esperto di SQL Server che ha di recente acquisito familiarità con il database SQL di Azure.

**Responsabile dei servizi informatici sanitari (CMIO, Chief Medical Information Officer) - Caroline**

Caroline collabora con Chris, responsabile sanitario, e Debra, data scientist, per determinare i fattori che influiscono sulla durata della degenza dei pazienti.
Caroline usa le stime dalla soluzione di calcolo della durata della degenza per determinare se le risorse vengono allocate in modo appropriato nella rete ospedaliera. Usa, ad esempio, il dashboard fornito in questa soluzione.

**Responsabile sanitario - Chris**

*Indirizzo di posta elettronica: Chris\_CareLineManager*

In quanto diretto responsabile della gestione del ricovero e delle dimissioni dei pazienti presso Contosoclinic, Chris usa le stime generate dalla soluzione di calcolo della durata della degenza per garantire la disponibilità di personale adeguato per fornire assistenza ai pazienti durante la degenza.

**Revisore - Han**

*Indirizzo di posta elettronica: Han\_Auditor*

Han è un revisore certificato con esperienza nel controllo della conformità a ISO, SOC e HiTrust. È stato assunto per la revisione della rete di Contosoclinc. Han può esaminare la matrice delle responsabilità del cliente fornita con la soluzione per assicurarsi che il progetto e la soluzione di calcolo della durata della degenza possano essere usati per archiviare, elaborare e visualizzare i dati personali sensibili.


## <a name="design-configuration"></a>Configurazione del progetto


Questa sezione illustra nel dettaglio le configurazioni predefinite e le misure di sicurezza integrate nel progetto per:

- **INSERIMENTO** di origini di dati non elaborati, inclusa l'origine dati FHIR
- **ARCHIVIAZIONE** di informazioni riservate
- **ANALISI** e stima dei risultati
- **INTERAZIONE** con i risultati e le stime
- **GESTIONE DELLE IDENTITÀ** della soluzione
- **FUNZIONALITÀ DI SICUREZZA**


## <a name="identity"></a>IDENTITÀ 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory e controllo degli accessi in base al ruolo


**Autenticazione:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft\' multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.



-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti e intraprende le azioni appropriate per risolverli.

-   Il [controllo degli accessi in base al ruolo di Azure](/azure/role-based-access-control/role-assignments-portal) consente un'accurata gestione degli accessi per Azure. L'accesso a una sottoscrizione è limitato all'amministratore della sottoscrizione e l'accesso ad Azure Key Vault è limitato all'amministratore del sito. Sono richieste password complesse (minimo 12 caratteri con almeno una lettera maiuscola/minuscola, un numero un carattere speciale).

-   L'autenticazione a più fattori è supportata quando l'opzione -enableMFA è abilitata durante la distribuzione.

-   Le password scadono dopo 60 giorni quando l'opzione -enableADDomainPasswordPolicy è abilitata durante la distribuzione.

**Ruoli:**

-   La soluzione usa i [ruoli predefiniti](/azure/role-based-access-control/built-in-roles) per gestire l'accesso alle risorse.

-   A tutti gli utenti vengono assegnati ruoli predefiniti specifici per impostazione predefinita.

### <a name="azure-key-vault"></a>Azure Key Vault

-   I dati archiviati in Key Vault includono:

    -   Chiave di Application Insights
    -   Chiave di accesso alle risorse di archiviazione dei dati dei pazienti
    -   Stringa di connessione per i pazienti
    -   Nome della tabella dati dei pazienti
    -   Endpoint servizio Web Azure ML
    -   Chiave API del servizio Azure ML

-   I criteri di accesso avanzati vengono configurati in base alle necessità
-   I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti
-   Tutti i segreti e le chiavi in Key Vault hanno date di scadenza
-   Tutte le chiavi in Key Vault sono protette dal modulo di protezione hardware \[Tipo di chiave = chiave RSA a 2048 bit protetta dal modulo di protezione hardware\]
-   A tutti gli utenti e le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo
-   Le applicazioni non condividono un'istanza di Key Vault a meno che non si considerino attendibili reciprocamente e non debbano accedere agli stessi segreti in fase di esecuzione
-   I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
-   Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie

## <a name="ingest"></a>INSERIMENTO 

### <a name="azure-functions"></a>Funzioni di Azure
La soluzione è stata progettata per usare [Funzioni di Azure](/azure/azure-functions/) per elaborare i dati di esempio sulla durata della degenza usati nella demo di analisi. Sono state create tre funzionalità nelle funzioni.

**1. Importazione in blocco delle informazioni sanitarie protette dei clienti**

Quando si usa lo script di demo. .\\HealthcareDemo.ps1 con l'opzione **BulkPatientAdmission** come illustrato nella sezione relativa a **distribuzione ed esecuzione della demo**, vengono eseguite le pipeline di elaborazione seguenti:
1. **Archiviazione BLOB di Azure** - File CVS di esempio dei dati dei pazienti caricato per l'archiviazione
2. **Griglia di eventi** - L'evento pubblica i dati nella funzione di Azure (importazione in blocco - evento BLOB)
3. **Funzione di Azure** - Esegue l'elaborazione e archivia i dati nel servizio di archiviazione SQL usando la funzione sicura - event(type;blob_url)
4. **Database SQL** - L'archivio di database per i dati dei pazienti, in cui vengono usati tag per la classificazione e il processo ML viene avviato per eseguire l'esperimento di training

![](images/dataflow.png)

La funzione di Azure è stata inoltre progettata per leggere e proteggere i dati sensibili designati nel set di dati di esempio usando i tag seguenti:
- dataProfile => "ePHI"
- owner => \<UPN amministratore sito\>
- environment => "Pilot"
- department => "Global Ecosystem". I tag sono stati applicati al set di dati di esempio in cui i nomi dei pazienti sono identificati come testo non crittografato.

**2. Ricovero di nuovi pazienti**

Quando si usa lo script di demo. .\\HealthcareDemo.ps1 con l'opzione **BulkPatientAdmission** come illustrato nella sezione relativa a **distribuzione ed esecuzione della demo**, vengono eseguite le pipeline di elaborazione seguenti: ![](images/securetransact.png)
**1. Funzione di Azure** attivata e la funzione richiede un [token di connessione](/rest/api/) da Azure Active Directory.

**2. Key Vault** richiesto per un segreto associato al token richiesto.

**3. I ruoli Azure** convaliderà la richiesta e autorizzano la richiesta di accesso a Key Vault.

**4. Key Vault** restituisce il segreto, in questo caso la stringa di connessione al database SQL.

**5. La funzione di Azure** usa la stringa di connessione per connettersi in modo sicuro al database SQL e continua l'elaborazione per archiviare i dati elettronici delle informazioni sanitarie protette dei clienti (ePHI).

Per l'archiviazione dei dati, è stato implementato uno schema API comune basato sullo standard FHIR (Fast Healthcare Interoperability Resources). Alla funzione sono stati forniti gli elementi di scambio FHIR seguenti:

-   [Schema dei pazienti](https://www.hl7.org/fhir/patient.html) che riguarda le informazioni personali di un paziente.

-   [Schema delle osservazioni](https://www.hl7.org/fhir/observation.html) che riguarda l'elemento centrale in ambito sanitario, usato per supportare le diagnosi, monitorare i progressi, determinare le linee di base e i modelli e anche acquisire le caratteristiche demografiche. 

-   [Schema degli incontri](https://www.hl7.org/fhir/encounter.html) che riguarda i tipi di incontri, ad esempio in ambulatorio, al pronto soccorso, in visite a domicilio, durante la degenza e incontri virtuali.

-   [Schema delle condizioni](https://www.hl7.org/fhir/condition.html) che riguarda le informazioni dettagliate su una condizione, un problema, una diagnosi o un altro tipo di evento, situazione, problematica o concetto clinico con un determinato livello di interesse.  



### <a name="event-grid"></a>Griglia di eventi


La soluzione supporta Griglia di eventi di Azure, un singolo servizio per la gestione del routing di tutti gli eventi da qualsiasi origine a qualunque destinazione, che fornisce:

-   [Sicurezza e autenticazione](/azure/event-grid/security-authentication)

-   [Controllo degli accessi in base al ruolo](/azure/event-grid/security-authentication#management-access-control) per diverse attività di gestione, come l'elenco delle sottoscrizioni di eventi, la creazione di nuove sottoscrizioni e la generazione di chiavi

-   Controllo

## <a name="store"></a>ARCHIVIAZIONE 

### <a name="sql-database-and-server"></a>Server e database SQL 


-   [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) offre crittografia e decrittografia in tempo reale dei dati archiviati nel database SQL di Azure, usando una chiave archiviata in Azure Key Vault.

-   [Valutazione della vulnerabilità di SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è uno strumento semplice da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database.

-   [Rilevamento delle minacce nel database SQL](/azure/sql-database/sql-database-threat-detection) abilitato.

-   [Controllo del database SQL](/azure/sql-database/sql-database-auditing) abilitato.

-   [Registrazione diagnostica e metriche del database SQL](/azure/sql-database/sql-database-metrics-diag-logging) abilitate.

-   [Regole firewall a livello di server e di database](/azure/sql-database/sql-database-firewall-configure) più restrittive.

-   [Colonne Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) usate per proteggere nome e cognome dei pazienti.
    Anche la crittografia delle colonne del database usa Azure Active Directory (AD) per l'autenticazione dell'applicazione nel database SQL di Azure.

-   L'accesso al database SQL e a SQL Server è configurato in base al principio dei privilegi minimi.

-   L'accesso attraverso il firewall SQL è consentito solo agli indirizzi IP necessari.

### <a name="storage-accounts"></a>Account di archiviazione


-   [I dati in transito vengono trasferiti usando solo TLS/SSL](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   L'accesso anonimo non è consentito per i contenitori.

-   Le regole di avviso sono configurate per il monitoraggio dell'attività anonima.

-   Per l'accesso alle risorse dell'account di archiviazione è necessario il protocollo HTTPS.

-   I dati delle richieste di autenticazione vengono registrati e monitorati.

-   I dati nell'archiviazione BLOB vengono crittografati quando sono inattivi.

## <a name="analyze"></a>ANALISI

### <a name="machine-learning"></a>Machine Learning


- [La registrazione è abilitata](/azure/machine-learning/studio/web-services-logging) per i servizi Web di Machine Learning Studio.
- L'uso di [Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) richiede lo sviluppo di esperimenti, per fornire la capacità di stima in un set di soluzioni.

## <a name="security"></a>SICUREZZA

### <a name="azure-security-center"></a>Centro sicurezza di Azure
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione. 

- [Azure Advisor](/azure/advisor/advisor-overview) è un consulente cloud personalizzato che facilita l'applicazione delle procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Questo servizio rileva le anomalie nelle prestazioni. e include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

### <a name="azure-alerts"></a>Avvisi di Azure
- [Gli avvisi](/azure/azure-monitor/platform/alerts-metric) offrono un metodo per monitorare i servizi di Azure e consentono di configurare le condizioni sui dati. Gli avvisi forniscono anche le notifiche quando c'è una corrispondenza tra una condizione di avviso e i dati di monitoraggio.

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
[Log di monitoraggio di Azure](/azure/operations-management-suite/operations-management-suite-overview) è una raccolta di servizi di gestione.

-   L'area di lavoro è abilitata per il Centro sicurezza

-   L'area di lavoro è abilitata per il monitoraggio del carico di lavoro

-   Il monitoraggio del carico di lavoro è abilitato per:

    -   Identità e accesso

    -   Security and Audit

    -   Analisi del database SQL di Azure

    -   Soluzione di [analisi delle app Web di Azure](/azure/log-analytics/log-analytics-azure-web-apps-analytics)

    -   Analisi dell'insieme di credenziali delle chiavi

    -   Change Tracking

-   [Connettore di Application Insights (anteprima)](/azure/log-analytics/log-analytics-app-insights-connector) abilitato

-   [Analisi dei log attività](/azure/log-analytics/log-analytics-activity) abilitata
