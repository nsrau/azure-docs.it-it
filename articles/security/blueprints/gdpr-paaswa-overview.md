---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per GDPR
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per GDPR
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c51ce44d1f6c2dcacaed09a490e46ad3af1ec9dc
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406688"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per GDPR

## <a name="overview"></a>Panoramica
Il Regolamento generale sulla protezione dei dati, o GDPR, contiene molti requisiti relativi alla raccolta, all'archiviazione e all'uso delle informazioni personali, ad esempio il modo in cui le organizzazioni identificano e proteggono i dati personali, rispettano i requisiti a livello di trasparenza, rilevano e segnalano violazioni ai dati personali ed eseguono la formazione del personale addetto alla privacy e di altri dipendenti. Il GDPR offre ai singoli utenti maggiore controllo sui dati personali e impone molti obblighi nuovi alle organizzazioni che raccolgono, gestiscono o analizzano i dati personali. Il GDPR impone nuove regole per le organizzazioni che offrono beni e servizi a persone nell'Unione europea o che raccolgono e analizzano dati collegati a persone residenti nell'Unione europea. Il GDPR si applica indipendentemente da dove si trova un'organizzazione.

Microsoft ha progettato Azure con misure di sicurezza e criteri di privacy leader di settore per la salvaguardia dei dati sul cloud, incluse le categorie dei dati personali identificate dal GDPR. Le [condizioni contrattuali](http://aka.ms/Online-Services-Terms) Microsoft impongono a Microsoft il rispetto dei requisiti dei responsabili.

Questo progetto per la sicurezza e la conformità di Azure contiene le linee guida per la distribuzione di un ambiente di piattaforma distribuita come servizio (PaaS) adatto a una semplice applicazione Web per Internet. Questa soluzione illustra i modi in cui i clienti possono soddisfare i requisiti specifici di sicurezza e conformità del GDPR e costituisce un punto di partenza per i clienti che vogliono creare e configurare soluzioni di applicazioni Web PaaS personalizzate in Azure. I clienti possono utilizzare questa architettura di riferimento e seguire il [processo in quattro passaggi](https://aka.ms/gdprebook) di Microsoft per ottenere la conformità al GDPR:
1. Individuazione: identificare i tipi di dati personali esistenti e la rispettiva posizione.
2. Gestione: regolamentare la modalità d'uso e di accesso dei dati personali.
3. Protezione: stabilire controlli di sicurezza per evitare, rilevare e rispondere alle vulnerabilità e alle violazioni di dati.
4. Segnalazione: mantenere la documentazione necessaria e gestire le richieste di dati e le notifiche relative alle violazioni.

Questa architettura di riferimento, la guida all'implementazione associata e il modello di minaccia devono essere considerati soluzioni di base che i clienti possono adattare ai propri requisiti specifici e non devono essere usati così come sono in un ambiente di produzione. Tenere presente quanto segue:
- L'architettura fornisce una baseline per aiutare i clienti a distribuire carichi di lavoro in Azure in modo da assicurare la conformità con il GDPR.
- I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

## <a name="architecture-diagram-and-components"></a>Diagramma dell'architettura e componenti
Questa soluzione fornisce un'architettura di riferimento per un'applicazione Web PaaS con un back-end del database SQL di Azure. L'applicazione Web è ospitata in un ambiente del servizio app di Azure isolato, vale a dire un ambiente dedicato privato in un data center di Azure. L'ambiente esegue il bilanciamento del carico del traffico per l'applicazione Web tra le macchine virtuali gestite da Azure. Questa architettura include anche i gruppi di sicurezza di rete, un gateway applicazione, DNS di Azure e Load Balancer. Monitoraggio di Azure offre anche analisi in tempo reale dell'integrità del sistema. **Azure consiglia di configurare una rete VPN o una connessione ExpressRoute per la gestione e l'importazione dei dati in questa subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per GDPR](images/gdpr-paaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per GDPR")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

- Azure Active Directory (AAD)
- Azure Key Vault
- database SQL di Azure
- Gateway applicazione
    - (1) Gateway applicazione WAF abilitato
        - Modalità firewall: prevenzione
        - Set di regole: OWASP 3.0
        - Listener: porta 443
- rete virtuale di Azure
- gruppi di sicurezza di rete
- DNS di Azure
- Archiviazione di Azure
- Monitoraggio di Azure
- Application Insights
- Centro sicurezza di Azure
- Ambiente del servizio app versione 2
- Azure Load Balancer
- App Web di Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architettura di distribuzione
La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) consente ai clienti di usare le risorse incluse nella soluzione come gruppo. I clienti possono distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. I clienti usano un modello per la distribuzione, utilizzabile per ambienti diversi, ad esempio di test, staging e produzione. Resource Manager offre funzionalità di sicurezza, controllo e assegnazione di tag per aiutare i clienti a gestire le risorse dopo la distribuzione.

**Ambiente del servizio app v2**: l'[ambiente del servizio app di Azure](https://docs.microsoft.com/azure/app-service/environment/intro) è una funzionalità del servizio app che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di applicazioni del servizio app su vasta scala.

Gli ambienti del servizio app sono isolati perché possano eseguire solo le applicazioni di un unico cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali.

L'uso di ambienti del servizio app per questa architettura è consentito per i controlli e le configurazioni seguenti:

- Host all'interno di una rete virtuale di Azure protetta e di regole di sicurezza di rete
- Ambiente del servizio app configurato con un certificato ILB autofirmato per la comunicazione HTTPS
- [Modalità di bilanciamento del carico interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modalità 3)
- Disabilitazione di [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Modifica della [crittografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controllo delle [porte di rete del traffico in ingresso](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [WAF: limitazione dei dati](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Traffico del database SQL di Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) consentito

**App Web di Azure**: [App Web di Azure](https://docs.microsoft.com/azure/app-service/) consente ai clienti di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito, senza dovere gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da GitHub, Azure DevOps o qualsiasi repository Git.

### <a name="virtual-network"></a>Rete virtuale
L'architettura definisce una rete virtuale privata con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che consentono o negano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Sono disponibili i gruppi di sicurezza di rete seguenti:
- 1 gruppo di sicurezza di rete per il gateway applicazione
- 1 gruppo di sicurezza di rete per l'ambiente del servizio app
- 1 gruppo di sicurezza di rete per il database SQL di Azure

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
  - [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
  - Log Analytics è connesso alla [diagnostica del gruppo di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnet**: ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

**DNS di Azure**: il sistema DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un servizio o di un sito Web nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) consente ai clienti di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza, velocità effettiva elevata e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

### <a name="data-in-transit"></a>Dati in transito
Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Tutte le transazioni verso Archiviazione di Azure mediante il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere i dati personali, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti dal GDPR.

**Crittografia dischi di Azure**
[Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa la funzionalità BitLocker di Windows per eseguire la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: l'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:
-   L'[autenticazione e l'autorizzazione di AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
-   Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
-   Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption, ovvero TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. TDE garantisce che i dati personali archiviati non siano soggetti ad accesso non autorizzato.
-   Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
-   Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
-   Le [colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati personali sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati in testo non crittografato.
- Le [proprietà estese](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) possono essere usate per interrompere l'elaborazione dei soggetti dei dati perché consentono agli utenti di aggiungere proprietà personalizzate agli oggetti database e di contrassegnare i dati come "sospesi" per supportare la logica dell'applicazione, in modo da impedire l'elaborazione dei dati personali associati.
- La [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) consente agli utenti di definire criteri per limitare l'accesso ai dati per interrompere l'elaborazione.
- La funzionalità [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati personali sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La Maschera dati dinamica può individuare automaticamente dati sensibili potenziali e suggerire le maschere appropriate da applicare, semplificando l'identificazione dei dati personali idonei per la protezione con GDPR e la riduzione dell'accesso, in modo da evitare l'uscita dal database tramite accesso non autorizzato. **Nota: i clienti dovranno modificare le impostazioni della Maschera dati dinamica in modo da adattarle al rispettivo schema del database.**

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati personali nell'ambiente di Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure.
-   L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione al database SQL di Azure. Per altre informazioni, vedere l'articolo su come [proteggere i dati sensibili nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati personali. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni, ad esempio i dati personali.  Gli amministratori possono usare AAD Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti per intraprendere le azioni appropriate per risolverli.

### <a name="security"></a>Sicurezza
**Gestione dei segreti**: la soluzione usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per la gestione di chiavi e segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere i dati personali e l'accesso a tali dati:
- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- A tutti gli utenti e tutte le identità vengono concesse le autorizzazioni minime richieste tramite il controllo degli accessi in base al ruolo.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

**Avvisi di sicurezza**: il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente ai clienti di monitorare il traffico, raccogliere i log e analizzare le origini dati alla ricerca di minacce. Il Centro sicurezza di Azure accede inoltre alla configurazione esistente dei servizi di Azure in modo da fornire consigli su configurazione e servizi per migliorare le condizioni di sicurezza e proteggere i dati personali. Il Centro sicurezza di Azure include un [report di intelligence](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti nelle attività di indagine e reazione alle minacce.

**Gateway applicazione**: l'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con Web Application Firewall (WAF) e il set di regole OWASP abilitati. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità WAF)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- Possibilità di abilitare la [registrazione diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Probe di integrità personalizzati](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="logging-and-auditing"></a>Registrazione e controllo

Monitoraggio di Azure offre una soluzione di registrazione completa delle attività di sistema e degli utenti, nonché dell'integrità del sistema. Raccoglie e analizza i dati generati dalle risorse in Azure e negli ambienti locali.
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) includono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa, ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione.
- **Archiviazione di log**: tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un periodo massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione. Questi log si connettono quindi a Log Analytics di Azure per l'elaborazione, l'archiviazione e la creazione di report nel dashboard.

Questa architettura include anche le soluzioni di monitoraggio seguenti:
-   [Valutazione AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Valutazione antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la soluzione Antimalware crea report relativi a malware, minacce e stato della protezione.
-   [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la soluzione Automazione di Azure archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log da Application Insights e dal database SQL di Azure.
-   [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): il dashboard Sicurezza e controllo offre informazioni dettagliate sullo stato di sicurezza delle risorse, grazie a metriche relative a domini di sicurezza, problemi rilevanti, rilevamenti, intelligence per le minacce e query comuni per la sicurezza.
-   [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
-   [Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la soluzione Gestione aggiornamenti consente ai clienti di gestire gli aggiornamenti della sicurezza del sistema operativo, offrendo anche lo stato degli aggiornamenti disponibili e un processo per l'installazione di quelli necessari.
-   [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello di agenti che inviano dati operativi.
-   [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
-   [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking): la soluzione Rilevamento modifiche consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze, consentendo alle organizzazioni di controllare, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle risorse di Azure dei clienti.

**Application Insights**
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/gdprPaaSdfd) o è riportato più avanti. Questo modello può aiutare i clienti a comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web PaaS per GDPR](images/gdpr-paaswa-threat-model.png?raw=true "Modello di minaccia dell'applicazione Web PaaS per GDPR")

## <a name="compliance-documentation"></a>Documentazione sulla conformità
In [Azure Security and Compliance Blueprint – GDPR Customer Responsibility Matrix](https://aka.ms/gdprCRM) (Progetto per la sicurezza e la conformità di Azure - Matrice di responsabilità per i clienti per GDPR) sono elencate le responsabilità di controller e responsabili per tutti gli articoli relativi al GDPR. Si noti che per i servizi di Azure un cliente ha in genere il ruolo di titolare, mentre Microsoft ha il ruolo di responsabile.

[Azure Security and Compliance Blueprint - GDPR PaaS Web Application Implementation Matrix](https://aka.ms/gdprPaaSWeb) (Progetto per la sicurezza e la conformità di Azure - Matrice di implementazione dell'applicazione Web PaaS per GDPR) fornisce informazioni sugli articoli del GDPR a cui l'architettura dell'applicazione Web PaaS fa riferimento, incluse descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni articolo specifico.


## <a name="guidance-and-recommendations"></a>Indicazioni e consigli
### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

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
