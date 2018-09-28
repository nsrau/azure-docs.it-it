---
title: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per FFIEC
description: Progetto per la sicurezza e la conformità di Azure - Applicazione Web PaaS per FFIEC
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: e9aa3939d0fd36ec55d1156d8a5fd38a1cee4279
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985045"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Progetto per la sicurezza e la conformità di Azure: Applicazione Web PaaS per servizi finanziari di FFIEC

## <a name="overview"></a>Panoramica

Questo progetto di automazione di Azure Security and Compliance Blueprint fornisce indicazioni per la distribuzione di una piattaforma come un ambiente del servizio (PaaS) adatto per la raccolta, l'archiviazione e il recupero dei dati finanziari regolamentati da parte del Consiglio di esame istituto finanziario federale (FFIEC). Questa soluzione automatizza la distribuzione e la configurazione delle risorse di Azure per un'architettura di riferimento comune, mostrando i diversi modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità, e costituisce un'architettura di base con cui i clienti possono creare e configurare soluzioni personalizzate in Azure. La soluzione implementa un subset dei requisiti dagli handbook della FFIEC. Per altre informazioni sui requisiti di FFIEC e su questa soluzione, vedere la sezione relativa alla [documentazione sulla conformità](#compliance-documentation).

Questo progetto di automazione Azure Security and Compliance Blueprint distribuisce un'architettura di riferimento per applicazioni Web PaaS con controlli di sicurezza preconfigurati per aiutare i clienti a realizzare la conformità ai requisiti di FFIEC. La soluzione è costituita da modelli di Azure Resource Manager e script di PowerShell che semplificano la distribuzione e la configurazione delle risorse.

Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti di obiettivi di FFIEC. Tenere presente quanto segue:
- Questa architettura offre una soluzione di base per aiutare i clienti a usare Azure in modo conforme a FFIEC.
- I clienti hanno la responsabilità di svolgere una valutazione appropriata della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

Ottenere la conformità di FFIEC richiede che i revisori qualificati certifichino una soluzione del cliente di produzione. I controlli vengono supervisionati dagli esaminatori dalle agenzie del membro della FFIEC, compreso il Consiglio di amministrazione del sistema di riserva federale (FRB), il sistema di assicurazione dei depositi federale (FDIC), la National Credit Union Administration (NCUA), l'ufficio del Comptroller della valuta (OCC), e l'ufficio di protezione finanziaria del consumatore (CFPB). Questi esaminatori certificano che i controlli vengono compilati da assessor che gestiscono l'indipendenza dall'istituzione controllata. I clienti hanno la responsabilità di svolgere valutazioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

Fare clic [qui](https://aka.ms/ffiec-paaswa-repo) per le istruzioni di distribuzione.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura

Questo progetto di automazione Azure Security and Compliance Blueprint distribuisce un'architettura di riferimento per un'applicazione Web PaaS con un back-end del database SQL di Azure. L'applicazione Web è ospitata in un ambiente del servizio app di Azure isolato, vale a dire un ambiente dedicato privato in un data center di Azure. L'ambiente esegue il bilanciamento del carico del traffico per l'applicazione Web tra le macchine virtuali gestite da Azure. Questa architettura include anche i gruppi di sicurezza di rete, un gateway applicazione, DNS di Azure e Load Balancer.

Per operazioni di analisi e di report avanzate, i database SQL di Azure possono essere configurati con indici columnstore. I database SQL di Azure possono essere ridimensionati o disattivati completamente a seconda dell'utilizzo del cliente. Tutto il traffico SQL viene crittografato con il protocollo SSL tramite l'inclusione di certificati autofirmati. Come procedura consigliata, Azure invita all'uso di un'autorità di certificazione attendibile per una sicurezza avanzata.

La soluzione usa gli account di Archiviazione di Azure che i clienti possono configurare per usare la crittografia del servizio di archiviazione e mantenere la riservatezza dei dati inattivi. Azure archivia tre copie di dati all'interno del data center scelto del cliente per garantire la resilienza. L'archiviazione con ridondanza geografica assicura che i dati vengano replicati in un data center secondario a centinaia di chilometri di distanza e archiviati di nuovo come tre copie all'interno di tale data center, impedendo a un evento negativo nel data center principale del cliente di causare una perdita di dati.

Per una sicurezza ottimale, tutte le risorse in questa soluzione vengono gestite come gruppo di risorse tramite Azure Resource Manager. Il controllo degli accessi in base al ruolo di Azure Active Directory viene usato per controllare l'accesso alle chiavi, incluse le risorse distribuite in Azure Key Vault. L'integrità del sistema è monitorata tramite Monitoraggio di Azure. I clienti configurano entrambi i servizi di monitoraggio per acquisire i log e visualizzare l'integrità del sistema in un singolo dashboard, facilmente navigabile.

Il database SQL di Azure viene gestito in genere con SQL Server Management Studio, eseguito in un computer locale configurato per l'accesso al database SQL di Azure tramite una connessione VPN o ExpressRoute sicura.

Application Insights fornisce inoltre funzionalità di gestione e analisi delle prestazioni delle applicazioni in tempo reale tramite Log Analytics. **Microsoft consiglia di configurare una connessione VPN o Azure ExpressRoute per la gestione e l'importazione dei dati nella subnet dell'architettura di riferimento.**

![Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per FFIEC](images/ffiec-paaswa-architecture.png "Diagramma dell'architettura di riferimento dell'applicazione Web PaaS per FFIEC")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

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
- Azure Log Analytics
- Rete virtuale di Azure
    - (1) Rete /16
    - (4) Reti /24
    - Gruppi di sicurezza di rete
- App Web di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) consente ai clienti di usare le risorse incluse nella soluzione come gruppo. I clienti possono distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. I clienti usano un modello per la distribuzione, utilizzabile per ambienti diversi, ad esempio di test, staging e produzione. Resource Manager offre funzionalità di sicurezza, controllo e assegnazione di tag per aiutare i clienti a gestire le risorse dopo la distribuzione.

**Bastion host**: il bastion host è il singolo punto di ingresso che consente agli utenti di accedere alle risorse distribuite in questo ambiente. Il bastion host fornisce una connessione sicura alle risorse distribuite consentendo solo il traffico remoto dagli indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Per consentire il traffico di desktop remoto (RDP), l'origine del traffico deve essere definita nel gruppo di sicurezza di rete.

Questa soluzione crea una macchina virtuale come bastion host aggiunto al dominio con le configurazioni seguenti:
-   [Estensione antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Estensione di Diagnostica di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tramite Azure Key Vault
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) abilitato, in modo che le credenziali e altri segreti vengano eseguiti in un ambiente protetto, isolato dal sistema operativo in esecuzione

**Ambiente del servizio app versione 2**: l'ambiente del servizio app di Azure è una funzionalità del servizio app che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di applicazioni del servizio app su vasta scala. Questa funzionalità di isolamento è necessaria per soddisfare i requisiti di conformità FFIEC.

Gli ambienti del servizio app sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. Questa funzionalità di isolamento consente che l'architettura di riferimento abbia l'isolamento completo dei tenant, rimuovendola dall'ambiente multi-tenant di Azure che impedisce a tali multi-tenant di enumerare le risorse dell'ambiente del servizio app distribuito. I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali. I clienti possono eseguire la "scalabilità automatica" con l'ambiente del servizio app in base alle metriche di caricamento, al budget disponibile o a una pianificazione definita.

L'uso dell'ambiente del servizio app per questa architettura consente le configurazioni seguenti:

- Host all'interno di una rete virtuale di Azure protetta e di regole di sicurezza di rete
- Certificato di bilanciamento del carico interno autofirmato per la comunicazione HTTPS. Come procedura consigliata, Microsoft consiglia l'utilizzo di un'autorità di certificazione attendibile per una sicurezza avanzata.
- [Modalità di bilanciamento del carico interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Disabilitazione di [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Modifica della [crittografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controllo delle [porte di rete del traffico in ingresso](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web application firewall - limitazione dei dati](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Traffico del database SQL di Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) consentito

**App Web di Azure**: [App Web di Azure](https://docs.microsoft.com/azure/app-service/) consente ai clienti di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito, senza dovere gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da GitHub, Azure DevOps o qualsiasi repository Git.

### <a name="virtual-network"></a>Rete virtuale

L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

**Gruppi di sicurezza di rete**: i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contengono elenchi di controllo di accesso che consentono o bloccano il traffico in una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola macchina virtuale. Esistono i seguenti gruppi di sicurezza di rete:

- 1 gruppo di sicurezza di rete per il gateway applicazione
- 1 gruppo di sicurezza di rete per l'ambiente del servizio app
- 1 gruppo di sicurezza di rete per il database SQL di Azure
- 1 gruppo di sicurezza di rete per il bastion host

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

- [Log ed eventi di diagnostica](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) abilitati e archiviati in un account di archiviazione
- Log Analytics connesso ai [log di diagnostica dei gruppi di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subnet**: ogni subnet è associata al gruppo di sicurezza di rete corrispondente

**DNS di Azure**: il sistema DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un servizio o di un sito Web nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) consente ai clienti di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza, velocità effettiva elevata e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

### <a name="data-in-transit"></a>Dati in transito

Per impostazione predefinita, Azure esegue la crittografia di tutte le comunicazioni da e verso i data center di Azure. Tutte le transazioni verso Archiviazione di Azure mediante il portale di Azure hanno luogo tramite HTTPS.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi tramite la crittografia, il controllo del database e altre misure.

**Archiviazione di Azure**: per soddisfare i requisiti dei dati inattivi crittografati, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ciò consente di proteggere e salvaguardare i dati, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da FFIEC.

**Crittografia dischi di Azure**: [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa la funzionalità BitLocker di Windows per eseguire la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

**Database SQL di Azure**: l'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
- Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
- Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
- Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
- Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
- Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- La [maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare. In questo modo è più semplice identificare e ridurre l'accesso ai dati in modo da evitare l'uscita dal database tramite accesso non autorizzato. I clienti sono tenuti a modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

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

**Centro sicurezza di Azure**: con il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di [avvisi di sicurezza predefiniti](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) che vengono attivati in caso di minaccia o di attività sospetta. Le [regole di avviso personalizzate](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un [report di intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report) per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

**Gateway applicazione di Azure**: l'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

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
- **Log attività**: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) includono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- **Log di diagnostica**: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa, ovvero i log eventi del sistema Windows, i log di Archiviazione di Azure, i log di controllo di Key Vault e i log degli accessi e del firewall del gateway applicazione. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

**Log Analytics**: questi log vengono consolidati in [Log Analytics](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report nel dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Operations Management Suite, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Inoltre, il Centro sicurezza di Azure si integra con Log Analytics consentendo ai clienti di usare le query di Log Analytics per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Le seguenti [soluzioni di gestione](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Log Analytics sono incluse come parte di questa architettura:
-   [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di elementi consigliati specifici per l'infrastruttura di server distribuita.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la soluzione Integrità agente segnala il numero di agenti distribuiti e la rispettiva distribuzione geografica, oltre al numero di agenti non reattivi e a quello di agenti che inviano dati operativi.
-   [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): questa soluzione fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

**Automazione di Azure**: la soluzione [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) archivia, esegue e gestisce i runbook. In questa soluzione i runbook consentono di raccogliere log dal database SQL di Azure. La soluzione [Rilevamento modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking) di Automazione consente ai clienti di identificare con facilità le modifiche apportate all'ambiente.

**Monitoraggio di Azure**: [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze, consentendo alle organizzazioni di controllare, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

**Application Insights**:[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/ffiec-paaswa-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'applicazione Web PaaS per FFIEC](images/ffiec-paaswa-threat-model.png "Modello di minaccia dell'applicazione Web PaaS per FFIEC")

## <a name="compliance-documentation"></a>Documentazione sulla conformità

Il [Progetto per la conformità e la sicurezza di Azure - Matrice di responsabilità clienti per FFIEC](https://aka.ms/ffiec-crm) elenca tutti i principi di sicurezza della FFIEC. La matrice descrive in dettaglio se l'implementazione di ogni obiettivo è responsabilità di Microsoft, del cliente o di entrambi.

[Azure Security and Compliance Blueprint – FFIEC PaaS Web Application Implementation Matrix](https://aka.ms/ffiec-paaswa-cim) (Progetto per la sicurezza e la conformità di Azure - Matrice di implementazione dell'applicazione Web PaaS per la FFEIC) fornisce informazioni sui requisiti del servizio sanitario nazionale (NHS) Regno Unito a cui l'architettura dell'applicazione Web PaaS fa riferimento, incluse le descrizioni dettagliate del modo in cui l'implementazione rispetta i requisiti di ogni obiettivo specifico.

## <a name="deploy-this-solution"></a>Distribuire questa soluzione
Questo progetto di automazione Azure Security and Compliance Blueprint è costituito da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire risorse in Azure. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Guida introduttiva
1. Clonare o scaricare [questo](https://aka.ms/ffiec-paaswa-repo) repository GitHub nella workstation locale.

2. Rivedere 0-Setup-AdministrativeAccountAndPermission.md ed eseguire i comandi indicati.

3. Distribuire una soluzione di test con dati di esempio di Contoso o utilizzare un progetto pilota con un ambiente di produzione iniziale.
    - 1A-ContosoWebStoreDemoAzureResources.ps1
        - Questo script distribuisce le risorse di Azure per una dimostrazione di un negozio online mediante dati di esempio di Contoso.
    - 1-DeployAndConfigureAzureResources.ps1
        - Questo script distribuisce le risorse di Azure necessarie per supportare un ambiente di produzione per un'applicazione Web di proprietà del cliente. Questo ambiente deve essere ulteriormente personalizzato dal cliente in base ai requisiti dell'organizzazione.

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute

È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente ai clienti di inviare in modo sicuro le informazioni attraverso un &quot;tunnel&quot; all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La [modalità tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito &quot;così com'è&quot;. Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.
- Alcuni elementi consigliati contenuti in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
