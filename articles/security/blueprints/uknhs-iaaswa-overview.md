---
title: 'Progetto per la sicurezza e la conformità di Azure: Applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito'
description: 'Progetto per la sicurezza e la conformità di Azure: Applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito'
services: security
author: jomolesk
ms.assetid: 6081bab2-315a-4af4-92a1-7c773f449d66
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 763ef04fbd14820ebecaa114d539b1026ebf4131
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343275"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-uk-nhs"></a>Progetto per la sicurezza e la conformità di Azure: Applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito

## <a name="overview"></a>Panoramica

Questo progetto per la sicurezza e la conformità di Azure fornisce un'architettura di riferimento e il materiale sussidiario per un’infrastruttura distribuita come applicazione Web (IaaS) appropriata per la raccolta, l'archiviazione e il recupero di dati sanitari. Questa soluzione illustra i modi in cui i clienti possono mantenere la conformità con le indicazioni specificate nella [Cloud Security Good Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) (Guida alle procedure consigliate per la sicurezza nel cloud) pubblicata da [Digital NHS](https://digital.nhs.uk/), un partner del Department of Health and Social Care (DHSC) del Regno Unito). La guida alle procedure consigliate per la sicurezza nel cloud si basa su 14 [principi di sicurezza nel cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) pubblicati dal National Cyber Security Centre (NCSC) del Regno Unito.

Questa architettura di riferimento, la guida all'implementazione e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici e non devono essere usati così come sono in un ambiente di produzione senza una configurazione aggiuntiva. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura

Questa soluzione distribuisce un'architettura di riferimento per un'applicazione Web IaaS con back-end di SQL Server. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione e un bilanciamento del carico. Le macchine virtuali distribuite nei livelli Web e dati sono configurate in un set di disponibilità, mentre le istanze di SQL Server sono configurate in un gruppo di disponibilità Always On per disponibilità elevata. Le macchine virtuali sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo.

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione per mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno del data center scelto di un cliente per garantire la resilienza. L'archiviazione con ridondanza geografica assicura che i dati vengano replicati in un data center secondario a centinaia di chilometri di distanza e archiviati di nuovo come tre copie all'interno di tale data center, impedendo a un evento avverso nel data center principale del cliente di generare una perdita di dati.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory viene usato per controllare l'accesso alle chiavi, incluse le risorse distribuite in Azure Key Vault. L'integrità del sistema viene monitorata tramite Centro sicurezza di Azure e Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile. Il gateway applicazione di Azure viene configurato come un firewall in modalità di prevenzione e non consente il traffico non TLS v1.2.

Un bastion host di gestione fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite. **Microsoft consiglia di configurare una rete VPN o una connessione ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito](images/uknhs-iaaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito")

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
    - (1) /16 Rete
    - (5) /24 Reti
    - (5) Gruppo di sicurezza di rete
    - Insieme di credenziali dei servizi di ripristino
- Gateway applicazione di Azure
    - (1) Web application firewall
        - Modalità firewall: prevenzione
        - Set di regole: OWASP 3.0
        - Porta listener: 443
- Azure Active Directory
- Cloud di controllo di Azure
- Azure Key Vault
- Azure Load Balancer
- Monitoraggio di Azure
- Azure Resource Manager
- Centro sicurezza di Azure
- Automazione di Azure
- Archiviazione di Azure
    - (7) Account di archiviazione con ridondanza geografica

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

L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da NHS Digital.

**Crittografia dischi di Azure**: [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa la funzionalità BitLocker di Windows per eseguire la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: l'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
- Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
- Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
- Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
- Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
- Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- La [maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare, semplificando l'identificazione e la riduzione dell'accesso ai dati, in modo da evitare l'uscita dal database tramite accesso non autorizzato. I clienti sono tenuti a modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure.
- L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Inoltre, la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione nel database SQL di Azure. Per altre informazioni, vedere l'articolo su come [proteggere i dati sensibili nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.


### <a name="security"></a>Sicurezza

**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere e accedere ai dati:

- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
- La soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS.

**Gestione delle patch**: le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita in modo da ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro), che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando necessario.

**Protezione da malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato prova a installare o eseguire se stesso su macchine virtuali protette.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), che vengono attivati in caso di minaccia o di attività sospetta. Le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un [report di intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

Inoltre, questa architettura di riferimento utilizza la [valutazione della vulnerabilità](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) nel Centro sicurezza di Azure. Una volta configurato, un agente del partner (ad es. Qualys) inizia a inviare i dati di vulnerabilità alla piattaforma di gestione del partner, che a propria volta trasmette i dati di vulnerabilità e monitoraggio dell'integrità al Centro sicurezza di Azure, consentendo ai clienti di identificare rapidamente le macchine virtuali vulnerabili.

**Gateway applicazione di Azure**: l'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

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

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

**Cloud di controllo**: il [cloud di controllo](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) è un tipo di controllo del quorum del cluster di failover in Windows Server 2016 che sfrutta Azure come punto di arbitraggio. Il cloud di controllo, come qualsiasi altro controllo del quorum, ottiene un voto e può partecipare ai calcoli del quorum, ma usa il servizio Archiviazione BLOB di Azure standard disponibile pubblicamente. Ciò consente di evitare il sovraccarico di manutenzione per le macchine virtuali ospitate in un cloud pubblico.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti, nonché dell'integrità del sistema:
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) includono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa, ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

**Log Analytics**: questi log vengono consolidati in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report nel dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per ogni tipo di dati, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Inoltre, il Centro sicurezza di Azure si integra con Log Analytics consentendo ai clienti di usare le query di Log Analytics per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Le seguenti [soluzioni di gestione](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Log Analytics sono incluse come parte di questa architettura:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di elementi consigliati specifici per l'infrastruttura di server distribuita.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello di agenti che inviano dati operativi.
-   [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): questa soluzione fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: la soluzione [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. La soluzione [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking) di Automazione consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze, consentendo alle organizzazioni di controllare, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/uknhs-iaaswa-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito](images/uknhs-iaaswa-threat-model.png?raw=true "Modello di minaccia dell'applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per il servizio sanitario nazionale (NHS) Regno Unito](https://aka.ms/uknhs-crm) elenca tutti i requisiti del servizio sanitario nazionale (NHS) Regno Unito. I dettagli della matrice se l'implementazione di ogni principio è responsabilità di Microsoft, del cliente o di entrambi.

[Azure Security and Compliance Blueprint – UK NHS IaaS Web Application Implementation Matrix](https://aka.ms/uknhs-iaaswa-cim) (Progetto per la sicurezza e la conformità di Azure - Matrice di implementazione dell'applicazione Web IaaS per il servizio sanitario nazionale (NHS) Regno Unito) fornisce informazioni sui requisiti del servizio sanitario nazionale (NHS) Regno Unito a cui l'architettura dell'applicazione Web IaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni principio specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute

È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web IaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un &quot;tunnel&quot; all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
