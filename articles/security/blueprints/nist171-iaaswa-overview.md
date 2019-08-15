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
ms.openlocfilehash: 88bca1a799d55ba59c8f5d2263f3219cfb66700e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946727"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per NIST SP 800-171

## <a name="overview"></a>Panoramica
Il documento [NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornisce indicazioni per la protezione di informazioni non classificate controllate (CUI, Controlled Unclassified Information) presenti in organizzazioni e sistemi informatici non federali. Il documento NIST SP 800-171 stabilisce 14 famiglie di requisiti di sicurezza per proteggere la riservatezza delle informazioni non classificate controllate.

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per aiutare i clienti a distribuire un'architettura di applicazione Web in Azure che implementa un subset di controlli NIST SP 800-171. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità. I clienti possono anche usarla come base per creare e configurare le proprie applicazioni Web in Azure.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici. Non devono essere usati così come sono in un ambiente di produzione. La distribuzione di questa architettura senza apportare modifiche è insufficiente a soddisfare completamente i requisiti di NIST SP 800-171. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata che usa questa architettura. I requisiti possono variare in base alle specifiche di implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
Questo progetto di sicurezza e conformità di Azure distribuisce un'architettura di riferimento per un'applicazione Web IaaS con un back-end SQL Server. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione di Azure e Azure Load Balancer. Le macchine virtuali (VM) distribuite per i livelli Web e dati vengono configurate in un set di disponibilità. Istanze di SQL Server vengono configurate in un gruppo di disponibilità AlwaysOn per la disponibilità elevata. Le VM sono aggiunte a un dominio. I criteri di gruppo di Active Directory applicano le configurazioni di sicurezza e conformità a livello di sistema operativo.

L'intera soluzione si basa su Archiviazione di Azure che i clienti possono configurare nel portale di Azure. Archiviazione di Azure crittografa tutti i dati tramite la crittografia del servizio di archiviazione per mantenere la riservatezza dei dati inattivi. L'archiviazione con ridondanza geografica garantisce che il verificarsi di un evento avverso nel data center primario del cliente non comporti la perdita di dati. Una seconda copia viene archiviata in una posizione separata distante centinaia di chilometri.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo (RBAC) di Azure Active Directory (Azure AD) viene usato per controllare l'accesso alle risorse distribuite e le chiavi in Azure Key Vault. L'integrità del sistema è monitorata tramite Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log. L'integrità del sistema viene visualizzata in un unico dashboard facile da usare.

Un bastion host di gestione fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite. *Microsoft consiglia di configurare una connessione VPN o Azure ExpressRoute per la gestione e l'importazione dei dati nella subnet dell'architettura di riferimento.*


![Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per NIST SP 800-171](images/nist171-iaaswa-architecture.png "Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per NIST SP 800-171")

Questa soluzione usa i servizi di Azure seguenti. Per altre informazioni, vedere la sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Macchine virtuali di Azure
    - (1) Gestione/bastion (Windows Server 2016 Datacenter)
    - (2) Controller di dominio Active Directory (Windows Server 2016 Datacenter)
    - (2) Nodo del cluster SQL Server (SQL Server 2017 su Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rete virtuale di Azure
    - (1) Rete /16
    - (5) Reti /24
    - (5) Gruppi di sicurezza di rete
- Set di disponibilità
    - (1) Controller di dominio Active Directory
    - (1) Nodi del cluster SQL
    - (1) Web/IIS
- Gateway applicazione di Azure
    - (1) Web application firewall
        - Modalità firewall: prevenzione
        - Set di regole: OWASP 3.0
        - Porta listener: 443
- Azure Active Directory
- Insieme di credenziali delle chiavi di Azure
- Azure Load Balancer
- Monitoraggio di Azure (log)
- Azure Resource Manager
- Centro sicurezza di Azure
- Archiviazione di Azure
- Automazione di Azure
- Cloud di controllo
- Insieme di credenziali dei servizi di ripristino

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Bastion host**: L'host Bastion è il singolo punto di ingresso che gli utenti possono usare per accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete (NSG).

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Estensione di Diagnostica di Azure](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Crittografia dischi di Azure](../azure-security-disk-encryption-overview.md) tramite Key Vault.
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) è abilitato in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione.

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: Questa soluzione distribuisce le risorse in un'architettura con subnet separate per il Web, il database, la Active Directory e la gestione all'interno di una rete virtuale. Le subnet sono separate logicamente da regole dei gruppi di sicurezza di rete applicate alle singole subnet. Le regole limitano il traffico tra subnet esclusivamente a quello necessario per la funzionalità e la gestione di sistema.

Vedere la configurazione per i [gruppi di sicurezza di rete](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribuita con questa soluzione. Le organizzazioni possono configurare i gruppi di sicurezza di rete modificando il file indicato in precedenza usando [questa documentazione](../../virtual-network/virtual-network-vnet-plan-design-arm.md) come riferimento.

Ognuna delle subnet ha un gruppo di sicurezza di rete dedicato:
- Un gruppo di sicurezza di rete per il gateway applicazione (LBNSG)
- Un gruppo di sicurezza di rete per il bastion host (MGTNSG)
- Un gruppo di sicurezza di rete per i controller di dominio primario e di backup (ADNSG)
- Un gruppo di sicurezza di rete per SQL Server e il cloud di controllo (SQLNSG)
- Un gruppo di sicurezza di rete per il livello Web (WEBNSG)

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Inoltre, tutte le transazioni con Archiviazione di Azure attraverso il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi tramite misure diverse, ad esempio crittografia e controllo del database.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, tutte le [risorse di archiviazione](https://azure.microsoft.com/services/storage/) usano la [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md). Questa funzionalità consente di proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da NIST SP 800-171.

**Crittografia dischi di Azure**: La crittografia del disco viene usata per crittografare i dischi delle macchine virtuali IaaS Windows. [Crittografia dischi](../azure-security-disk-encryption-overview.md) usa la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**SQL Server**: L'istanza di SQL Server usa le misure di sicurezza del database seguenti:
-   [Controllo di SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) tiene traccia degli eventi di database e li scrive nei log di controllo.
-   La funzione [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni per proteggere i dati inattivi. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Le [colonne crittografate](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- [Maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. Può individuare automaticamente dati sensibili potenziali e può suggerire le maschere appropriate da applicare. Maschera dati dinamica consente di ridurre l'accesso in modo che i dati sensibili non escano dal database tramite accesso non autorizzato. *I clienti sono tenuti a modificare le impostazioni in modo da adattarle al proprio schema del database.*

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure AD, inclusi gli utenti che accedono all'istanza di SQL Server.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere l'articolo che illustra come [integrare applicazioni con Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
-   Il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) può essere usato dagli amministratori per definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- I clienti possono usare [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) per ridurre al minimo il numero di utenti autorizzati ad accedere a determinate risorse. Gli amministratori possono usare Azure AD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il relativo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e JIT quando necessario.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione e configura le risposte automatizzate alle azioni sospette rilevate correlate alle identità di un'organizzazione. Esamina anche gli eventi imprevisti sospetti per eseguire l'azione appropriata per risolverli.

### <a name="security"></a>Security
**Gestione dei segreti**: La soluzione USA [Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione di chiavi e segreti. Key Vault consente di proteggere i segreti e le chiavi di crittografia usati da servizi e applicazioni cloud. Le funzionalità di Key Vault seguenti aiutano gli utenti a proteggere i dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e a tutte le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
- Questa soluzione è integrata con Key Vault per gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS.

**Gestione delle patch**: Le macchine virtuali Windows distribuite come parte di questa architettura di riferimento sono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione include anche il servizio [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) che consente di creare distribuzioni aggiornate per applicare patch alle macchine virtuali quando è necessario.

**Protezione antimalware**: [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) per macchine virtuali offre funzionalità di protezione in tempo reale che consentono di identificare e rimuovere virus, spyware e altro software dannoso. I clienti possono configurare avvisi che vengono generati in caso di tentativo di installazione o di esecuzione di software dannoso o indesiderato nelle macchine virtuali protette.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati di configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi diretti agli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), che vengono attivati in caso di minaccia o di attività sospetta. I clienti possono usare le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) per definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza dispone avvisi di sicurezza ed eventi imprevisti in ordine di priorità e rende più semplice per i clienti individuare e risolvere potenziali problemi di sicurezza. Per ogni minaccia rilevata viene generato un [report di intelligence sulle minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report). I team di risposta agli eventi imprevisti possono usare i report quando analizzano e correggono le minacce.

Questa architettura di riferimento usa la funzionalità di [valutazione della vulnerabilità](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) nel Centro sicurezza. Dopo la configurazione, un agente del partner, ad esempio Qualys, crea report sui dati di vulnerabilità per la piattaforma di gestione del partner, che a propria volta trasmette i dati di vulnerabilità e monitoraggio dell'integrità al Centro Sicurezza. I clienti possono usare queste informazioni per identificare rapidamente le macchine virtuali vulnerabili.

**Gateway applicazione di Azure**: L'architettura riduce il rischio di vulnerabilità della sicurezza usando un gateway applicazione con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Abilitazione di [Offload SSL](../../application-gateway/create-ssl-portal.md).
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Web application firewall](../../application-gateway/waf-overview.md) (modalità di prevenzione).
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0.
- Abilitazione della [registrazione diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Probe di integrità personalizzati](../../application-gateway/quick-create-portal.md).
- [Centro sicurezza](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza mette a disposizione anche un sistema di reputazione.

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: La soluzione distribuisce tutte le macchine virtuali in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). I set di disponibilità assicurano che le macchine virtuali vengano distribuite in più cluster hardware isolati per migliorare la disponibilità. Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%.

Insieme di credenziali di **servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale. Questo processo consente tempi di ripristino più rapidi.

**Cloud di controllo**: [Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) è un tipo di quorum di controllo del cluster di failover in Windows Server 2016 che usa Azure come punto di arbitraggio. Il cloud di controllo, come qualsiasi altro controllo del quorum, ottiene un voto e può partecipare ai calcoli del quorum. Usa l'Archiviazione BLOB di Azure standard disponibile pubblicamente. Ciò consente di evitare il sovraccarico di manutenzione per le macchine virtuali ospitate in un cloud pubblico.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](../../azure-monitor/platform/activity-logs-overview.md) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](../../azure-monitor/platform/diagnostic-logs-overview.md) includono tutti i log generati da ogni risorsa. ovvero i registri di sistema per gli eventi Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. Gli utenti possono configurare il periodo di conservazione, fino a 730 giorni, per soddisfare requisiti specifici.

**Log di Monitoraggio di Azure**: Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per ogni tipo di dati all'interno di aree di lavoro di Log Analytics. In questo modo tutti i dati possono essere analizzati insieme, indipendentemente dall'origine. Il Centro sicurezza si integra con i log di monitoraggio di Azure. I clienti possono usare le query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](../../monitoring/monitoring-solutions.md) di Azure seguenti:
-   [Valutazione Active Directory](../../azure-monitor/insights/ad-assessment.md): la soluzione Controllo integrità Active Directory Domain Services valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Valutazione SQL](../../azure-monitor/insights/sql-assessment.md): la soluzione Controllo integrità SQL consente di valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre ai clienti un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Integrità agente](../../monitoring/monitoring-solution-agenthealth.md): la soluzione Integrità agente indica quanti agenti vengono distribuiti e la relativa distribuzione geografica. Indica anche quanti agenti non rispondono e il numero di agenti che inviano dati operativi.
-   [Analisi log attività](../../azure-monitor/platform/collect-activity-logs.md): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: [Automazione](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log da SQL Server. I clienti possono usare la soluzione [Rilevamento modifiche](../../automation/change-tracking.md) di Automazione per identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, gestire la sicurezza e identificare le tendenze. Le organizzazioni possono usarlo per effettuare controlli, creare avvisi e archiviare i dati. Possono inoltre tenere traccia delle chiamate API nelle risorse di Azure.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/nist171-iaaswa-tm) o è riportato qui. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando apportano modifiche.

![Modello di minaccia dell'applicazione Web IaaS per NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Modello di minaccia dell'applicazione Web IaaS per NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per NIST SP 800-171](https://aka.ms/nist171-crm) elenca tutti i controlli di sicurezza richiesti da NIST SP 800-171. La matrice descrive in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [progetto di sicurezza e conformità di Azure - NIST SP 800-171 - Matrice di implementazione del controllo delle applicazioni Web IaaS](https://aka.ms/nist171-iaaswa-cim) fornisce informazioni sui controlli di NIST SP 800-171 a cui l'architettura delle applicazioni Web IaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web IaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita tramite Internet. I clienti possono usare la connessione per veicolare in modo sicuro le informazioni in un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico all'interno del tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Le connessioni ExpressRoute si connettono direttamente al provider di telecomunicazioni del cliente. Di conseguenza, i dati non vengono trasmessi via Internet e non sono esposti alla rete. Tali connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle connessioni tipiche. 

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio. 
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft. 
- I clienti possono copiare e usare questo documento per scopi di riferimento interni. 
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente. 
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
