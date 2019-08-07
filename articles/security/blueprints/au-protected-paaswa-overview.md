---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per Australia PROTECTED
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per Australia PROTECTED
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 294716052869dac03db42feea9ade15d610551e6
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781117"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per Australia PROTECTED

## <a name="overview"></a>Panoramica

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per la distribuzione di una piattaforma come ambiente di servizio (PaaS) adatto per la raccolta, l'archiviazione e il recupero dei dati AU-PROTECTED, in conformità agli obiettivi definiti nell'Information Security Manual (ISM) del governo australiano pubblicato dall'Australian Signals Directorate (ASD). Questo progetto presenta un'architettura di riferimento comune e illustra come gestire correttamente di dati sensibili degli enti pubblici in un ambiente sicuro, conforme e a più livelli.

Questa architettura di riferimento, la guida all'implementazione e il modello di minaccia offrono una base a cui i clienti possono fare riferimento per intraprendere i processi di pianificazione e di accreditamento del sistema, nonché distribuire più facilmente i carichi di lavoro in Azure in conformità all'ASD. I clienti possono scegliere di implementare un Gateway VPN di Azure o ExpressRoute per usare servizi federati e integrare le risorse locali con quelle di Azure. I clienti devono valutare le implicazioni di sicurezza che comporta l'uso delle risorse locali. Altre attività di configurazione sono necessarie per soddisfare tutti i requisiti, poiché possono variare in base alle specifiche dell'implementazione di ogni cliente.

Per la conformità all'ASD è necessario che il sistema sia controllato da un esperto di sicurezza delle informazioni accreditato. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma dell'architettura e componenti
Questa soluzione fornisce un'architettura di riferimento per un'applicazione Web PaaS con un back-end del database SQL di Azure. L'applicazione Web è ospitata in un ambiente del servizio app di Azure isolato, vale a dire un ambiente dedicato privato in un data center di Azure. L'ambiente esegue il bilanciamento del carico del traffico per l'applicazione Web tra le macchine virtuali gestite da Azure. Tutte le connessioni dell'applicazione Web richiedono TLS con versione minima 1.2. Questa architettura include anche i gruppi di sicurezza di rete, un gateway applicazione, DNS di Azure e Load Balancer.

L'architettura può offrire un ambiente ibrido sicuro che estende una rete locale in Azure, consentendo l'accesso sicuro ai carichi di lavoro basati sul Web da parte di utenti aziendali di una LAN privata di un'organizzazione o da Internet. Per le soluzioni in locale, il cliente è responsabile di tutti gli aspetti correlati alla sicurezza, alle operazioni e alla conformità.

Le risorse di Azure incluse in questa soluzione possono connettersi a una rete locale o a una struttura di condivisione di data center (ad esempio CDC a Canberra) tramite una VPN IPSec con un Gateway VPN e tramite ExpressRoute. La decisione di usare una VPN deve essere presa tenendo conto della classificazione dei dati trasmessi e del percorso di rete. I clienti che eseguono carichi di lavoro critici su larga scala con requisiti per Big Data dovrebbero prendere in considerazione un'architettura di rete ibrida con ExpressRoute per la connettività di rete privata ai servizi di Azure. Fare riferimento alla sezione [Indicazioni e consigli](#guidance-and-recommendations) per informazioni più dettagliate sui meccanismi di connessione ad Azure.

La federazione con Azure Active Directory dovrebbe essere usata per consentire agli utenti di eseguire l'autenticazione con credenziali locali e accedere a tutte le risorse nel cloud tramite un'infrastruttura locale di Active Directory Federation Services. Questi servizi possono offrire funzionalità semplificate e protette di federazione delle identità e Web Single Sign-On per questo ambiente ibrido. Fare riferimento alla sezione [Indicazioni e consigli](#guidance-and-recommendations) per informazioni più dettagliate sulla configurazione di Azure Active Directory.

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione e mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno dell'area selezionata di un cliente per la resilienza. Le aree di Azure vengono distribuite in coppie di aree di resilienza e l'archiviazione con ridondanza geografica assicura che i dati vengano replicati anche nella seconda area con tre copie. Ciò impedisce che un evento negativo nell'area in cui si trovano i dati principali del cliente comporti la perdita di dati.

Per una sicurezza ottimale, tutte le risorse di Azure in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory viene usato per controllare l'accesso alle chiavi e alle risorse distribuite in Azure Key Vault. L'integrità del sistema è monitorata tramite il Centro sicurezza di Azure e Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile. Il gateway applicazione di Azure viene configurato come firewall in modalità di prevenzione e non consente il traffico non TLS v1.2 o versioni successive. La soluzione usa l'ambiente del servizio app v2 di Azure per isolare il livello Web in un ambiente multi-tenant.

![Architettura di riferimento dell'applicazione Web PaaS per AU-PROTECTED](images/au-protected-paaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per AU-PROTECTED")

Questa soluzione usa i servizi di Azure seguenti. Altri dettagli sono disponibili nella sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Gateway applicazione
    - Web application firewall
        - Modalità firewall: prevenzione
        - Set di regole: OWASP
        - Porta listener: 443
- Application Insights
- Azure Active Directory
- Ambiente del servizio app v2 di Azure
- Automazione di Azure
- DNS di Azure
- Azure Key Vault
- Azure Load Balancer
- Monitoraggio di Azure
- Azure Resource Manager
- Centro sicurezza di Azure
- database SQL di Azure
- Archiviazione di Azure
- Log di Monitoraggio di Azure
- Rete virtuale di Azure
    - (1) Rete /16
    - (4) Reti /24
    - Gruppi di sicurezza di rete
- Gruppi di sicurezza di rete
- Insieme di credenziali dei servizi di ripristino
- App Web di Azure

Questo progetto contiene servizi di Azure che non sono stati certificati per l'uso a livello di dati classificati come Protected dall'Australian Cyber Security Centre (ACSC). Microsoft consiglia ai clienti di rivedere i report di sicurezza e controllo pubblicati relativi a questi servizi di Azure e usare il rispettivo framework di gestione dei rischi per determinare se un servizio di Azure è adatto per l'accreditamento interno e l'uso a livello di dati classificati come Protected.

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) consente ai clienti di usare le risorse incluse nella soluzione come gruppo. I clienti possono distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. I clienti usano un modello per la distribuzione, utilizzabile per ambienti diversi, ad esempio di test, staging e produzione. Resource Manager offre funzionalità di sicurezza, controllo e assegnazione di tag per aiutare i clienti a gestire le risorse dopo la distribuzione.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto (RDP), l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Azure Key Vault
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso

**Ambiente del servizio app di Azure versione 2**: L'[ambiente del servizio app di Azure](https://docs.microsoft.com/azure/app-service/environment/intro) è una funzionalità del servizio app che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di applicazioni del servizio app su vasta scala.

Gli ambienti del servizio app sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali.

L'uso degli ambienti del servizio app per questa architettura consente i controlli e le configurazioni seguenti:

- Ambienti del servizio app configurati per l'uso del piano di servizio Isolated
    - Configurare diversi ambienti del servizio app per le applicazioni a diversi livelli di classificazione
- Host all'interno di una rete virtuale di Azure protetta e di regole di sicurezza di rete
- Ambienti del servizio app configurati con un certificato di bilanciamento del carico interno autofirmato per le comunicazioni HTTPS. Come procedura consigliata, Microsoft consiglia l'utilizzo di un'autorità di certificazione attendibile per una sicurezza avanzata.
- [Modalità di bilanciamento del carico interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modalità 3)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Modifica della [crittografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controllo delle [porte di rete del traffico in ingresso](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web application firewall - limitazione dei dati](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Traffico del database SQL di Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) consentito

**App Web di Azure**: [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/) consente di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito senza gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da GitHub, Azure DevOps Services o qualsiasi repository Git.

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che autorizzano o bloccano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola macchina virtuale. Esistono i seguenti gruppi di sicurezza di rete:
- 1 gruppo di sicurezza di rete per il gateway applicazione
- 1 gruppo di sicurezza di rete per l'ambiente del servizio app
- 1 gruppo di sicurezza di rete per il database SQL di Azure
- 1 gruppo di sicurezza di rete per il bastion host

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
  - I log di monitoraggio di Azure sono connessi alla [diagnostica del gruppo di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnet**: ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

**DNS di Azure**: Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) consente ai clienti di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza, velocità effettiva elevata e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. 

Per i dati classificati come Protected, in transito da reti di proprietà dei clienti, l'architettura di Azure usa Internet o ExpressRoute con un Gateway VPN configurato con IPSec.

Inoltre, tutte le transazioni in Azure tramite il portale di gestione di Azure si verificano tramite HTTPS utilizzando TLS v 1.2.

### <a name="data-at-rest"></a>Dati inattivi
L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa sempre la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti dall'ISM del governo australiano.

**Crittografia dischi di Azure**: [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) si avvale della funzionalità BitLocker di Windows per abilitare la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:
-   L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali. Il Rilevamento minacce SLQ integra gli avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), includendo così i dettagli delle attività sospette e le azioni consigliate per individuare e ridurre la minaccia.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- La [maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare. In questo modo è più semplice ridurre l'accesso ai dati sensibili ed evitare così l'uscita dal database tramite accesso non autorizzato. I clienti dovranno modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

### <a name="identity-management"></a>Gestione delle identità
I clienti possono usare Active Directory Federation Services in locale per attuare la federazione con [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integra le directory locali con Azure Active Directory. Tutti gli utenti della soluzione devono disporre di account di Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure. Con l'accesso alla federazione, gli utenti possono accedere ad Azure Active Directory ed eseguire l'autenticazione alle risorse di Azure usando le credenziali locali.

Inoltre, le funzionalità di Azure Active Directory seguenti sono utili per gestire l'accesso ai dati nell'ambiente di Azure:
- L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Inoltre, la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione nel database SQL di Azure. Per altre informazioni, vedere l'articolo su come [proteggere i dati sensibili nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.

**Azure Multi-Factor Authentication**: Per proteggere le identità è necessario implementare l'autenticazione a più fattori. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) è una soluzione semplice da usare, scalabile e affidabile che offre un secondo metodo di autenticazione per proteggere gli utenti. Azure Multi-Factor Authentication sfrutta le potenzialità del cloud e si integra con Active Directory locale e con le app personalizzate. Questa protezione viene estesa agli scenari di importanza strategica con volumi elevati.

### <a name="security"></a>Security
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere i dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) che vengono attivati in caso di minaccia o di attività sospetta. Le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un [report di intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

**Gateway applicazione**: L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con un web application firewall e il set di regole OWASP abilitati. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità di prevenzione)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- Possibilità di abilitare la [registrazione diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Probe di integrità personalizzati](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

**Log di Monitoraggio di Azure**: Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per ogni tipo di dati, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Il Centro sicurezza di Azure si integra inoltre con i log di monitoraggio di Azure, consentendo ai clienti di usare query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Azure seguenti:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello degli agenti che inviano dati operativi.
-   [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. La soluzione [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking) di Automazione consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, implementare la sicurezza e identificare le tendenze, permettendo alle organizzazioni di eseguire verifiche, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

Azure Network Watcher: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornisce gli strumenti per il monitoraggio, la diagnostica, la visualizzazione delle metriche e l'abilitazione o la disabilitazione dei log per le risorse in una rete virtuale di Azure.  Le entità del Commonwealth devono implementare i log dei flussi di Network Watcher per i gruppi di sicurezza di rete e le macchine virtuali. Questi log devono essere archiviati in un account di archiviazione dedicato in cui vengono archiviati solo i log di sicurezza e l'accesso all'account di archiviazione deve essere protetto con controlli degli accessi in base al ruolo.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/au-protected-paaswa-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web PaaS per AU-PROTECTED](images/au-protected-paaswa-threat-model.png?raw=true "Diagramma del modello di minaccia dell'applicazione Web PaaS per AU-PROTECTED")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
Questa documentazione sulla conformità è stata realizzata da Microsoft sulla base delle piattaforme e dei servizi offerti da Microsoft. A causa dell'ampia gamma di distribuzioni dei clienti, questa documentazione presenta un approccio generalizzato per una soluzione ospitata esclusivamente nell'ambiente di Azure. I clienti possono identificare e usare prodotti e servizi alternativi in base ai propri ambienti operativi e risultati aziendali. I clienti che scelgono di usare risorse locali devono gestire la sicurezza e le operazioni per tali risorse. La soluzione documentata può essere personalizzata da parte dei clienti per soddisfare specifici requisiti della sicurezza e dell'ambiente locale.

In [Azure Security and Compliance Blueprint - AU-PROTECTED Customer Responsibility Matrix](https://aka.ms/au-protected-crm) (Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per AU-PROTECTED) sono elencati tutti i controlli di sicurezza richiesti da AU-Prot. La matrice descrive in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

In [Azure Security and Compliance Blueprint – AU-PROTECTED PaaS Web Application Implementation Matrix](https://aka.ms/au-protected-paaswa-cim) (Progetto per la conformità e la sicurezza di Azure - Matrice di implementazione delle applicazioni Web PaaS per AU-PROTECTED) vengono fornite informazioni sui requisiti di AU-PROTECTED a cui l'architettura delle applicazioni Web PaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute

Per le informazioni classificate è necessario configurare un tunnel VPN IPSec sicuro per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web IaaS. La configurazione corretta di una connessione VPN IPSec consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN IPSec sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione può essere eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. 

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'opzione di connessione privata. Azure ExpressRoute è un collegamento dedicato tra Azure e una posizione locale o un provider di hosting di Exchange ed è considerato una rete privata. Poiché non sfruttano la rete Internet, le connessioni ExpressRoute offrono maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure. 

Per proteggere i dati classificati, tramite Internet o Azure ExpressRoute, i clienti devono configurare la VPN IPSec applicando le impostazioni seguenti:

•   L'iniziatore VPN dei clienti deve essere configurato per una durata dell'associazione di sicurezza non superiore a 14.400 secondi.
•   L'iniziatore VPN dei clienti deve disabilitare la modalità aggressiva IKEv1.
•   L'iniziatore VPN dei clienti deve configurare Perfect Forward Secrecy.
•   L'iniziatore VPN dei clienti deve configurare l'uso di HMAC-SHA256 o superiore.

Le opzioni di configurazione per i dispositivi VPN e i parametri IPSec /IKE sono [illustrate in questo articolo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="azure-active-directory-setup"></a>Configurazione di Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è essenziale per la gestione della distribuzione e per il provisioning dell'accesso al personale che interagisce con l'ambiente. Un'istanza esistente di Windows Server Active Directory può essere integrata con Azure Active Directory con [quattro clic](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Inoltre, [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) consente ai clienti di configurare la federazione con [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) in locale e Azure Active Directory. Grazie all'accesso federato, i clienti possono consentire agli utenti di accedere ai servizi basati su Azure Active Directory con le proprie password locali e senza dover immettere di nuovo le password quando usano la rete aziendale. Usando l'opzione di federazione con Active Directory Federation Services, è possibile distribuire una nuova installazione di Active Directory Federation Services oppure specificare un'installazione esistente in una farm di Windows Server 2012 R2.

Per impedire la sincronizzazione dei dati classificati con Azure Active Directory, i clienti possono limitare gli attributi che vengono replicati in Azure Active Directory applicando le impostazioni seguenti in Azure Active Directory Connect:

- [Abilitare il filtraggio](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Disabilitare la sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Disabilitare il writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Disabilitare il writeback dei dispositivi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Mantenere le impostazioni predefinite per [impedire eliminazioni accidentali](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) e l'[aggiornamento automatico](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
