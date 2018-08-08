---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per NIST SP 800-171
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f19478b29fd96cd2bd3ed5e0e994595d7b00edf3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392918"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per NIST SP 800-171

## <a name="overview"></a>Panoramica
Il documento [NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornisce indicazioni per la protezione di informazioni non classificate controllate (CUI, Controlled Unclassified Information) presenti in organizzazioni e sistemi informatici non federali. Il documento NIST SP 800-171 stabilisce 14 famiglie di requisiti di sicurezza per proteggere la riservatezza delle informazioni non classificate controllate.

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per aiutare i clienti a distribuire un'architettura di applicazione Web in Azure che implementa un subset di controlli NIST SP 800-171. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità e costituisce un punto di partenza per i clienti che vogliono creare e configurare applicazioni Web personalizzate in Azure.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. La distribuzione di questa architettura senza apportare modifiche è insufficiente a soddisfare completamente i requisiti di NIST SP 800-171. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
Questo progetto di Azure Security and Compliance Blueprint distribuisce un'architettura di riferimento per un'applicazione Web IaaS con un back-end del server SQL. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione e un bilanciamento del carico. Le macchine virtuali distribuite nei livelli Web e dati sono configurate in un set di disponibilità, mentre le istanze di SQL Server sono configurate in un gruppo di disponibilità Always On per disponibilità elevata. Le macchine virtuali sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo.

L'intera soluzione si basa su Archiviazione di Azure che i clienti possono configurare nel portale di Azure. Archiviazione di Azure crittografa tutti i dati tramite la crittografia del servizio di archiviazione per mantenere la riservatezza dei dati inattivi. L'archiviazione con ridondanza geografica garantisce che un evento negativo nel data center principale del cliente non comporti la perdita di dati perché una seconda copia verrà archiviata in una posizione separata lontana centinaia di chilometri.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory viene usato per controllare l'accesso alle chiavi e alle risorse distribuite in Azure Key Vault. L'integrità del sistema è monitorata tramite Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile.

Un bastion host di gestione fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite. **Microsoft consiglia di configurare una connessione VPN o Azure ExpressRoute per la gestione e l'importazione dei dati nella subnet dell'architettura di riferimento.**


![Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per NIST SP 800-171](images/nist171-iaaswa-architecture.png "Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per NIST SP 800-171")

Questa soluzione usa i servizi di Azure seguenti. Altri dettagli sono disponibili nella sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Macchine virtuali di Azure
    - (1) Gestione/bastion (Windows Server 2016 Datacenter)
    - (2) Controller di dominio Active Directory (Windows Server 2016 Datacenter)
    - (2) Nodo del cluster SQL Server (SQL Server 2017 su Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rete virtuale di Azure
    - (1) Rete /16
    - (5) Reti /24
    - (5) Gruppi di sicurezza di rete
- SET DI DISPONIBILITÀ
    - (1) Controller di dominio Active Directory
    - (1) Nodi del cluster SQL
    - (1) Web/IIS
- Gateway applicazione di Azure
    - (1) Web application firewall
        - Modalità firewall: prevenzione
        - Set di regole: OWASP 3.0
        - Porta listener: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Monitoraggio di Azure
- Azure Resource Manager
- Centro sicurezza di Azure
- Archiviazione di Azure
- Azure Log Analytics
- Automazione di Azure
- Cloud di controllo
- Insieme di credenziali dei servizi di ripristino

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto (RDP), l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Azure Key Vault
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: questa soluzione distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate all'interno di una rete virtuale. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione.

Vedere la configurazione per i [gruppi di sicurezza di rete](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribuita con questa soluzione. Le organizzazioni possono configurare gruppi di sicurezza di rete modificando il file indicato sopra usando [questa documentazione](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) come riferimento.

Ognuna delle subnet ha un gruppo di sicurezza di rete dedicato:
- 1 gruppo di sicurezza di rete per il gateway applicazione (LBNSG)
- 1 gruppo di sicurezza di rete per il bastion host (MGTNSG)
- 1 gruppo di sicurezza di rete per i controller di dominio primario e di backup (ADNSG)
- 1 gruppo di sicurezza di rete per SQL Server e il cloud di controllo (SQLNSG)
- 1 gruppo di sicurezza di rete per il livello web (WEBNSG)

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Inoltre, tutte le transazioni con Archiviazione di Azure attraverso il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi in diversi modi, tra cui la crittografia e il controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da NIST SP 800-171.

**Crittografia dischi di Azure**: questo servizio viene usato per crittografare i dischi delle macchine virtuali IaaS Windows. [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**SQL Server**: l'istanza di SQL Server usa le misure di sicurezza del database seguenti:
-   [Controllo di SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) tiene traccia degli eventi di database e li scrive nei log di controllo.
-   La funzione [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni per proteggere i dati inattivi. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Le [colonne crittografate](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- La [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare. In questo modo è più semplice ridurre l'accesso ai dati sensibili ed evitare così l'uscita dal database tramite accesso non autorizzato. **I clienti sono tenuti a modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.**

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:
-   [Azure Active Directory ](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono a SQL Server.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate risorse.  Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.

### <a name="security"></a>Sicurezza
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere i dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Il tipo di chiave è una chiave RSA a 2048 bit protetta con modulo di protezione hardware.
- A tutti gli utenti e le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
- La soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS.

**Gestione delle patch**: le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.

**Protezione da malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato prova a installare o eseguire se stesso su macchine virtuali protette.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) che vengono attivati in caso di minaccia o di attività sospetta. Le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un [report di intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

Inoltre, questa architettura di riferimento usa la [valutazione della vulnerabilità](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) nel Centro sicurezza di Azure. Dopo la configurazione, un agente del partner, ad esempio Qualys, crea report sui dati di vulnerabilità per la piattaforma di gestione del partner, che a propria volta trasmette i dati di vulnerabilità e monitoraggio dell'integrità di nuovo al Centro sicurezza di Azure, consentendo ai clienti di identificare rapidamente le macchine virtuali vulnerabili.

**Gateway applicazione**: l'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità di prevenzione)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- Possibilità di abilitare la [registrazione diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Probe di integrità personalizzati](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: la soluzione distribuisce macchine virtuali di livello Web in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). I set di disponibilità assicurano che le macchine virtuali vengano distribuite in più cluster hardware isolati per migliorare la disponibilità. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni di Macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

**Cloud di controllo**: il [cloud di controllo](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) è un tipo di controllo del quorum del cluster di failover in Windows Server 2016 che sfrutta Azure come punto di arbitraggio. Il cloud di controllo, come qualsiasi altro controllo del quorum, ottiene un voto e può partecipare ai calcoli del quorum, ma usa il servizio Archiviazione BLOB di Azure standard disponibile pubblicamente. Ciò consente di evitare il sovraccarico di manutenzione per le macchine virtuali ospitate in un cloud pubblico.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) includono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa, ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

**Log Analytics**: questi log vengono consolidati in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report nel dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Operations Management Suite, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Inoltre, il Centro sicurezza di Azure si integra con Log Analytics consentendo ai clienti di usare le query di Log Analytics per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Le seguenti [soluzioni di gestione](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Log Analytics sono incluse come parte di questa architettura:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di elementi consigliati specifici per l'infrastruttura di server distribuita.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello di agenti che inviano dati operativi.
-   [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): questa soluzione fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: la soluzione [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log da Azure SQL Server. La soluzione [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking) di Automazione consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze, consentendo alle organizzazioni di controllare, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/nist171-iaaswa-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web IaaS per NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Modello di minaccia dell'applicazione Web IaaS per NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per NIST SP 800-171](https://aka.ms/nist171-crm) elenca tutti i controlli di sicurezza richiesti da NIST SP 800-171. La matrice descrive in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

In [Modelli Blueprint per sicurezza e conformità di Azure - NIST SP 800-171 - Matrice di implementazione del controllo applicazioni Web IaaS](https://aka.ms/nist171-iaaswa-cim) sono fornite informazioni sui requisiti di NIST SP 800-171 a cui l'architettura delle applicazioni Web IaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web IaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.  
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.  
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.  
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.  
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
