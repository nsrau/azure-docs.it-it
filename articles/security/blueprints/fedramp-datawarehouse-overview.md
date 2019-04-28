---
title: Progetto di conformità e sicurezza di Azure - Data warehouse per automazione FedRAMP
description: Progetto di conformità e sicurezza di Azure - Data warehouse per automazione FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586033"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Progetto di sicurezza e conformità di Azure: Data Warehouse per l'automazione FedRAMP

## <a name="overview"></a>Panoramica

Il programma [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) è un programma governativo degli Stati Uniti che stabilisce un approccio standardizzato in termini di valutazione della sicurezza, autorizzazione e monitoraggio continuo per i prodotti e i servizi cloud. Questo progetto di conformità e sicurezza di Azure include le indicazioni su come offrire un'architettura di data warehouse Microsoft Azure che aiuti a implementare un sottoinsieme di controlli FedRAMP High. Questa soluzione include indicazioni sulla distribuzione e la configurazione di risorse di Azure per un'architettura di riferimento comune, mostrando alcuni modi in cui i clienti possono soddisfare requisiti di uniformità e di sicurezza specifici. Funge inoltre da base per la creazione e la configurazione di soluzioni di data warehouse proprie in Azure.

Questa architettura di riferimento, le guide all'implementazione dei controlli associate e i modelli di minaccia sono studiati per offrire ai clienti una base da adattare ai requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza alcuna modifica non è sufficiente a soddisfare completamente i requisiti dei principi di base di FedRAMP High. Tenere presente quanto segue:
- L'architettura rappresenta una baseline per aiutare i clienti a distribuire carichi di lavoro in Azure rispettando la conformità a FedRAMP.
- I clienti sono responsabili dell'esecuzione delle valutazioni appropriate in termini di sicurezza e conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche di implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura

Questa soluzione fornisce un'architettura di data warehouse di riferimento che implementa un data warehouse cloud a prestazioni e sicurezza elevate. L'architettura include due livelli di dati separati: un livello in cui i dati vengono importati, archiviati e sottoposti a staging in un ambiente SQL con cluster e un altro per Azure SQL Data Warehouse, in cui i dati vengono caricati tramite uno strumento ETL (ad esempio query T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) per l'elaborazione. Dopo che i dati sono stati archiviati in Azure SQL Data Warehouse, è possibile eseguire l'analisi su larga scala.

Microsoft Azure offre svariati servizi di creazione di report e di analisi per i clienti. Questa soluzione include SQL Server Reporting Services (SSRS) per la creazione rapida di report da Azure SQL Data Warehouse. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

I dati in Azure SQL Data Warehouse vengono archiviati in tabelle relazionali con uno spazio di archiviazione a colonne, vale a dire un formato che riduce in modo significativo i costi di archiviazione dei dati migliorando al tempo stesso le prestazioni delle query.  In base ai requisiti di utilizzo, le risorse di calcolo di Azure SQL Data Warehouse possono essere aumentate o ridotte o arrestate completamente, se non sono presenti processi attivi che richiedono risorse di calcolo.

Un servizio di bilanciamento del carico SQL gestisce il traffico SQL, assicurando prestazioni elevate. Tutte le macchine virtuali in questa architettura di riferimento vengono distribuite in un set di disponibilità con istanze di SQL Server configurate in un gruppo di disponibilità AlwaysOn per assicurare la disponibilità elevata e offrire funzionalità di ripristino di emergenza.

Questa architettura di riferimento per data warehouse include anche un livello Active Directory (AD) per la gestione delle risorse nell'architettura. La subnet di Active Directory consente l'adozione facilitata in una struttura di foresta di AD più ampia, permettendo il funzionamento continuo dell'ambiente anche in caso di mancata disponibilità dell'accesso alla foresta più grande. Tutte le macchine virtuali sono aggiunte al dominio nel livello Active Directory e usano i Criteri di gruppo di Active Directory per applicare configurazioni di sicurezza e conformità a livello di sistema operativo.

Una macchina virtuale viene usata come bastion host di gestione, fornendo una connessione sicura per l'accesso alle risorse distribuite da parte degli amministratori. I dati vengono caricati nell'area di staging tramite questo bastion host di gestione. **Azure consiglia di configurare una rete VPN o una connessione Azure ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento di data warehouse per FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Diagramma dell'architettura di riferimento di data warehouse per FedRAMP")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

Macchine virtuali di Azure
-   (1) Bastion host
-   (2) Controller di dominio di Active Directory
-   (2) Nodo del cluster di SQL Server
-   (1) Controllo SQL Server

SET DI DISPONIBILITÀ
-   (1) Controller di dominio Active Directory
-   (1) Controllo e nodi del cluster SQL

Rete virtuale
-   (4) Subnet
-   (4) Gruppi di sicurezza di rete

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Insieme di credenziali dei servizi di ripristino

Azure Key Vault

Log di Monitoraggio di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) è un Enterprise Data Warehouse (EDW) che si avvale Massively Parallel Processing (MPP) per eseguire rapidamente query complesse su petabyte di dati. Importare i Big Data in SQL Data Warehouse con semplici query T-SQL PolyBase e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) consente la rapida creazione di report con tabelle, grafici, mappe, misuratori, matrici e più per Azure SQL Data Warehouse.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete (NSG).

È stata creata una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Monitoraggio di Azure registra l'estensione](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Azure Key Vault (rispetta i requisiti di Azure per enti pubblici, dello standard PCI DSS, di HIPAA e altri)
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione

### <a name="virtual-network"></a>Rete virtuale
Questa architettura di riferimento definisce una rete privata virtuale con uno spazio degli indirizzi 10.0.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso (ACL) che consentono o negano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Sono disponibili i gruppi di sicurezza di rete seguenti:
  - Un gruppo di sicurezza di rete per il livello dati (cluster SQL Server, controllo SQL Server e SQL Load Balancer)
  - Un gruppo di sicurezza di rete per il bastion host di gestione
  - Un gruppo di sicurezza di rete per Active Directory
  - Un gruppo di sicurezza di rete per SQL Server Reporting Services

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
  - Log di monitoraggio di Azure è connessa la [diagnostica del NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnet**: Ogni subnet è associata al rispettivo gruppo di sicurezza di rete.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati crittografati inattivi, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Crittografia dischi di Azure**
[Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**. L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:
-   L'[autenticazione e l'autorizzazione di AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Il database SQL è configurato per l'uso di [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale dei dati e dei file di log per proteggere le informazioni inattive. TDE garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati in testo non crittografato.
-   [La maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) può essere creata dopo la distribuzione dell'architettura di riferimento. I clienti dovranno modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

### <a name="business-continuity"></a>Continuità aziendale
**Disponibilità elevata**: Carichi di lavoro server vengono raggruppati in un' [Set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) al fine di garantire la disponibilità elevata delle macchine virtuali in Azure. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

### <a name="logging-and-audit"></a>Registrazione e controllo
[Log di monitoraggio di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) offre registrazione completa delle attività di sistema e utente, nonché dell'integrità del sistema. Il [monitoraggio di Azure registra](https://azure.microsoft.com/services/log-analytics/) soluzione raccoglie e analizza i dati generati dalle risorse di Azure e negli ambienti locali.
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i registri di sistema degli eventi di Windows e i log di Archiviazione BLOB di Azure, delle tabelle e delle code.
- **Log del firewall**: Il gateway applicazione fornisce log completi relativi a diagnostica e accesso. I log del firewall sono disponibili per le risorse del gateway applicazione con WAF abilitato.
- **Archiviazione di log**: tutti i log di diagnostica scrivono a un account di archiviazione di Azure crittografato e centralizzato per l'archiviazione con un periodo di conservazione definito di 2 giorni. Questi log connettere ai log di monitoraggio di Azure per l'elaborazione, l'archiviazione e i report del dashboard.

Questa architettura include anche le soluzioni di monitoraggio seguenti:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione antimalware crea report relativi a malware, minacce e stato di protezione.
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): la dashboard Sicurezza e controllo offre informazioni dettagliate di alto livello sullo stato di sicurezza delle risorse, grazie a metriche relative a domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente ai clienti di gestire gli aggiornamenti di sicurezza del sistema operativo, tra cui lo stato degli aggiornamenti disponibili e il processo per l'installazione di quelli necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello degli agenti che inviano dati operativi.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) può essere il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). La crittografia delle colonne del database usa Azure AD per autenticare l'applicazione nel database SQL di Azure. Per altre informazioni, vedere la procedura per [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente un'accurata gestione degli accessi per Azure. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.

Per altre informazioni sull'uso delle funzionalità di sicurezza del database SQL di Azure, vedere l'[applicazione demo di esempio Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Security
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud.

**Protezione antimalware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato tenta di installare o eseguire se stesso su macchine virtuali protette.

**Gestione delle patch**: Le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.


## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento del data warehouse. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. La configurazione corretta di una connessione ExpressRoute o VPN consente ai clienti di aggiungere un livello di protezione per i dati in transito.

### <a name="extract-transform-load-etl-process"></a>Processo ETL (Extract-Transform-Load)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) può caricare dati in Azure SQL Data Warehouse senza che sia necessario uno strumento ETL o di importazione separato. PolyBase consente l'accesso ai dati tramite query T-SQL. È possibile usare con PolyBase lo stack di business intelligence e analisi Microsoft, oltre a strumenti di terze parti compatibili con SQL Server.

### <a name="azure-active-directory-setup"></a>Configurazione di Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Un'istanza esistente di Active Directory di Windows Server può essere integrata con AAD in [quattro clic](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). I clienti possono anche associare l'infrastruttura di Active Directory distribuita (controller di dominio) a un'istanza esistente di AAD configurando l'infrastruttura di Active Directory distribuita come sottodominio di una foresta di AAD.

### <a name="additional-services"></a>Servizi aggiuntivi
Sebbene questa architettura di data warehouse non sia progettata per la distribuzione nell'ambiente di [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), obiettivi simili possono essere ottenuti tramite i servizi descritti in questa architettura di riferimento, nonché i servizi aggiuntivi disponibili solo nell'ambiente Azure Commercial. Azure Commercial mantiene un JAB P-ATO FedRAMP a livello di impatto moderato, consentendo agli enti e ai partner governativi di distribuire le informazioni moderatamente riservate nel cloud sfruttando l'ambiente Azure Commercial.

Azure Commercial offre una vasta gamma di servizi che gestiscono lo staging e l'archiviazione dei dati formattati e non da usare nel data warehouse, tra cui:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) è un servizio cloud gestito ideato per complessi progetti ibridi di estrazione, trasformazione e caricamento (ETL), di estrazione, caricamento e trasformazione (ELT) e di integrazione dei dati. Con Azure Data Factory i clienti possono creare e pianificare flussi di lavoro basati sui dati, detti pipeline, che ricevono dati provenienti da archivi diversi. I clienti possono quindi elaborare e trasformare i dati per l'output in archivi dati come Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative. Azure Data Lake Store è compatibile con la maggior parte dei componenti open source dell'ecosistema Hadoop e si integra senza problemi con altri servizi di Azure, ad esempio Azure SQL Data Warehouse.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/blueprintdwthreatmodel) o è riportato più avanti:

![Modello di rischio di data warehouse per FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Modello di rischio di data warehouse per FedRAMP")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
Il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP High](https://aka.ms/blueprinthighcrm) elenca tutti i controlli di sicurezza richiesti dalla baseline FedRAMP High. Il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP Moderate](https://aka.ms/blueprintcrmmod) elenca in modo analogo tutti i controlli di sicurezza richiesti dalla baseline FedRAMP Moderate. Entrambi i documenti descrivono in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [Progetto di conformità e sicurezza di Azure - Matrice di implementazione controlli FedRAMP High](https://aka.ms/blueprintdwcimhigh) e il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP Moderate](https://aka.ms/blueprintdwcimmod) includono informazioni su quali controlli vengono coperti dall'architettura di data warehouse per ogni baseline FedRAMP, incluse le descrizioni dettagliate sul modo in cui l'implementazione soddisfa i requisiti di ogni controllo coperto.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
