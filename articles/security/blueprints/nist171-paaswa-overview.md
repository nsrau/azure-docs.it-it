---
title: 'Modelli Blueprint per sicurezza e conformità di Azure: Applicazione Web PaaS per NIST SP 800-171'
description: 'Modelli Blueprint per sicurezza e conformità di Azure: Applicazione Web PaaS NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 4d635d04c5207b3668bd179cec4f21e26dd59452
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946690"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Modelli Blueprint per sicurezza e conformità di Azure: Applicazione Web PaaS per NIST Special Publication 800-171

## <a name="overview"></a>Panoramica
Il documento [NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornisce indicazioni per la protezione di informazioni non classificate controllate (CUI, Controlled Unclassified Information) presenti in organizzazioni e sistemi informatici non federali. Il documento NIST SP 800-171 stabilisce 14 famiglie di requisiti di sicurezza per proteggere la riservatezza delle informazioni non classificate controllate.

Questo progetto per la sicurezza e la conformità di Azure fornisce indicazioni per aiutare i clienti a distribuire un'applicazione PaaS in Azure che implementa un subset di controlli NIST SP 800-171. Questa soluzione illustra i modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità. I clienti possono anche usarla come base per creare e configurare la propria applicazione Web in Azure.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici. Non devono essere usati così come sono in un ambiente di produzione. La distribuzione di questa architettura senza apportare modifiche è insufficiente a soddisfare completamente i requisiti di NIST SP 800-171. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata che usa questa architettura. I requisiti possono variare in base alle specifiche di implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura

Questo progetto di sicurezza e conformità di Azure offre un'architettura di riferimento per un'applicazione Web PaaS con un back-end del database SQL di Azure. L'applicazione Web è ospitata in un ambiente del servizio app isolato, vale a dire un ambiente dedicato privato in un data center di Azure. L'ambiente esegue il bilanciamento del carico del traffico per l'applicazione Web tra le macchine virtuali gestite da Azure. Questa architettura include anche i gruppi di sicurezza di rete, un gateway applicazione di Azure, DNS di Azure e Load Balancer.

Per operazioni di analisi e di report avanzate, i database SQL di Azure possono essere configurati con indici columnstore. I database SQL di Azure possono essere ridimensionati o disattivati completamente a seconda dell'utilizzo del cliente. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione e mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno del data center scelto del cliente per garantire la resilienza. L'archiviazione con ridondanza geografica assicura che i dati vengano replicati in un data center secondario a centinaia di chilometri di distanza e archiviati di nuovo come tre copie all'interno di tale data center, impedendo a un evento negativo nel data center principale del cliente di generare una perdita di dati.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory (Azure AD) viene usato per controllare l'accesso alle risorse distribuite. Queste risorse includono le chiavi dei clienti in Azure Key Vault. L'integrità del sistema è monitorata tramite Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log. L'integrità del sistema viene visualizzata in un unico dashboard facile da usare.

Il database SQL di Azure è in genere gestito tramite SQL Server Management Studio. Viene eseguito da un computer locale configurato per accedere al database SQL tramite una VPN sicura o una connessione Azure ExpressRoute.

Application Insights fornisce funzionalità di analisi e gestione delle prestazioni delle applicazioni in tempo reale tramite *i log di monitoraggio di Azure Microsoft consiglia di configurare una connessione VPN o ExpressRoute per la gestione e l'importazione di dati nell'architettura di riferimento subnet.*

![Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per NIST SP 800-171](images/nist171-paaswa-architecture.png "Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per NIST SP 800-171")

Questa soluzione usa i servizi di Azure seguenti. Per altre informazioni, vedere la sezione relativa all'[architettura di distribuzione](#deployment-architecture).

- Macchine virtuali di Azure
    - (1) Gestione/bastion (Windows Server 2016 Datacenter)
- Rete virtuale di Azure
    - (1) Rete /16
    - (4) Reti /24
    - (4) Gruppi di sicurezza di rete
- Gateway applicazione di Azure
    - Web application firewall
        - Modalità firewall: prevenzione
        - Set di regole: OWASP
        - Porta listener: 443
- Application Insights
- Azure Active Directory
- Ambiente del servizio app versione 2
- Automazione di Azure
- DNS di Azure
- Azure Key Vault
- Azure Load Balancer
- Monitoraggio di Azure (log)
- Azure Resource Manager
- Centro sicurezza di Azure
- database SQL di Azure
- Archiviazione di Azure
- Automazione di Azure
- App Web di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Azure Resource Manager**: [Gestione risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) possono essere usati dai clienti per lavorare con le risorse nella soluzione come gruppo. I clienti possono distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. I clienti usano un modello per la distribuzione adatto per ambienti diversi, ad esempio di test, staging e produzione. Resource Manager offre funzionalità di sicurezza, controllo e assegnazione di tag per aiutare i clienti a gestire le risorse dopo la distribuzione.

**Bastion host**: L'host Bastion è il singolo punto di ingresso che gli utenti possono usare per accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto, l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Estensione di Diagnostica di Azure](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Crittografia dischi di Azure](../azure-security-disk-encryption-overview.md) tramite Key Vault.
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione.

**App Web**: [App Web](https://docs.microsoft.com/azure/app-service/) è una funzionalità di servizio app Azure. che consente ai clienti di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito, senza dovere gestire l'infrastruttura. Offre scalabilità automatica e disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da GitHub, Azure DevOps o qualsiasi repository Git.

**Ambiente del servizio app**: [Ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro) è una funzionalità del servizio app. che consente di usare un ambiente completamente isolato e dedicato per l'esecuzione sicura di applicazioni del servizio app su vasta scala.

L'ambiente del servizio app è isolato in modo che sia in grado di eseguire una singola applicazione ed è sempre distribuito in una rete virtuale. Questa funzionalità di isolamento consente all'architettura di riferimento l'isolamento completo del tenant, rimuovendolo dall'ambiente multi-tenant di Azure. I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali. I clienti possono eseguire la "scalabilità automatica" con l'ambiente del servizio app in base alle metriche di caricamento, al budget disponibile o a una pianificazione definita.

L'ambiente del servizio app per questa architettura consente di usare i controlli e le configurazioni seguenti:

- Host in una rete virtuale di Azure protetta con regole di sicurezza di rete
- Certificato di bilanciamento del carico interno autofirmato per la comunicazione HTTPS. Come procedura consigliata, Microsoft consiglia l'utilizzo di un'autorità di certificazione attendibile per una sicurezza avanzata.
- [Modalità di bilanciamento del carico interno](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (modalità 3)
- Disabilitazione di [TLS 1.0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Modifica della [crittografia TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Controllo delle [porte di rete del traffico in ingresso](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Web application firewall - limitazione dei dati](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Traffico del database SQL di Azure](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) consentito

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: [Gruppi](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contengono elenchi di controllo di accesso che consentono o negano il traffico all'interno di una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Sono disponibili i gruppi di sicurezza di rete seguenti:
- Un gruppo di sicurezza di rete per il gateway applicazione
- Un gruppo di sicurezza di rete per l'ambiente del servizio app
- Un gruppo di sicurezza di rete per il database SQL
- Un gruppo di sicurezza di rete per bastion host

Per ogni gruppo di sicurezza di rete sono aperti protocolli e porte specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione.
  - I log di monitoraggio di Azure sono connessi alla [diagnostica di NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subnet**: Ogni subnet è associata al rispettivo gruppo di sicurezza di rete.

**DNS di Azure**: Il Domain Name System (DNS) è responsabile della conversione (o risoluzione) del nome di un sito Web o di un nome di servizio nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

**Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) possibile utilizzare i clienti per ridimensionare le applicazioni e creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita. Tale servizio offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Tutte le transazioni verso Archiviazione di Azure mediante il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, tutte le [risorse di archiviazione](https://azure.microsoft.com/services/storage/) usano la [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md). Questa funzionalità consente di proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da NIST SP 800-171.

**Crittografia dischi di Azure**: [Crittografia dischi](../azure-security-disk-encryption-overview.md) usa la funzionalità BitLocker di Windows per eseguire la crittografia del volume per i dischi dati. La soluzione si integra con Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: L'istanza di database SQL usa le seguenti misure di sicurezza del database:
-   L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](../../sql-database/sql-database-auditing.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Il database SQL di Azure è configurato per usare [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) che esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni per proteggere i dati inattivi. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   [Rilevamento delle minacce SQL](../../sql-database/sql-database-threat-detection.md) consente il rilevamento e la risposta alle minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- [Maschera dati dinamica](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. Può individuare automaticamente dati sensibili potenziali e può suggerire le maschere appropriate da applicare. Maschera dati dinamica consente di ridurre l'accesso in modo che i dati sensibili non escano dal database tramite accesso non autorizzato. *I clienti sono tenuti a modificare le impostazioni in modo da adattarle al proprio schema del database.*

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati nell'ambiente di Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure AD, inclusi quelli che accedono al database SQL.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere l'articolo che illustra come [integrare applicazioni con Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Anche la crittografia delle colonne del database usa Azure AD per autenticare l'applicazione nel database SQL di Azure. Per altre informazioni, vedere la procedura per [proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) può essere usato dagli amministratori per definire le autorizzazioni di accesso con granularità fine. In questo modo è possibile concedere agli utenti solo il livello di accesso necessario per lavorare. Invece di concedere a ogni utente l'accesso senza limiti alle risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso alle risorse e ai dati. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- I clienti possono usare [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) per ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni. Gli amministratori possono usare Azure AD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il relativo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e JIT quando necessario.
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

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati di configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi diretti agli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), che vengono attivati in caso di minaccia o di attività sospetta. I clienti possono usare le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) per definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza dispone avvisi di sicurezza ed eventi imprevisti in ordine di priorità e rende più semplice per i clienti individuare e risolvere potenziali problemi di sicurezza. Per ogni minaccia rilevata viene generato un [report di intelligence sulle minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report). I team di risposta agli eventi imprevisti possono usare i report quando analizzano e correggono le minacce.

**Gateway applicazione di Azure**: L'architettura riduce il rischio di vulnerabilità della sicurezza usando un gateway applicazione con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Abilitazione di [Offload SSL](../../application-gateway/create-ssl-portal.md).
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Web application firewall](../../application-gateway/waf-overview.md) (modalità di prevenzione).
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0.
- Abilitazione della [registrazione diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Probe di integrità personalizzati](../../application-gateway/quick-create-portal.md).
- [Centro sicurezza](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza mette a disposizione anche un sistema di reputazione.

### <a name="logging-and-auditing"></a>Registrazione e controllo

I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
- **Log attività**: i [log attività](../../azure-monitor/platform/activity-logs-overview.md) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](../../azure-monitor/platform/diagnostic-logs-overview.md) includono tutti i log generati da ogni risorsa. ovvero i registri di sistema per gli eventi Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. Gli utenti possono configurare il periodo di conservazione, fino a 730 giorni, per soddisfare requisiti specifici.

**Log di Monitoraggio di Azure**: I log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e il reporting del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per ogni tipo di dati all'interno di aree di lavoro di Log Analytics. In questo modo tutti i dati possono essere analizzati insieme, indipendentemente dall'origine. Il Centro sicurezza si integra con i log di monitoraggio di Azure. I clienti possono usare le query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](../../monitoring/monitoring-solutions.md) di Azure seguenti:
-   [Valutazione Active Directory](../../azure-monitor/insights/ad-assessment.md): la soluzione Controllo integrità Active Directory Domain Services valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Valutazione SQL](../../azure-monitor/insights/sql-assessment.md): la soluzione Controllo integrità SQL consente di valuta i rischi e l'integrità degli ambienti server a intervalli regolari. e offre ai clienti un elenco con priorità di elementi consigliati specifici per l'infrastruttura distribuita dei server.
- [Integrità agente](../../monitoring/monitoring-solution-agenthealth.md): la soluzione Integrità agente indica quanti agenti vengono distribuiti e la relativa distribuzione geografica. Indica anche quanti agenti non rispondono e il numero di agenti che inviano dati operativi.
-   [Analisi log attività](../../azure-monitor/platform/collect-activity-logs.md): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: [Automazione](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. I clienti possono usare la soluzione [Rilevamento modifiche](../../automation/change-tracking.md) di Automazione per identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, gestire la sicurezza e identificare le tendenze. Le organizzazioni possono usarlo per effettuare controlli, creare avvisi e archiviare i dati. Possono inoltre tenere traccia delle chiamate API nelle risorse di Azure.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva le anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Application Insight include strumenti di analisi avanzati che consentono ai clienti di diagnosticare i problemi e di conoscere il modo in cui l'app viene usata dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/nist171-paaswa-tm) o è riportato qui. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando apportano modifiche.

![Modello di minaccia dell'applicazione Web PaaS per NIST SP 800-171](images/nist171-paaswa-threat-model.png "Modello di minaccia dell'applicazione Web PaaS per NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per NIST SP 800-171](https://aka.ms/nist171-crm) elenca tutti i controlli di sicurezza richiesti da NIST SP 800-171. La matrice descrive in dettaglio se l'implementazione di ogni controllo è responsabilità di Microsoft, del cliente o di entrambi.

Il [progetto di sicurezza e conformità di Azure - NIST SP 800-171 - Matrice di implementazione del controllo delle applicazioni Web PaaS](https://aka.ms/nist171-paaswa-cim) fornisce informazioni sui controlli di NIST SP 800-171 a cui l'architettura delle applicazioni Web PaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni controllo specifico.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico all'interno del tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Le connessioni ExpressRoute si connettono direttamente al provider di telecomunicazioni del cliente. Di conseguenza, i dati non vengono trasmessi via Internet e non sono esposti alla rete. Tali connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle connessioni tipiche.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
