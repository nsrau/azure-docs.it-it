---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per GDPR
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161854"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per GDPR

## <a name="overview"></a>Panoramica
Il Regolamento generale sulla protezione dei dati (GDPR, General Data Protection Regulation) contiene molti requisiti relativi alla raccolta, all'archiviazione e all'uso delle informazioni personali, ad esempio il modo in cui le organizzazioni identificano e proteggono i dati personali, rispettano i requisiti a livello di trasparenza, rilevano e segnalano violazioni ai dati personali ed eseguono la formazione del personale addetto alla privacy e di altri dipendenti. Il GDPR offre ai singoli utenti maggiore controllo sui dati personali e impone molti obblighi nuovi alle organizzazioni che raccolgono, gestiscono o analizzano i dati personali. Il GDPR impone nuove regole per le organizzazioni che offrono beni e servizi a persone nell'Unione europea o che raccolgono e analizzano dati collegati a persone residenti nell'Unione europea. Il GDPR si applica indipendentemente da dove si trova un'organizzazione.

Microsoft ha progettato Azure con misure di sicurezza e criteri di privacy leader di settore per la salvaguardia dei dati sul cloud, incluse le categorie dei dati personali identificate dal GDPR. Le [condizioni contrattuali](http://aka.ms/Online-Services-Terms) Microsoft impongono a Microsoft il rispetto dei requisiti dei responsabili.

Questo progetto per la sicurezza e la conformità di Azure contiene le linee guida per la distribuzione di un ambiente di infrastruttura distribuita come servizio (IaaS) adatto a una semplice applicazione Web per Internet. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità del GDPR e costituisce un punto di partenza per i clienti che vogliono creare e configurare soluzioni di applicazioni Web IaaS personalizzate in Azure. I clienti possono usare questa architettura di riferimento e seguire il [processo in quattro passaggi](https://aka.ms/gdprebook) di Microsoft per ottenere la conformità al GDPR:
1. Individuazione: identificare i tipi di dati personali esistenti e la rispettiva posizione.
2. Gestione: regolamentare la modalità d'uso e di accesso dei dati personali.
3. Protezione: stabilire controlli di sicurezza per evitare, rilevare e rispondere alle vulnerabilità e alle violazioni di dati.
4. Segnalazione: mantenere la documentazione necessaria e gestire le richieste di dati e le notifiche relative alle violazioni.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. Tenere presente quanto segue:
- L'architettura fornisce una baseline per aiutare i clienti a distribuire carichi di lavoro in Azure in modo da assicurare la conformità al GDPR.
- I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
Questa soluzione distribuisce un'architettura di riferimento per un'applicazione Web IaaS con back-end di SQL Server. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione e un servizio di bilanciamento del carico. Le macchine virtuali distribuite nei livelli Web e dati sono configurate in un set di disponibilità, mentre le istanze di SQL Server sono configurate in un gruppo di disponibilità AlwaysOn per disponibilità elevata. Le macchine virtuali sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo. Un bastion host di gestione fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite. **Azure consiglia di configurare una rete VPN o una connessione ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per GDPR](images/gdpr-iaaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per GDPR")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

- Macchine virtuali di Azure
    - (1) Gestione/bastion (Windows Server 2016 Datacenter)
    - (2) Controller di dominio Active Directory (Windows Server 2016 Datacenter)
    - (2) Nodo del cluster SQL Server (SQL Server 2017 su Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- SET DI DISPONIBILITÀ
    - (1) Controller di dominio Active Directory
    - (1) Nodi del cluster SQL
    - (1) Web/IIS
- Rete virtuale di Azure
    - (1) /16 reti virtuali
    - (5) /24 subnet
    - Impostazioni DNS configurate in entrambi i controller di dominio
- Azure Load Balancer
- Gateway applicazione di Azure
    - (1) Gateway applicazione WAF abilitato
        - Modalità firewall: prevenzione
        - Set di regole: OWASP 3.0
        - Listener: porta 443
- Archiviazione di Azure
    - (7) Account di archiviazione con ridondanza geografica
- Cloud di controllo
- Insieme di credenziali dei servizi di ripristino
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Centro sicurezza di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete (NSG, Network Security Group).

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Estensione di OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
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
- 1 gruppo di sicurezza di rete per il livello Web (WEBNSG)

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Inoltre, tutte le transazioni con Archiviazione di Azure attraverso il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi in diversi modi, tra cui la crittografia e il controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati crittografati inattivi, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere i dati personali, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti dal GDPR.

**Crittografia dischi di Azure**: questo servizio viene usato per crittografare i dischi delle macchine virtuali IaaS Windows. [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**SQL Server**: l'istanza di SQL Server usa le misure di sicurezza del database seguenti:
-   L'[autenticazione e l'autorizzazione di AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di archiviazione di Azure.
-   Il database SQL è configurato per l'uso di [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. TDE garantisce che i dati personali archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente il rilevamento e la risposta a minacce potenziali non appena si verificano, fornendo avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati personali sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati in testo non crittografato.
- La funzionalità [Proprietà estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) può essere usata per interrompere l'elaborazione dei soggetti dei dati perché consente agli utenti di aggiungere proprietà personalizzate agli oggetti database e di contrassegnare i dati come "sospesi" per supportare la logica dell'applicazione, in modo da impedire l'elaborazione dei dati personali associati.
- La [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) consente agli utenti di definire criteri per limitare l'accesso ai dati per interrompere l'elaborazione.
- La funzionalità [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati personali sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La Maschera dati dinamica può individuare automaticamente dati sensibili potenziali e può suggerire le maschere appropriate da applicare, semplificando l'identificazione dei dati personali idonei per la protezione in base al GDPR e la riduzione dell'accesso, in modo da evitare l'uscita dal database tramite accesso non autorizzato. **Nota: i clienti dovranno modificare le impostazioni della Maschera dati dinamica in modo da adattarle al rispettivo schema del database.**

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati personali nell'ambiente di Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono a SQL Server.
-   L'autenticazione all'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione a SQL Server. Per altre informazioni, vedere l'articolo su come [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati personali. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione stessa.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate risorse.  Gli amministratori possono usare AAD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time, quando necessario.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità di un'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità di un'organizzazione e ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.
- Un'istanza di Active Directory IaaS distribuita fornisce la gestione delle identità a livello di sistema operativo per le macchine virtuali IaaS distribuite.

### <a name="security"></a>Sicurezza
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione di chiavi e segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere i dati personali e l'accesso a tali dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e a tutte le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
- La soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS.

**Gestione delle patch**: le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) di OMS, che consente la creazione di distribuzioni aggiornate per applicare patch alle macchine virtuali, quando necessario.

**Protezione da malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili nel caso di tentativi di installazione o esecuzione di software dannoso o indesiderato su macchine virtuali protette.

**Avvisi di sicurezza**: il [Centro sicurezza di Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) consente ai clienti di monitorare il traffico, raccogliere i log e analizzare le origini dati alla ricerca di minacce. Il Centro sicurezza di Azure accede inoltre alla configurazione esistente dei servizi di Azure in modo da fornire consigli su configurazione e servizi per migliorare le condizioni di sicurezza e proteggere i dati personali. Il Centro sicurezza di Azure include un [report di intelligence](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti nelle attività di indagine e reazione alle minacce.

**Gateway applicazione**: l'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con web application firewall (WAF) e il set di regole OWASP abilitati. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità WAF)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- Possibilità di abilitare la [registrazione diagnostica](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Probe di integrità personalizzati](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: la soluzione distribuisce macchine virtuali di livello Web in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). I set di disponibilità assicurano che le macchine virtuali vengano distribuite in più cluster hardware isolati per migliorare la disponibilità. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni di Macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

**Cloud di controllo**: il [cloud di controllo](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) è un tipo di controllo del quorum del cluster di failover in Windows Server 2016 che sfrutta Azure come punto di arbitraggio. Il cloud di controllo, come qualsiasi altro controllo del quorum, ottiene un voto e può partecipare ai calcoli del quorum, ma usa il servizio Archiviazione BLOB di Azure standard disponibile pubblicamente. Ciò consente di evitare il sovraccarico di manutenzione per le macchine virtuali ospitate in un cloud pubblico.

### <a name="logging-and-auditing"></a>Registrazione e controllo

OMS (Microsoft Operations Management Suite) offre una soluzione di registrazione completa delle attività di sistema e degli utenti, nonché dell'integrità del sistema. La soluzione [Log Analytics](https://azure.microsoft.com/services/log-analytics/) di OMS raccoglie e analizza i dati generati dalle risorse in Azure e negli ambienti locali.
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) includono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa, ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Azure Key Vault e i log degli accessi e del firewall del gateway applicazione.
- **Archiviazione di log**: tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un periodo massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione. Questi log si connettono quindi a Log Analytics di Azure per l'elaborazione, l'archiviazione e la creazione di report nel dashboard.

Questa architettura include anche le soluzioni OMS seguenti:
-   [Valutazione AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione Antimalware crea report relativi a malware, minacce e stato della protezione.
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): il dashboard Sicurezza e controllo offre informazioni dettagliate sullo stato di sicurezza delle risorse, grazie a metriche relative a domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente la gestione da parte dei clienti degli aggiornamenti della sicurezza del sistema operativo, offrendo anche lo stato degli aggiornamenti disponibili e un processo per l'installazione degli aggiornamenti necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, il numero di agenti non reattivi e il numero di agenti che inviano dati operativi.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento illustrato di seguito è disponibile anche per il [download](https://aka.ms/gdprIaaSdfd). Questo modello può aiutare i clienti a comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web IaaS per GDPR](images/gdpr-iaaswa-threat-model.png?raw=true "Modello di minaccia dell'applicazione Web IaaS per GDPR")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

In [Azure Security and Compliance Blueprint - GDPR Customer Responsibility Matrix](https://aka.ms/gdprCRM) (Progetto per la sicurezza e la conformità di Azure - Matrice di responsabilità per i clienti per GDPR) sono elencate le responsabilità di titolari e responsabili del trattamento dei dati per tutti gli articoli del GDPR. Si noti che per i servizi di Azure un cliente ha in genere il ruolo di titolare, mentre Microsoft ha il ruolo di responsabile.

[Azure Security and Compliance Blueprint - GDPR IaaS Web Application Matrix](https://aka.ms/gdprIaaSweb) (Progetto per la sicurezza e la conformità di Azure - Matrice di implementazione dell'applicazione Web IaaS per GDPR) fornisce informazioni sugli articoli del GDPR a cui l'architettura dell'applicazione Web IaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni articolo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web IaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione avviene tramite Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico nel tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevata e minore latenza rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi via Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.  
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.  
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.  
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.  
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
