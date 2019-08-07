---
title: Modelli Blueprint per sicurezza e conformità di Azure - Data warehouse per NIST SP 800-171
description: Modelli Blueprint per sicurezza e conformità di Azure - Data warehouse per NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 5a308dfd5467aecb8b0ff6c661de1cb8b1740590
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780725"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Modelli Blueprint per sicurezza e conformità di Azure - Data warehouse per NIST SP 800-171

## <a name="overview"></a>Panoramica
Il documento [NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornisce indicazioni per la protezione di informazioni non classificate controllate (CUI, Controlled Unclassified Information) presenti in organizzazioni e sistemi informatici non federali. Il documento NIST SP 800-171 stabilisce 14 famiglie di requisiti di sicurezza per proteggere la riservatezza delle informazioni non classificate controllate.

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per aiutare i clienti a distribuire un'architettura di data warehouse in Azure che implementa un subset di controlli NIST SP 800-171. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità. I clienti possono anche usarla come base per creare e configurare le proprie soluzioni di warehouse in Azure.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici. Non devono essere usati così come sono in un ambiente di produzione. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata che usa questa architettura. I requisiti possono variare in base alle specifiche di implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
Questa soluzione fornisce un'architettura di riferimento che implementa un data warehouse cloud a prestazioni e sicurezza elevate. L'architettura è costituita da due livelli di dati separati: un livello in cui i dati vengono importati, archiviati e sottoposti a staging in un ambiente SQL con cluster e un altro per SQL Data Warehouse, in cui i dati vengono caricati tramite uno strumento di estrazione, trasformazione e caricamento (ETL), ad esempio query T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) per l'elaborazione. Dopo che i dati sono stati archiviati in SQL Data Warehouse, è possibile eseguire l'analisi su larga scala.

Azure offre svariati servizi di creazione di report e di analisi per i clienti. Questa soluzione include SQL Server Reporting Services per la creazione rapida di report in SQL Data Warehouse. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. È consigliabile usare un'autorità di certificazione attendibile per una sicurezza avanzata.

Azure SQL Data Warehouse archivia i dati in tabelle relazionali con archiviazione a colonne. Questo formato consente di ridurre notevolmente i costi di archiviazione dei dati e di migliorare le prestazioni di query. In base ai requisiti di utilizzo, le risorse di calcolo di SQL Data Warehouse possono essere aumentate o ridotte o arrestate completamente, se non sono presenti processi attivi che richiedono risorse di calcolo.

Un servizio di bilanciamento del carico SQL gestisce il traffico SQL, assicurando prestazioni elevate. Tutte le macchine virtuali in questa architettura di riferimento vengono distribuite in un set di disponibilità con istanze di SQL Server configurate in un gruppo di disponibilità AlwaysOn per assicurare la disponibilità elevata e offrire funzionalità di ripristino di emergenza.

Questa architettura di riferimento per data warehouse include anche un livello Active Directory per la gestione delle risorse nell'architettura. La subnet di Active Directory consente l'adozione facilitata in una struttura di foresta di Active Directory più ampia, permettendo il funzionamento continuo dell'ambiente anche in caso di mancata disponibilità dell'accesso alla foresta più grande. Tutte le macchine virtuali sono aggiunte al dominio nel livello Active Directory e usano i Criteri di gruppo di Active Directory per applicare configurazioni di sicurezza e conformità a livello di sistema operativo

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione e mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno del data center scelto del cliente per garantire la resilienza. L'archiviazione con ridondanza geografica assicura che i dati vengano replicati in un data center secondario a centinaia di chilometri di distanza e archiviati di nuovo come tre copie all'interno di tale data center, impedendo a un evento negativo nel data center principale del cliente di generare una perdita di dati.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory (Azure AD) viene usato per controllare l'accesso alle risorse distribuite. Queste risorse includono le chiavi dei clienti in Azure Key Vault. L'integrità del sistema è monitorata tramite il Centro sicurezza di Azure e Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log. L'integrità del sistema viene visualizzata in un unico dashboard facile da usare.

Una macchina virtuale viene usata come bastion host di gestione realizzando una connessione sicura per l'accesso alle risorse distribuite da parte degli amministratori. I dati vengono caricati nell'area di staging tramite questo bastion host di gestione. *Microsoft consiglia di configurare una connessione VPN o Azure ExpressRoute per la gestione e l'importazione dei dati nella subnet dell'architettura di riferimento.*

![Diagramma dell'architettura di riferimento di data warehouse per NIST SP 800-171](images/nist171-dw-architecture.png "Diagramma dell'architettura di riferimento di data warehouse per NIST SP 800-171")

Questa soluzione usa i servizi di Azure seguenti. Per altre informazioni, vedere la sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Set di disponibilità
    - Controller di dominio Active Directory
    - Controllo e nodi del cluster SQL Server
- Azure Active Directory
- Azure Data Catalog
- Insieme di credenziali delle chiavi di Azure
- Monitoraggio di Azure (log)
- Centro sicurezza di Azure
- Azure Load Balancer
- Archiviazione di Azure
- Macchine virtuali di Azure
    - (1) Bastion host
    - (2) Controller di dominio di Active Directory
    - (2) Nodo del cluster di SQL Server
    - (1) Controllo SQL Server
- Rete virtuale di Azure
    - (1) Rete /16
    - (4) Reti /24
    - (4) Gruppi di sicurezza di rete
- Insieme di credenziali dei servizi di ripristino
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Azure SQL data warehouse**: [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) è un data warehouse aziendale che sfrutta i vantaggi dell'elaborazione parallela massiva per eseguire rapidamente query complesse su petabyte di dati. Gli utenti possono usare semplici query T-SQL PolyBase per importare Big Data in SQL Data Warehouse e quindi sfruttare la potenza dell'elaborazione parallela massiva per eseguire analisi ad alte prestazioni.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) consente di creare rapidamente report con tabelle, grafici, mappe, misuratori, matrici e altro ancora per SQL data warehouse.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) rende le origini dati facilmente individuabili e comprensibili per gli utenti che gestiscono i dati. Le origini dati comuni possono essere registrate, contrassegnate con tag e sottoposte a ricerche di dati. I dati rimangono nella posizione esistente, ma una copia dei relativi metadati viene aggiunta a Data Catalog. È incluso un riferimento alla posizione dell'origine dati. I metadati vengono indicizzati per semplificare l'individuazione delle origini dati tramite la ricerca. L'indicizzazione li rende anche comprensibili agli utenti che li individuano.

**Bastion host**: L'host Bastion è il singolo punto di ingresso che gli utenti possono usare per accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Key Vault.
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) è abilitato in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione.

### <a name="virtual-network"></a>Rete virtuale
Questa architettura di riferimento definisce una rete privata virtuale con uno spazio degli indirizzi 10.0.0.0/16.

**Gruppi di sicurezza di rete**: I [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che consentono o negano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Sono disponibili i gruppi di sicurezza di rete seguenti:
  - Un gruppo di sicurezza di rete per il livello dati (cluster SQL Server, controllo SQL Server e SQL Load Balancer)
  - Un gruppo di sicurezza di rete per il bastion host di gestione
  - Un gruppo di sicurezza di rete per Active Directory
  - Un gruppo di sicurezza di rete per SQL Server Reporting Services

Per ogni gruppo di sicurezza di rete sono aperti protocolli e porte specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione.
  - I log di monitoraggio di Azure sono connessi alla [diagnostica di NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subnet**: Ogni subnet è associata al rispettivo gruppo di sicurezza di rete.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi tramite misure diverse, ad esempio crittografia e controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, tutte le [risorse di archiviazione](https://azure.microsoft.com/services/storage/) usano la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). In questo modo è possibile proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità.

**Crittografia dischi di Azure**: [Crittografia dischi](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione si integra con Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: L'istanza di database SQL usa le seguenti misure di sicurezza del database:
-   L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Il database SQL di Azure è configurato per usare [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) che esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni per proteggere i dati inattivi. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   [Rilevamento delle minacce SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente il rilevamento e la risposta alle minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- Le [proprietà estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) possono essere usate per interrompere l'elaborazione degli elementi soggetti ai dati perché consentono agli utenti di aggiungere proprietà personalizzate agli oggetti di database e di contrassegnare i dati come "Sospesi" per supportare la logica dell'applicazione, in modo da impedire l'elaborazione dei dati finanziari associati.
- La [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) consente agli utenti di definire criteri per limitare l'accesso ai dati per interrompere l'elaborazione.
- [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. Può individuare automaticamente dati sensibili potenziali e può suggerire le maschere appropriate da applicare. Maschera dati dinamica consente di ridurre l'accesso in modo che i dati sensibili non escano dal database tramite accesso non autorizzato. *I clienti sono tenuti a modificare le impostazioni in modo da adattarle al proprio schema del database.*

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure AD, inclusi quelli che accedono al database SQL.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere l'articolo che illustra come [integrare applicazioni con Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure AD per autenticare l'applicazione nel database SQL di Azure. Per altre informazioni, vedere la procedura per [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) può essere usato dagli amministratori per definire le autorizzazioni di accesso con granularità fine. In questo modo è possibile concedere agli utenti solo il livello di accesso necessario per lavorare. Invece di concedere a ogni utente l'accesso senza limiti alle risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso alle risorse e ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) può essere usato dai clienti per ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni, ad esempio i dati. Gli amministratori possono usare Azure AD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il relativo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e JIT quando necessario.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione e configura le risposte automatizzate alle azioni sospette rilevate correlate alle identità di un'organizzazione. Esamina anche gli eventi imprevisti sospetti per eseguire l'azione appropriata per risolverli.

### <a name="security"></a>Security
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. Key Vault consente di proteggere i segreti e le chiavi di crittografia usati da servizi e applicazioni cloud. Le funzionalità di Key Vault seguenti aiutano gli utenti a proteggere i dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e a tutte le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

**Gestione delle patch**: Le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.

**Protezione antimalware**: [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) per macchine virtuali offre funzionalità di protezione in tempo reale che consentono di identificare e rimuovere virus, spyware e altro software dannoso. I clienti possono configurare avvisi che vengono generati in caso di tentativo di installazione o di esecuzione di software dannoso o indesiderato nelle macchine virtuali protette.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati di configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi diretti agli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), che vengono attivati in caso di minaccia o di attività sospetta. I clienti possono usare le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) per definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza dispone avvisi di sicurezza ed eventi imprevisti in ordine di priorità e rende più semplice per i clienti individuare e risolvere potenziali problemi di sicurezza. Per ogni minaccia rilevata viene generato un [report di intelligence sulle minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report). I team di risposta agli eventi imprevisti possono usare i report quando analizzano e correggono le minacce.

Questa architettura di riferimento usa anche la funzionalità di [valutazione della vulnerabilità](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) nel Centro sicurezza. Dopo la configurazione, un agente del partner, ad esempio Qualys, crea report sui dati di vulnerabilità per la piattaforma di gestione del partner, che a propria volta trasmette i dati di vulnerabilità e monitoraggio dell'integrità al Centro Sicurezza. I clienti possono usare queste informazioni per identificare rapidamente le macchine virtuali vulnerabili.

### <a name="business-continuity"></a>Continuità aziendale
**Disponibilità elevata**: I carichi di lavoro del server vengono raggruppati in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per garantire la disponibilità elevata delle macchine virtuali in Azure. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

Insieme di credenziali di **servizi di ripristino**: L'insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale. Questo processo consente tempi di ripristino più rapidi.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati dalle risorse. ovvero i registri di sistema degli eventi del sistema Windows, i log di archiviazione, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione Azure. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. Gli utenti possono configurare il periodo di conservazione, fino a 730 giorni, per soddisfare requisiti specifici.

**Log di Monitoraggio di Azure**: Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per ogni tipo di dati all'interno di aree di lavoro di Log Analytics. In questo modo tutti i dati possono essere analizzati insieme, indipendentemente dall'origine. Il Centro sicurezza si integra con i log di monitoraggio di Azure. I clienti possono usare le query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Azure seguenti:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory Domain Services valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL consente di valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre ai clienti un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente indica quanti agenti vengono distribuiti e la relativa distribuzione geografica. Indica anche quanti agenti non rispondono e il numero di agenti che inviano dati operativi.
-   [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: [Automazione](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. I clienti possono usare la soluzione [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking) di Automazione per identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, gestire la sicurezza e identificare le tendenze. Le organizzazioni possono usarlo per effettuare controlli, creare avvisi e archiviare i dati. Possono inoltre tenere traccia delle chiamate API nelle risorse di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/nist171-dw-tm) o è riportato qui. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando apportano modifiche.

![Modello di rischio di Data Warehouse per NIST SP 800-171](images/nist171-dw-threat-model.png "Modello di rischio di Data Warehouse per NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per NIST SP 800-171](https://aka.ms/nist171-crm) elenca tutti i controlli di sicurezza richiesti da NIST SP 800-171. La matrice descrive in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [Progetto di sicurezza e conformità di Azure - NIST SP 800-171 - Matrice di implementazione dei controlli di data wharehouse](https://aka.ms/nist171-dw-cim) contiene informazioni sui controlli di NIST SP 800-171 a cui fa riferimento l'architettura del data warehouse, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento del data warehouse. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita tramite Internet. I clienti possono usare la connessione per veicolare in modo sicuro le informazioni in un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico all'interno del tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Le connessioni ExpressRoute si connettono direttamente al provider di telecomunicazioni del cliente. Di conseguenza, i dati non vengono trasmessi via Internet e non sono esposti alla rete. Tali connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle connessioni tipiche.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

### <a name="extract-transform-load-process"></a>Processo di estrazione, trasformazione e caricamento
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) può caricare dati in SQL Data Warehouse senza che sia necessario uno strumento ETL o di importazione separato. PolyBase consente l'accesso ai dati tramite query T-SQL. Con PolyBase è possibile usare lo stack di business intelligence e analisi Microsoft, oltre a strumenti di terze parti compatibili con SQL Server.

### <a name="azure-ad-setup"></a>Configurazione di Azure AD
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Active Directory in locale può essere integrata con Azure AD in [quattro clic](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). I clienti possono anche associare l'infrastruttura distribuita di Active Directory (controller di dominio) ad Azure AD. A tale scopo, rendere l'infrastruttura distribuita di Active Directory un sottodominio di una foresta di AD Azure.

### <a name="optional-services"></a>Servizi facoltativi
Azure offre diversi servizi che semplificano l'archiviazione e lo staging di dati formattati e non formattati. I servizi seguenti possono essere aggiunti a questa architettura di riferimento in base ai requisiti dei clienti:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) è un servizio cloud gestito ideato per complessi progetti ibridi di estrazione, trasformazione e caricamento e di integrazione dei dati. Data Factory include funzionalità utili per il monitoraggio e l'individuazione dei dati, tra cui strumenti di visualizzazione e monitoraggio per identificare quando sono stati ricevuti i dati e la rispettiva provenienza. I clienti possono creare e pianificare flussi di lavoro basati sui dati (denominati pipeline) per inserire dati da archivi diversi. È possibile usare le pipeline per inserire i dati da origini interne ed esterne. I clienti possono quindi elaborare e trasformare i dati per l'output in archivi dati come SQL Data Warehouse.
- I clienti possono eseguire lo staging di dati non strutturati in [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) che consente l'acquisizione di dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per analisi operative ed esplorative. Azure Data Lake include funzionalità che consentono l'estrazione e la conversione dei dati. Azure Data Lake Storage è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop e si integra bene anche con altri servizi di Azure, ad esempio SQL Data Warehouse.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
