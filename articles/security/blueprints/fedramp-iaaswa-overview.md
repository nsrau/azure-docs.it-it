---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per FedRAMP
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web IaaS per FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586135"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Progetto di sicurezza e conformità di Azure: Applicazione Web IaaS per FedRAMP

## <a name="overview"></a>Panoramica

Il [programma FedRAMP, ovvero Federal Risk and Authorization Management Program](https://www.fedramp.gov), è un programma governativo degli Stati Uniti che stabilisce un approccio standard per quanto riguarda valutazione della sicurezza, autorizzazione e monitoraggio continuo per i prodotti e i servizi cloud. L'automazione del progetto di sicurezza e conformità di Azure contiene le linee guida per la distribuzione di un ambiente di infrastruttura distribuita come servizio, ovvero IaaS, conforme a FedRAMP adatto a una semplice applicazione Web per Internet. Questa soluzione automatizza la distribuzione e la configurazione delle risorse di Azure per un'architettura di riferimento comune, mostrando i diversi modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità, e costituisce un'architettura di base con cui i clienti possono creare e configurare soluzioni personalizzate in Azure. La soluzione implementa un subset di controlli basati sui principi di base di FedRAMP High e su NIST SP 800-53. Per altre informazioni sui requisiti di FedRAMP e su questa soluzione, vedere la [documentazione sulla conformità](#compliance-documentation).
> [!NOTE]
> Questa soluzione viene distribuita in Azure per enti pubblici.

Questo progetto di automazione Azure Security and Compliance Blueprint distribuisce automaticamente un'architettura di riferimento per applicazioni Web IaaS con controlli di sicurezza preconfigurati per aiutare i clienti a realizzare la conformità ai requisiti di FedRAMP. La soluzione è costituita da modelli di Azure Resource Manager e script di PowerShell che semplificano la distribuzione e la configurazione delle risorse.

Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti dei principi di base di FedRAMP High. Tenere presente quanto segue:
- Questa architettura offre una soluzione di base per aiutare i clienti a usare Azure in modo conforme a FedRAMP.
- I clienti hanno la responsabilità di svolgere una valutazione appropriata della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

Per una rapida panoramica del funzionamento di questa soluzione, guardare questo [video](https://aka.ms/fedrampblueprintvideo), che ne spiega e descrive la distribuzione.

Fare clic [qui](https://aka.ms/fedrampblueprintrepo) per le istruzioni di distribuzione.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
Questa soluzione distribuisce un'architettura di riferimento per un'applicazione Web IaaS con back-end di SQL Server. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione e un bilanciamento del carico. Le macchine virtuali distribuite nei livelli Web e dati sono configurate in un set di disponibilità, mentre le istanze di SQL Server sono configurate in un gruppo di disponibilità AlwaysOn per disponibilità elevata. Le macchine virtuali sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo. Un bastion host offre una connessione sicura agli amministratori per accedere alle risorse distribuite. **Azure consiglia di configurare una rete VPN o una connessione Azure ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'applicazione Web IaaS per FedRAMP")

Questa soluzione usa i servizi di Azure seguenti. Per dettagli dell'architettura di distribuzione, vedere la sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Macchine virtuali di Azure
    - (1) Bastion host (Windows Server 2016 Datacenter)
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
- Cloud di controllo di Azure
- Insieme di credenziali dei servizi di ripristino
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Monitoraggio di Azure (log)

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Bastion host**: il bastion host è l'unico punto di ingresso che offre una connessione sicura agli amministratori per accedere alle risorse distribuite. Il gruppo di sicurezza di rete del bastion host consente le connessioni solo sulla porta TCP 3389 per RDP. I clienti possono anche configurare il bastion host per soddisfare i requisiti di protezione avanzata del sistema aziendale.

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: Questa soluzione distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate all'interno di una rete virtuale. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione.

Vedere la configurazione per i [gruppi di sicurezza di rete](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribuita con questa soluzione. I clienti possono configurare i gruppi di sicurezza di rete modificando il file indicato sopra mediante [questa documentazione](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) come riferimento.

Ognuna delle subnet ha un gruppo di sicurezza di rete dedicato:
- 1 gruppo di sicurezza di rete per il gateway applicazione (LBNSG)
- 1 gruppo di sicurezza di rete per il bastion host (MGTNSG)
- 1 gruppo di sicurezza di rete per i controller di dominio primario e di backup (ADNSG)
- 1 gruppo sicurezza di rete per SQL Server (SQLNSG)
- 1 gruppo di sicurezza di rete per il livello Web (WEBNSG)

**Subnet**: Ogni subnet è associata al rispettivo gruppo di sicurezza di rete.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi attraverso diverse misure di crittografia.

**Archiviazione di Azure**: Per soddisfare i requisiti di crittografia dei dati inattivi, tutti gli account di archiviazione usano la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server è configurato per l'uso di [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), che esegue la crittografia e la decrittografia in tempo reale dei dati e dei file di log per proteggere le informazioni inattive. TDE garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.

I clienti possono anche configurare le misure di sicurezza seguenti per SQL Server:
-   L'[autenticazione e l'autorizzazione di AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati in testo non crittografato.
-   [La maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) può essere creata dopo la distribuzione dell'architettura di riferimento. I clienti dovranno modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

**Crittografia dischi di Azure**: Crittografia dischi di Azure viene usato per crittografare i dischi delle macchine virtuali IaaS Windows. [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud.
- L'autenticazione a un'applicazione Web distribuita dal cliente può essere eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) consente un'accurata gestione degli accessi per Azure. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione, mentre l'accesso alle risorse può essere limitato in base al ruolo utente.
- Un'istanza di Active Directory IaaS distribuita fornisce la gestione delle identità a livello di sistema operativo per le macchine virtuali IaaS distribuite.

### <a name="security"></a>Security
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Azure Key Vault consente di gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS per questa architettura di riferimento.

**Gestione delle patch**: Le macchine virtuali Windows distribuite tramite questo progetto di automazione Azure Security and Compliance Blueprint vengono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione distribuisce anche la soluzione di automazione di Azure tramite la quale è possibile creare distribuzioni di aggiornamento per distribuire patch nei server Windows in base alle esigenze.

**Protezione antimalware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato tenta di installare o eseguire se stesso su macchine virtuali protette.

**Gateway applicazione**: L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con web application firewall (WAF) e il set di regole OWASP abilitati. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità WAF)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0

### <a name="business-continuity"></a>Continuità aziendale

**Disponibilità elevata**: Almeno una macchina virtuale è disponibile durante un evento di manutenzione pianificato o non pianificato, in conformità al Contratto di servizio di Azure con disponibilità garantita del 99,95%. La soluzione distribuisce tutte le macchine virtuali di livello Web e di livello dati in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). I set di disponibilità assicurano che le macchine virtuali vengano distribuite in più cluster hardware isolati per migliorare la disponibilità. Questa soluzione distribuisce anche macchine virtuali SQL Server in un set di disponibilità come il [gruppo di disponibilità AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Il gruppo di disponibilità Always On offre funzionalità di ripristino di emergenza e disponibilità elevata.

**Insieme di credenziali di Servizi di ripristino**: l'[insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ospita i dati di backup e protegge tutte le configurazioni delle macchine virtuali di Azure in questa architettura. Con un insieme di credenziali di Servizi di ripristino i clienti possono ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, consentendo tempi di ripristino più rapidi.

**Cloud di controllo**: il [Cloud di controllo](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) è un tipo di controllo del quorum del cluster di failover in Windows Server 2016 che sfrutta Azure come punto di arbitraggio. Il cloud di controllo, come qualsiasi altro controllo del quorum, ottiene un voto e può partecipare ai calcoli del quorum, ma usa il servizio Archiviazione BLOB di Azure standard disponibile pubblicamente. Ciò consente di evitare il sovraccarico di manutenzione per le macchine virtuali ospitate in un cloud pubblico.

### <a name="logging-and-auditing"></a>Registrazione e controllo

Log di monitoraggio di Azure offre registrazione completa delle attività di sistema e utente, nonché dell'integrità del sistema. Il [monitoraggio di Azure registra](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) soluzione raccoglie e analizza i dati generati dalle risorse di Azure e negli ambienti locali.

- **Log attività:**  i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica:**  I [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono tutti i log generati da ogni risorsa. Questi log includono log eventi del sistema Windows, log di archiviazione di Azure, log di controllo di Azure Key Vault e log degli accessi e del firewall del gateway applicazione.
- **Archiviazione di log**:  Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione. Questi log connettere ai log di monitoraggio di Azure per l'elaborazione, l'archiviazione e i report del dashboard.

Questa architettura include anche l'installazione delle soluzioni di monitoraggio seguenti. Si noti che è responsabilità del cliente configurare le soluzioni per allinearle ai controlli di sicurezza FedRAMP:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione antimalware crea report relativi a malware, minacce e stato di protezione.
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): la dashboard Sicurezza e controllo offre informazioni dettagliate di alto livello sullo stato di sicurezza delle risorse, grazie a metriche relative a domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente ai clienti di gestire gli aggiornamenti di sicurezza del sistema operativo, tra cui lo stato degli aggiornamenti disponibili e il processo per l'installazione di quelli necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello degli agenti che inviano dati operativi.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze, consentendo alle organizzazioni di controllare, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle risorse di Azure dei clienti.

## <a name="threat-model"></a>Modello di minaccia
Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/fedrampWAdfd) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web IaaS per FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "Modello di minaccia dell'applicazione Web IaaS per FedRAMP")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

Il [Progetto di conformità e sicurezza di Azure - Matrice di responsabilità clienti FedRAMP High](https://aka.ms/blueprinthighcrm) elenca tutti i controlli di sicurezza richiesti dalla baseline FedRAMP High. La matrice denota se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [Progetto di conformità e sicurezza di Azure - Matrice di implementazione del controllo di applicazioni Web IaaS per FedRAMP High](https://aka.ms/blueprintwacim) elenca tutti i controlli di sicurezza richiesti dalla baseline di FedRAMP High. La matrice offre informazioni sui controlli previsti dall'architettura dell'applicazione Web IaaS, incluse le descrizioni dettagliate del modo in cui l'implementazione soddisfa i requisiti di ogni controllo previsto.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Questo progetto di automazione Azure Security and Compliance Blueprint è costituito da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire risorse in Azure. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Questa soluzione viene distribuita in Azure per enti pubblici.

#### <a name="quickstart"></a>Guida introduttiva
1. Clonare o scaricare [questo](https://aka.ms/fedrampblueprintrepo) repository GitHub nella workstation locale.

2. Eseguire lo script di PowerShell di predistribuzione: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Fare clic sul pulsante di seguito, accedere al portale di Azure, immettere i parametri del modello di Azure Resource Manager richiesti e quindi fare clic su **Acquista**.

    [![Distribuzione in Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
