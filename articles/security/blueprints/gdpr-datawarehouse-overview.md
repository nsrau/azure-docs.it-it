---
title: Progetto per la sicurezza e la conformità di Azure - Data warehouse per GDPR
description: Progetto per la sicurezza e la conformità di Azure - Data warehouse per GDPR
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161864"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Progetto per la sicurezza e la conformità di Azure: data warehouse per GDPR

## <a name="overview"></a>Panoramica
Il regolamento GDPR (General Data Protection Regulation) contiene molti requisiti relativi alla raccolta, all'archiviazione e all'uso delle informazioni personali, ad esempio il modo in cui le organizzazioni identificano e proteggono i dati personali, rispettano i requisiti a livello di trasparenza, rilevano e segnalano violazioni ai dati personali ed eseguono la formazione del personale addetto alla privacy e di altri dipendenti. Il GDPR offre ai singoli utenti maggiore controllo sui dati personali e impone molti obblighi nuovi alle organizzazioni che raccolgono, gestiscono o analizzano i dati personali. Il GDPR impone nuove regole per le organizzazioni che offrono beni e servizi a persone nell'Unione europea o che raccolgono e analizzano dati collegati a persone residenti nell'Unione europea. Il GDPR si applica indipendentemente da dove si trova un'organizzazione.

Microsoft ha progettato Azure con misure di sicurezza e criteri di privacy leader di settore per la salvaguardia dei dati sul cloud, incluse le categorie dei dati personali identificate dal GDPR. Le [condizioni contrattuali](http://aka.ms/Online-Services-Terms) Microsoft impongono a Microsoft il rispetto dei requisiti dei responsabili.

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per la distribuzione di un'architettura di data warehouse in Azure utile per supportare i requisiti del GDPR. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità e costituisce un punto di partenza per i clienti che vogliono creare e configurare soluzioni di data warehouse personalizzate in Azure. I clienti possono usare questa architettura di riferimento e seguire il [processo in quattro passaggi](https://aka.ms/gdprebook) di Microsoft per ottenere la conformità con GDPR:
1. Individuazione: identificare i tipi di dati personali esistenti e la rispettiva posizione.
2. Gestione: regolamentare la modalità d'uso e di accesso dei dati personali.
3. Protezione: stabilire controlli di sicurezza per evitare, rilevare e rispondere alle vulnerabilità e alle violazioni di dati.
4. Segnalazione: mantenere la documentazione necessaria e gestire le richieste di dati e le notifiche relative alle violazioni.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. Tenere presente quanto segue:
- L'architettura fornisce una baseline per aiutare i clienti a distribuire carichi di lavoro in Azure in modo da assicurare la conformità con il GDPR.
- I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma dell'architettura e componenti
Questa soluzione fornisce un'architettura di riferimento che implementa un data warehouse cloud a prestazioni e sicurezza elevate. L'architettura include due livelli di dati separati: un livello in cui i dati vengono importati, archiviati e sottoposti a staging in un ambiente SQL con cluster e un altro per Azure SQL Data Warehouse, in cui i dati vengono caricati tramite uno strumento ETL (ad esempio query T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) per l'elaborazione. Dopo l'archiviazione dei dati in Azure SQL Data Warehouse, è possibile eseguire le analisi su larga scala.

Azure offre svariati servizi di creazione di report e di analisi per i clienti. Questa soluzione include SQL Server Reporting Services (SSRS) per la creazione rapida di report da Azure SQL Data Warehouse. Tutto il traffico SQL viene crittografato con SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

I dati in Azure SQL Data Warehouse vengono archiviati in tabelle relazionali con un archivio a colonne, un formato che riduce in modo significativo i costi di archiviazione dei dati, migliorando al tempo stesso le prestazioni delle query.  In base ai requisiti di utilizzo, le risorse di calcolo di Azure SQL Data Warehouse possono essere aumentate o ridotte o arrestate completamente, se non sono presenti processi attivi che richiedono risorse di calcolo.

Un servizio di bilanciamento del carico SQL gestisce il traffico SQL, assicurando prestazioni elevate. Tutte le macchine virtuali in questa architettura di riferimento vengono distribuite in un set di disponibilità con istanze di SQL Server configurate in un gruppo di disponibilità AlwaysOn per assicurare la disponibilità elevata e offrire funzionalità di ripristino di emergenza.

Questa architettura di riferimento per data warehouse include anche un livello Active Directory (AD) per la gestione delle risorse nell'architettura. La subnet di Active Directory consente l'adozione facilitata in una struttura di foresta di AD più ampia, permettendo il funzionamento continuo dell'ambiente anche in caso di mancata disponibilità dell'accesso alla foresta più grande. Tutte le macchine virtuali sono aggiunte al dominio nel livello Active Directory e usano i Criteri di gruppo di Active Directory per applicare configurazioni di sicurezza e conformità a livello di sistema operativo.

Una macchina virtuale viene usata come bastion host di gestione, fornendo una connessione sicura per l'accesso alle risorse distribuite da parte degli amministratori. I dati vengono caricati nell'area di staging tramite questo bastion host di gestione. **Azure consiglia di configurare una VPN o una connessione Azure ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento di Data Warehouse per GDPR](images/gdpr-datawarehouse-architecture.png?raw=true "Diagramma dell'architettura di riferimento di Data Warehouse per GDPR")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

-   Macchine virtuali di Azure
    - (1) Bastion host
    -   (2) Controller di dominio di Active Directory
    -   (2) Nodo del cluster di SQL Server
    -   (1) Controllo SQL Server
-   SET DI DISPONIBILITÀ
    - Controller di dominio di Active Directory
    - Controllo e nodi del cluster SQL
-   Rete virtuale
    - (4) Subnet
    -   (4) Gruppi di sicurezza di rete
- SQL Data Warehouse
- SQL Server Reporting Services
- Azure SQL Load Balancer
- Azure Active Directory
- Insieme di credenziali dei servizi di ripristino
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Data Catalog
- Centro sicurezza di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) è un data warehouse aziendale che sfrutta l'elaborazione MPP (Massively Parallel Processing) per eseguire rapidamente query complesse su petabyte di dati, consentendo agli utenti di identificare in modo efficiente i dati personali. Gli utenti possono usare semplici query T-SQL PolyBase per importare Big Data in SQL Data Warehouse e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) consente la creazione rapida di report con tabelle, grafici, mappe, misuratori, matrici e altro ancora per Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) rende le origini dati facilmente individuabili e comprensibili per gli utenti che gestiscono i dati. Le origini dati comuni possono essere registrate, contrassegnate con tag e sottoposte a ricerche di dati personali. I dati rimangono nella posizione esistente, ma una copia dei relativi metadati viene aggiunta a Data Catalog, insieme a un riferimento alla posizione dell'origine dati. I metadati vengono anche indicizzati per semplificare l'individuazione di ogni origine dati tramite una ricerca e per rendere l'origine dati comprensibile per gli utenti che la individuano.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di elementi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Estensione di OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Azure Key Vault
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo

### <a name="virtual-network"></a>Rete virtuale
Questa architettura di riferimento definisce una rete virtuale privata con spazio degli indirizzi 10.0.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che consentono o negano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Sono disponibili i gruppi di sicurezza di rete seguenti:
  - Un gruppo di sicurezza di rete per il livello dati (cluster SQL Server, controllo SQL Server e SQL Load Balancer)
  - Un gruppo di sicurezza di rete per il bastion host di gestione
  - Un gruppo di sicurezza di rete per Active Directory
  - Un gruppo di sicurezza di rete per SQL Server Reporting Services

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - I [log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sono abilitati e archiviati in un account di archiviazione
  - OMS Log Analytics è connesso alla [diagnostica del gruppo di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnet**: ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi in diversi modi, tra cui la crittografia e il controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati crittografati inattivi, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere i dati personali, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da GDPR.

**Crittografia dischi di Azure**: [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: l'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:
-   L'[autenticazione e l'autorizzazione di AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure.
-   Il database SQL è configurato per l'uso di [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. TDE garantisce che i dati personali archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente il rilevamento e la risposta a minacce potenziali non appena si verificano, fornendo avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati personali sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati in testo non crittografato.
- La funzionalità [Proprietà estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) può essere usata per interrompere l'elaborazione dei soggetti dei dati perché consente agli utenti di aggiungere proprietà personalizzate agli oggetti database e di contrassegnare i dati come "sospesi" per supportare la logica dell'applicazione, in modo da impedire l'elaborazione dei dati personali associati.
- La [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) consente agli utenti di definire criteri per limitare l'accesso ai dati per interrompere l'elaborazione.
- La funzionalità [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati personali sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La Maschera dati dinamica può individuare automaticamente dati sensibili potenziali e può suggerire le maschere appropriate da applicare, semplificando l'identificazione dei dati personali idonei per la protezione con GDPR e la riduzione dell'accesso, in modo da evitare l'uscita dal database tramite accesso non autorizzato. **Nota: i clienti dovranno modificare le impostazioni della Maschera dati dinamica in modo da adattarle al rispettivo schema del database.**

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati personali nell'ambiente di Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione al database SQL di Azure. Per altre informazioni, vedere la procedura per [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso a granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati personali. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni, ad esempio i dati personali.  Gli amministratori possono usare AAD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.

### <a name="security"></a>Sicurezza
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere i dati personali e l'accesso a tali dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e tutte le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

**Gestione delle patch**: le macchine virtuali Windows distribuite come parte di questa architettura di riferimento vengono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) di OMS, che consente la creazione di distribuzioni aggiornate per applicare patch alle macchine virtuali quando necessario.

**Protezione da malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato prova a installare o eseguire se stesso su macchine virtuali protette.

**Avvisi di sicurezza**: il [Centro sicurezza di Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) consente ai clienti di monitorare il traffico, raccogliere i log e analizzare le origini dati alla ricerca di minacce. Il Centro sicurezza di Azure accede inoltre alla configurazione esistente dei servizi di Azure in modo da fornire consigli su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati personali. Il Centro sicurezza di Azure include un [report di intelligence per le minacce](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

### <a name="business-continuity"></a>Continuità aziendale
**Disponibilità elevata**: i carichi di lavoro sono raggruppati in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per consentire di assicurare la disponibilità elevata delle macchine virtuali in Azure. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, rispettando il Contratto di servizio al 99,95% di Azure.

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

### <a name="logging-and-auditing"></a>Registrazione e controllo
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) fornisce una registrazione completa delle attività di sistema e degli utenti, nonché dell'integrità del sistema. La soluzione OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) raccoglie e analizza i dati generati dalle risorse in Azure e negli ambienti locali.
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) forniscono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i log eventi di sistema di Windows e i log di archiviazione BLOB di Azure, delle tabelle e delle code.
- **Archiviazione di log**: tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione. Questi log si connettono quindi a Log Analytics di Azure per l'elaborazione, l'archiviazione e la creazione di report nel dashboard.

Questa architettura include anche le soluzioni OMS seguenti:
-   [Valutazione AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione Antimalware crea report relativi a malware, minacce e stato della protezione.
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): il dashboard Sicurezza e controllo offre informazioni generali sullo stato di sicurezza delle risorse, fornendo metriche relative ai domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente la gestione da parte dei clienti degli aggiornamenti della sicurezza del sistema operativo, offrendo anche lo stato degli aggiornamenti disponibili e un processo per l'installazione degli aggiornamenti necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e il numero di agenti che inviano dati operativi.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/gdprDWdfd) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di rischio di Data Warehouse per GDPR](images/gdpr-datawarehouse-threat-model.png?raw=true "Modello di rischio di Data Warehouse per GDPR")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
In [Azure Security and Compliance Blueprint – GDPR Customer Responsibility Matrix](https://aka.ms/gdprCRM) (Progetto per la sicurezza e la conformità di Azure - Matrice di responsabilità per i clienti per GDPR) sono elencate le responsabilità di controller e responsabili per tutti gli articoli relativi al GDPR. Si noti che per i servizi di Azure un utente è in genere il controller e Microsoft ha il ruolo di responsabile.

[Azure Security and Compliance Blueprint - GDPR Data Warehouse Implementation Matrix](https://aka.ms/gdprDW) (Progetto per la sicurezza e la conformità di Azure - Matrice di implementazione del data warehouse per GDPR) fornisce informazioni sugli articoli del GDPR a cui l'architettura del data warehouse fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni articolo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento del data warehouse. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

### <a name="extract-transform-load-etl-process"></a>Processo ETL (Extract-Transform-Load)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) può caricare dati in Azure SQL Data Warehouse senza che sia necessario uno strumento ETL o di importazione separato. PolyBase consente l'accesso ai dati tramite query T-SQL. È possibile usare con PolyBase lo stack di business intelligence e analisi Microsoft, oltre a strumenti di terze parti compatibili con SQL Server.

### <a name="azure-active-directory-setup"></a>Configurazione di Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Un'istanza esistente di Active Directory di Windows Server può essere integrata con AAD in [quattro clic](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). I clienti possono anche associare l'infrastruttura di Active Directory distribuita (controller di dominio) a un'istanza esistente di AAD configurando l'infrastruttura di Active Directory distribuita come sottodominio di una foresta di AAD.

### <a name="optional-services"></a>Servizi facoltativi
Azure offre diversi servizi che semplificano l'archiviazione e lo staging di dati formattati e non formattati. I servizi seguenti possono essere aggiunti a questa architettura di riferimento in base ai requisiti dei clienti:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) è un servizio cloud gestito ideato per complessi progetti ibridi di estrazione, trasformazione e caricamento (ETL), di estrazione, caricamento e trasformazione (ELT) e di integrazione dei dati. Azure Data Factory include funzionalità utili per la verifica e l'individuazione dei dati personali, tra cui strumenti di visualizzazione e monitoraggio per identificare quando sono stati ricevuti i dati e la rispettiva provenienza. Con Azure Data Factory i clienti possono creare e pianificare flussi di lavoro basati sui dati, detti pipeline, che inseriscono dati provenienti da archivi diversi. Queste pipeline consentono ai clienti di inserire dati da origini interne ed esterne. I clienti possono quindi elaborare e trasformare i dati per l'output in archivi dati come Azure SQL Data Warehouse.
- I clienti possono eseguire lo staging di dati non strutturati in [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), che consente l'acquisizione di dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per analisi operative ed esplorative.  Azure Data Lake include funzionalità che consentono l'estrazione e la conversione dei dati. Azure Data Lake Store è compatibile con la maggior parte dei componenti open source dell'ecosistema Hadoop e si integra senza problemi con altri servizi di Azure, ad esempio Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
