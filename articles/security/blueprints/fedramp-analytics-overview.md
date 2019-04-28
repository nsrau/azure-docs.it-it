---
title: Progetto di sicurezza e conformità di Azure - Analisi per FedRAMP
description: Progetto di sicurezza e conformità di Azure - Analisi per FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586288"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Progetto di sicurezza e conformità di Azure: Analitica per FedRAMP

## <a name="overview"></a>Panoramica

Il programma [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) è un programma governativo degli Stati Uniti che stabilisce un approccio standardizzato in termini di valutazione della sicurezza, autorizzazione e monitoraggio continuo per i prodotti e i servizi cloud. Questo progetto di conformità e sicurezza di Azure indica come distribuire un'architettura di analisi Microsoft Azure che aiuti a implementare un subset di controlli FedRAMP High. La soluzione include indicazioni sulla distribuzione e la configurazione di risorse di Azure per un'architettura di riferimento comune, illustrando alcuni modi in cui i clienti possono soddisfare requisiti di sicurezza e conformità specifici e costituisce una base per la creazione e la configurazione di soluzioni di analisi personalizzate in Azure.

Questa architettura di riferimento, le guide all'implementazione dei controlli associate e i modelli di minaccia sono studiati per offrire ai clienti una base da adattare ai requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza alcuna modifica non è sufficiente a soddisfare completamente i requisiti dei principi di base di FedRAMP High. Tenere presente quanto segue:
- L'architettura rappresenta una baseline per aiutare i clienti a distribuire carichi di lavoro in Azure rispettando la conformità a FedRAMP.
- I clienti sono responsabili dell'esecuzione delle valutazioni appropriate in termini di sicurezza e conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche di implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura

Questa soluzione offre una piattaforma di analisi su cui i clienti possono creare i propri strumenti. L'architettura di riferimento descrive un caso d'uso generico in cui i clienti inseriscono i dati con importazioni in blocco tramite l'amministratore SQL/dati o con gli aggiornamenti dei dati operativi tramite un utente operativo. Entrambi i flussi di lavoro incorporano [Funzioni Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) per l'importazione di dati nel database SQL. È necessario configurare Funzioni di Azure nel portale di Azure per gestire le attività di importazione specifiche dei requisiti di analisi di ogni cliente.

Microsoft Azure offre al cliente un'ampia gamma di servizi di analisi e report. Questa soluzione include tuttavia Azure Analysis Services in combinazione con il database SQL di Azure per esplorare rapidamente i dati e ottenere risultati più velocemente tramite la modellazione efficace dei dati dei clienti. Azure Analytics Service è una forma di apprendimento automatico che consente di aumentare la velocità di query individuando nuove relazioni tra i set di dati. Dopo aver eseguito il training dei dati tramite diverse funzioni statistiche, è possibile sincronizzare fino a 7 pool di query aggiuntivi (8 con il server del cliente) con gli stessi modelli tabulari per distribuire il carico di lavoro di query e ridurre i tempi di risposta.

Per operazioni di analisi e di report avanzate, i database SQL possono essere configurati con indici columnstore. Sia Azure Analytics Services che i database SQL possono essere ridimensionati o disattivati completamente in risposta all'uso del cliente. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

Dopo che sono stati caricati nel database SQL di Azure e che ne è stato eseguito il training in Azure Analysis Services, ai dati viene applicato un codice hash da parte dell'utente operativo e dall'amministratore SQL/dati con Power BI. Power BI consente di visualizzare i dati in modo intuitivo e raggruppa le informazioni tra più set di dati per realizzare schemi più dettagliati. Grazie all'elevato livello di adattabilità e alla semplice integrazione con il database SQL di Azure, i clienti sono in grado di configurare la soluzione in modo da gestire una vasta gamma di scenari in base alle proprie esigenze aziendali.

L'intera soluzione si basa su una risorsa di Archiviazione di Azure che i clienti con un account possono configurare nel portale di Azure. Archiviazione di Azure crittografa tutti i dati tramite la crittografia del servizio di archiviazione per mantenere la riservatezza dei dati inattivi.  L'archiviazione con ridondanza geografica garantisce che un evento negativo nel data center principale del cliente non comporti la perdita di dati perché una seconda copia verrà archiviata in una posizione separata lontana centinaia di chilometri.

Per una sicurezza ottimale, questa architettura gestisce le risorse con Azure Active Directory in combinazione con Azure Key Vault. L'integrità del sistema è monitorata tramite Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile.

Il database SQL di Azure viene gestito in genere con SQL Server Management Studio (SSMS), eseguito in un computer locale configurato per l'accesso al database SQL di Azure tramite una connessione VPN o ExpressRoute sicura. **Azure consiglia di configurare una connessione Azure ExpressRoute o VPN per gestire e importare i dati nel gruppo di risorse dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento per l'analisi per FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Diagramma dell'architettura di riferimento per l'analisi per FedRAMP")

### <a name="roles"></a>Ruoli
Il progetto di analisi descrive uno scenario con tre tipi generali di utente, ovvero utente operativo, amministratore SQL/dati e sistemista. Il controllo degli accessi in base al ruolo di Azure consente di implementare in modo preciso la gestione degli accessi tramite ruoli personalizzati predefiniti. Sono disponibili risorse per la configurazione del [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) e per la definizione e l'implementazione di [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Sistemista
Un sistemista dispone della sottoscrizione ad Azure del cliente e configura la distribuzione della soluzione tramite il portale di Azure.

#### <a name="sqldata-administrator"></a>Amministratore SQL/dati
L'amministratore SQL/dati definisce la funzione di importazione in blocco dei dati e quella di aggiornamento dei dati operativi per il caricamento nel database SQL di Azure. L'amministratore SQL/dati non è responsabile di alcun aggiornamento dei dati operativi nel database, ma è in grado di visualizzare i dati tramite Power BI.

#### <a name="operational-user"></a>Utente operativo
L'utente operativo aggiorna regolarmente i dati ed è responsabile della generazione giornaliera dei dati. L'utente operativo usa Power BI anche per interpretare i risultati.

### <a name="azure-services"></a>Servizi di Azure

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).
- Funzioni di Azure
- Database SQL di Azure
- Azure Analysis Services
- Azure Active Directory
- Azure Key Vault
- Monitoraggio di Azure (log)
- Archiviazione di Azure
- Gateway VPN/ExpressRoute
- dashboard di Power BI

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

![testo alternativo](images/fedramp-analytics-components.png?raw=true "Diagramma dei componenti per l'analisi per FedRAMP")

**Funzioni di Azure**: [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) sono soluzioni per l'esecuzione di piccole porzioni di codice nel cloud tramite la maggior parte dei linguaggi di programmazione. In questa soluzione le funzioni si integrano con Archiviazione di Azure per effettuare automaticamente il pull dei dati dei clienti nel cloud, semplificando l'integrazione con altri servizi di Azure. Le funzioni sono facilmente scalabili e prevedono un addebito solo quando vengono eseguite.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) fornisce la modellazione dei dati aziendali e l'integrazione con servizi di piattaforma dati di Azure. Azure Analysis Services accelera l'esplorazione in enormi quantità di dati combinando le informazioni da più origini in un singolo modello di dati.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) fornisce analitica e le funzionalità di reporting per i clienti che tentando di eseguire il pull maggiore conoscenza grazie all'esterno le proprie attività di elaborazione dei dati.

### <a name="networking"></a>Rete
**Gruppi di sicurezza di rete**: [Gli Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sono configurati per gestire il traffico indirizzato a servizi e le risorse distribuite. I gruppi di sicurezza di rete vengono configurati in base a uno schema di negazione per impostazione predefinita e consentono solo il traffico contenuto nell'elenco di controllo di accesso preconfigurato.

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
  - [Log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) è connesso a log di diagnostica del NSG.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Replica dei dati** In Azure per enti pubblici sono disponibili due opzioni per la [replica dei dati](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - **Archiviazione con ridondanza geografica**, impostazione predefinita che consente di archiviare in modo asincrono i dati dei clienti in un data center separato al di fuori dell'area primaria. In questo modo si garantisce il recupero dei dati nel caso di una perdita totale dei dati nel data center principale.
 - **Archiviazione con ridondanza locale**, che può essere configurata, in alternativa, tramite l'account di archiviazione di Azure. L'archiviazione con ridondanza locale replica i dati all'interno di un'unità di scala di archiviazione, ospitata nella stessa area in cui il cliente crea il proprio account. Tutti i dati vengono replicati contemporaneamente, assicurando che non venga perso alcun dato di backup in caso di errore dell'unità di scala di archiviazione primaria.

**Archiviazione di Azure** Per soddisfare i requisiti correlati ai dati inattivi crittografati, tutti i servizi distribuiti in questa architettura di riferimento usano [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), che consente di archiviare i dati con la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="logging-and-audit"></a>Registrazione e controllo
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) genera una visualizzazione completa dei dati di monitoraggio, ad esempio log attività, metriche e dati di diagnostica, consentendo ai clienti di creare un quadro completo dello stato del sistema.  
[Log di monitoraggio di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) offre registrazione completa delle attività di sistema e utente, nonché dell'integrità del sistema. Raccoglie e analizza i dati generati dalle risorse in Azure e negli ambienti locali.
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i registri di sistema degli eventi di Windows e i log di Archiviazione BLOB di Azure, delle tabelle e delle code.
- **Log del firewall**: Il gateway applicazione fornisce log completi relativi a diagnostica e accesso. I log del firewall sono disponibili per le risorse del gateway applicazione con WAF abilitato.
- **Archiviazione di log**: tutti i log di diagnostica scrivono a un account di archiviazione di Azure crittografato e centralizzato per l'archiviazione con un periodo di conservazione definito di 2 giorni. Questi log connettere ai log di monitoraggio di Azure per l'elaborazione, l'archiviazione e i report del dashboard.

Questa architettura include anche le soluzioni di monitoraggio seguenti:
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): la dashboard Sicurezza e controllo offre informazioni dettagliate di alto livello sullo stato di sicurezza delle risorse, grazie a metriche relative a domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

### <a name="identity-management"></a>Gestione delle identità
-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). La crittografia delle colonne del database usa Azure AD per autenticare l'applicazione nel database SQL di Azure. Per altre informazioni, vedere la procedura per [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente un'accurata gestione degli accessi per Azure. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.

Per altre informazioni sull'uso delle funzionalità di sicurezza del database SQL di Azure, vedere l'[applicazione demo di esempio Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Security
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro una connessione alle risorse distribuite nell'ambito di questa architettura di riferimento di analisi dei dati. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. La configurazione corretta di una connessione ExpressRoute o VPN consente ai clienti di aggiungere un livello di protezione per i dati in transito.

### <a name="azure-active-directory-setup"></a>Configurazione di Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Un'istanza esistente di Active Directory di Windows Server può essere integrata con AAD in [quattro clic](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). I clienti possono anche associare l'infrastruttura di Active Directory distribuita (controller di dominio) a un'istanza esistente di AAD configurando l'infrastruttura di Active Directory distribuita come sottodominio di una foresta di AAD.

### <a name="additional-services"></a>Servizi aggiuntivi
#### <a name="iaas---vm-considerations"></a>IaaS - considerazioni sulle VM
Questa soluzione PaaS non incorpora alcuna macchina virtuale IaaS di Azure. Un cliente potrebbe creare una macchina virtuale di Azure per eseguire molti servizi PaaS descritti. In questo caso, servizi e funzionalità specifiche per la continuità aziendale e i log di monitoraggio di Azure possono essere usati:

##### <a name="business-continuity"></a>Continuità aziendale
- **Disponibilità elevata**: Carichi di lavoro server vengono raggruppati in un' [Set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) al fine di garantire la disponibilità elevata delle macchine virtuali in Azure. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

- **Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

##### <a name="monitoring-solutions"></a>Soluzioni di monitoraggio
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione antimalware crea report relativi a malware, minacce e stato di protezione.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente ai clienti di gestire gli aggiornamenti di sicurezza del sistema operativo, tra cui lo stato degli aggiornamenti disponibili e il processo per l'installazione di quelli necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello degli agenti che inviano dati operativi.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

##### <a name="security"></a>Security
- **Protezione antimalware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato tenta di installare o eseguire se stesso su macchine virtuali protette.
- **Gestione delle patch**: Le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.

#### <a name="azure-commercial"></a>Azure Commercial
Sebbene questa architettura di analisi dei dati non sia progettata per la distribuzione nell'ambiente di [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), obiettivi simili possono essere ottenuti tramite i servizi descritti in questa architettura di riferimento, nonché tramite servizi aggiuntivi disponibili solo nell'ambiente Azure Commercial. Azure Commercial mantiene un JAB P-ATO FedRAMP a livello di impatto moderato, consentendo agli enti e ai partner governativi di distribuire le informazioni moderatamente riservate nel cloud sfruttando l'ambiente Azure Commercial.

Azure Commercial offre un'ampia gamma di servizi di analisi per estrarre informazioni dettagliate da grandi quantità di dati:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), un componente di [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), sviluppa un modello di analisi predittiva da una o più origini dati. Per generare un modello efficace che applicazioni diverse, ad esempio Power BI, possono usare, sono disponibili funzioni statistiche applicate a più iterazioni.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative. Azure Data Lake Store è compatibile con la maggior parte dei componenti open source nell'ecosistema di Hadoop e si integra perfettamente con altri servizi di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/blueprintanalyticsthreatmodel) o è riportato più avanti:

## <a name="compliance-documentation"></a>Documentazione sulla conformità
Il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP High](https://aka.ms/blueprinthighcrm) elenca tutti i controlli di sicurezza richiesti dalla baseline FedRAMP High. Il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP Moderate](https://aka.ms/blueprintanalyticscimmod) elenca in modo analogo tutti i controlli di sicurezza richiesti dalla baseline FedRAMP Moderate. Entrambi i documenti descrivono in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [Progetto di conformità e sicurezza di Azure - Matrice di implementazione dei controlli FedRAMP High](https://aka.ms/blueprintanalyticscimhigh) e il [Progetto di conformità e sicurezza di Azure - Matrice di implementazione dei controlli FedRAMP Moderate](https://aka.ms/blueprintanalyticscimmod) includono informazioni sui controlli trattati dall'architettura di analisi per ogni baseline FedRAMP, incluse le descrizioni dettagliate sul modo in cui l'implementazione soddisfa i requisiti di ogni controllo trattato.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
