---
title: Piano per l'elaborazione dei pagamenti per ambienti conformi allo standard PCI DSS
description: Requisito PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Progetto per l'automazione di Azure: elaborazione dei pagamenti per ambienti conformi allo standard PCI DSS

## <a name="overview"></a>Panoramica

Il progetto per l'elaborazione dei pagamenti per ambienti conformi allo standard PCI DSS offre indicazioni per la distribuzione di un ambiente PaaS (Platform-as-a-Service) conforme allo standard PCI DSS adatto per la gestione di dati sensibili relativi a carte di pagamento. Il piano, che presenta un'architettura di riferimento comune, è progettato per semplificare l'adozione di Microsoft Azure. Questo progetto illustra una soluzione end-to-end in grado di soddisfare le esigenze delle organizzazioni alla ricerca di un approccio basato sul cloud per ridurre il carico di lavoro e il costo della distribuzione.

Questo progetto è ideato per soddisfare i requisiti rigorosi dello standard PCI DSS (Payment Card Industry Data Security Standard) 3.2 per la raccolta, l'archiviazione e il recupero dei dati relativi a carte di pagamento. Illustra la gestione corretta dei dati delle carte di credito (numero, scadenza e dati di verifica) in un ambiente multilivello protetto e conforme, distribuito come soluzione end-to-end PaaS basata su Azure. Per altre informazioni sui requisiti dello standard PCI DSS 3.2 e su questa soluzione, vedere [Requisiti PCI DSS - Panoramica generale](pci-dss-requirements-overview.md).

Questo progetto è destinato all'uso come base per la comprensione dei requisiti specifici da parte dei clienti e non deve essere usato così com'è in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti di una soluzione conforme allo standard PCI DSS per una soluzione personalizzata. Tenere presente quanto segue:
- Questo progetto offre una soluzione iniziale per aiutare i clienti a usare Microsoft Azure in modo conforme allo standard PCI DSS.
- Per ottenere la conformità allo standard PCI DSS è necessaria la certificazione di una soluzione di produzione del cliente da parte di un Qualified Security Assessor (QSA) accreditato.
- I clienti hanno la responsabilità di svolgere revisioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura di base, in quanto i requisiti possono variare a seconda delle specifiche e della geografia dell'implementazione di ogni cliente.  

Per una rapida panoramica del funzionamento di questa soluzione, guardare questo [video](https://aka.ms/pciblueprintvideo), che ne spiega e descrive la distribuzione.

## <a name="solution-components"></a>Componenti della soluzione

L'architettura di base è costituita dai componenti seguenti:

- **Diagramma dell'architettura**. Il diagramma illustra l'architettura di riferimento usata per la soluzione Contoso Webstore.
- **Modelli di distribuzione**. In questa distribuzione vengono usati [modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) per distribuire automaticamente i componenti dell'architettura in Microsoft Azure specificando i parametri di configurazione durante l'installazione.
- **Script di distribuzione automatizzata**. Questi script agevolano la distribuzione della soluzione end-to-end. Gli script sono costituiti da:
    - Uno script di installazione dei moduli e degli [amministratori globali](/azure/active-directory/active-directory-assign-admin-roles-azure-portal), usato per installare i moduli PowerShell e verificare che questi ultimi e i ruoli di amministratore globale siano configurati correttamente.
    - Uno script PowerShell di installazione viene usato per distribuire la soluzione end-to-end, fornita tramite un file con estensione zip e un file con estensione bacpac contenenti un'applicazione Web demo precompilata con il contenuto del [database SQL di esempio](https://github.com/Microsoft/azure-sql-security-sample) . Il codice sorgente per questa soluzione è disponibile per la revisione in [Payment Processing Blueprint code repository][code-repo](Repository del codice del progetto per l'elaborazione dei pagamenti). 

## <a name="architectural-diagram"></a>Diagramma dell'architettura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Scenario utente

Il progetto è riferito al caso d'uso seguente.

> Questo scenario illustra in che modo un negozio Web fittizio ha trasferito l'elaborazione delle carte di pagamento in una soluzione PaaS basata su Azure. La soluzione gestisce la raccolta delle informazioni di base degli utenti, tra cui i dati di pagamento. La soluzione non usa questi dati di titolari di carte per elaborare i pagamenti. Dopo che i dati sono stati raccolti, i clienti sono responsabili dell'avvio e del completamento delle transazioni tramite un gestore di pagamenti. Per altre informazioni, vedere [Review and Guidance for Implementation](https://aka.ms/pciblueprintprocessingoverview) (Revisione e indicazioni per l'implementazione).

### <a name="use-case"></a>Caso d'uso
Un piccolo negozio Web, *Contoso Webstore*, è pronto a trasferire il proprio sistema di pagamento nel cloud. Il negozio ha scelto Microsoft Azure per ospitare il processo di acquisto e consentire a un impiegato di raccogliere i pagamenti con carta di credito dai clienti.

Per realizzare questi obiettivi, l'amministratore sta cercando una soluzione nata per il cloud e di rapida distribuzione e usa il modello di verifica (PoC, Proof of Concept) per parlare con gli stakeholder di come sia possibile usare Azure per raccogliere, archiviare e recuperare i dati delle carte di pagamento in conformità ai requisiti dello standard PCI DSS (Payment Card Industry Data Security Standard).

> L'utente ha la responsabilità di svolgere revisioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con l'architettura usata dal modello di verifica, in quanto i requisiti possono variare a seconda delle specifiche e della geografia dell'implementazione. Lo standard PCI DSS richiede la collaborazione diretta con un Qualified Security Assessor accreditato che certifichi la soluzione pronta per la produzione.

### <a name="elements-of-the-foundational-architecture"></a>Elementi dell'architettura di base

L'architettura di base è progettata con gli elementi fittizi seguenti:

Sito di dominio `contosowebstore.com`

Ruoli utente usati per illustrare il caso d'uso e consentire la comprensione dell'interfaccia utente.

#### <a name="role-site-and-subscription-admin"></a>Ruolo: amministrazione del sito e della sottoscrizione

|Elemento      |Esempio|
|----------|------|
|Nome utente: |`adminXX@contosowebstore.com`|
| Nome: |`Global Admin Azure PCI Samples`|
|Tipo di utente:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- L'account amministratore non può leggere le informazioni relative alle carta di credito non mascherate. Tutte le azioni vengono registrate.
- L'account amministratore non può gestire il database SQL né accedervi.
- L'account amministratore può gestire Active Directory e la sottoscrizione.

#### <a name="role-sql-administrator"></a>Ruolo: amministratore SQL

|Elemento      |Esempio|
|----------|------|
|Nome utente: |`sqlAdmin@contosowebstore.com`|
| Nome: |`SQLADAdministrator PCI Samples`|
| Nome: |`SQL AD Administrator`|
|Cognome: |`PCI Samples`|
|Tipo di utente:| `Administrator`|

- L'account sqladmin non può visualizzare le informazioni relative alle carte di credito non filtrate. Tutte le azioni vengono registrate.
- L'account sqladmin può gestire il database SQL.

#### <a name="role-clerk"></a>Ruolo: impiegato

|Elemento      |Esempio|
|----------|------|
|Nome utente:| `receptionist_EdnaB@contosowebstore.com`|
| Nome: |`Edna Benson`|
| Nome:| `Edna`|
|Cognome:| `Benson`|
| Tipo di utente: |`Member`|

Edna Benson è responsabile commerciale e della reception. Ha il dovere di assicurarsi che le informazioni relative ai clienti siano precise e che venga eseguita la fatturazione. Edna è l'utente connesso per tutte le interazioni con il sito Web demo Contoso Webstore e ha i diritti seguenti: 

- Può creare e leggere le informazioni relative ai clienti.
- Può modificare le informazioni relative ai clienti.
- Può sovrascrivere o sostituire il numero, la scadenza e le informazioni CVV delle carte di credito.

> Per il test delle funzionalità dell'ambiente distribuito in Contoso Webstore, l'utente viene connesso automaticamente con l'account di **Edna**.

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - Prezzi stimati

L'architettura di base e l'applicazione Web di esempio hanno una struttura a corrispettivi mensili e un costo di utilizzo all'ora che devono essere tenuti in considerazione al momento di definire le dimensioni della soluzione. Questi costi possono essere stimati tramite la [calcolatrice di determinazione costi di Azure](https://azure.microsoft.com/pricing/calculator/). A partire da settembre 2017, il costo mensile stimato per questa soluzione è di circa 2500 dollari, incluso un addebito per l'utilizzo di 1000 dollari al mese per Ambiente del servizio app versione 2. Questi costi variano a seconda dell'utilizzo e sono soggetti a modifiche. Per una stima più accurata, è compito del cliente calcolare i costi mensili stimati al momento della distribuzione. 

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

>- gateway applicazione
>- Azure Active Directory
>- Ambiente del servizio app versione 2
>- Log Analytics OMS
>- Insieme di credenziali chiave Azure
>- Gruppi di sicurezza di rete
>- Database SQL di Azure
>- Azure Load Balancer
>- Application Insights
>- Centro sicurezza di Azure
>- App Web di Azure
>- Automazione di Azure
>- Runbook di Automazione di Azure
>- DNS di Azure
>- Rete virtuale di Azure
>- Macchina virtuale di Azure
>- Gruppo di risorse e Criteri di Azure
>- Archiviazione BLOB di Azure
>- Controllo degli accessi in base al ruolo di Azure Active Directory

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

### <a name="network-segmentation-and-security"></a>Segmentazione e sicurezza della rete

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>gateway applicazione

L'architettura di base riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con web application firewall (WAF) e il set di regole OWASP abilitato. Altre funzionalità:

- [SSL end-to-end](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modalità WAF)
- [Modalità di prevenzione](/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- Possibilità di abilitare la [registrazione diagnostica](/azure/application-gateway/application-gateway-diagnostics)
- [Probe di integrità personalizzati](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

#### <a name="virtual-network"></a>Rete virtuale

L'architettura di base definisce una rete privata virtuale con spazio degli indirizzi 10.0.0.0/16.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato:
- Un gruppo di sicurezza di rete perimetrale per firewall e WAF di gateway applicazione
- Un gruppo di sicurezza di rete per la gestione del jumpbox (bastion host)
- Un gruppo di sicurezza di rete per l'ambiente del servizio app

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:
- I [log e gli eventi di diagnostica](/azure/virtual-network/virtual-network-nsg-manage-log) abilitati vengono archiviati nell'account di archiviazione 
- Connessione di Log Analytics di OMS alla [diagnostica dei gruppi di sicurezza di rete](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>Subnet
 Assicurarsi che ogni subnet sia associata al rispettivo gruppo di sicurezza di rete.

#### <a name="custom-domain-ssl-certificates"></a>Certificati SSL di dominio personalizzato
 Il traffico HTTPS viene abilitato tramite un certificato SSL di dominio personalizzato.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi, tra l'altro, tramite la crittografia e il controllo del database.

#### <a name="azure-storage"></a>Archiviazione di Azure

Per soddisfare i requisiti dei dati crittografati inattivi, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa sempre la [crittografia del servizio di archiviazione](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Database SQL di Azure

L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- [Autenticazione e autorizzazione AD](/azure/sql-database/sql-database-aad-authentication)
- [Controllo del database SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Regole del firewall](/azure/sql-database/sql-database-firewall-configure), che consentono pool di ruoli di lavoro ASE di base e la gestione dell'IP dei client
- [Rilevamento delle minacce a SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Colonne Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Dynamic Data Masking del database SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) tramite lo script PowerShell di post-distribuzione

### <a name="logging-and-auditing"></a>Registrazione e controllo

[Operations Management Suite (OMS)](/azure/operations-management-suite/) può offrire a Contoso Webstore funzionalità di registrazione estesa di tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. 

- **Log attività:** i [log attività](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.
- **Log di diagnostica:** i [log di diagnostica](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono tutti i log generati da ogni risorsa. Questi log includono i log eventi di sistema di Windows e i log di archiviazione BLOB di Azure, delle tabelle e delle code.
- **Log del firewall**: il gateway applicazione fornisce log completi relativi a diagnostica e accesso. I log del firewall sono disponibili per le risorse del gateway applicazione con WAF abilitato.
- **Archiviazione di log:** tutti i log di diagnostica sono configurati per la scrittura in un account di archiviazione di Azure centralizzato e crittografato per l'archiviazione con un periodo di conservazione definito (2 giorni). I log vengono quindi connessi a Log Analytics di Azure per l'elaborazione, l'archiviazione e la visualizzazione nel dashboard. [Log Analytics](https://azure.microsoft.com/services/log-analytics) è un servizio di OMS che semplifica la raccolta e l'analisi dei dati generati dalle risorse nel cloud e negli ambienti locali.

### <a name="encryption-and-secrets-management"></a>Gestione della crittografia e dei segreti

Contoso Webstore crittografa tutti i dati relativi alle carte di credito e usa Azure Key Vault per la gestione delle chiavi, impedendo il recupero dei dati di titolari di carte.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) consente di proteggere le chiavi di crittografia e i segreti usati da applicazioni e servizi cloud. 
- La crittografia [TDE SQL](/sql/relational-databases/security/encryption/transparent-data-encryption) viene usata per crittografare tutti i dati dei clienti relativi al titolare, alla data di scadenza e al codice CVV delle carte di credito.
- I dati vengono archiviati nel disco tramite [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption) e BitLocker.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.
- L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure AD per l'autenticazione dell'applicazione al database SQL di Azure. Per altre informazioni, vedere [Always Encrypted: Proteggere i dati sensibili nel database SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione, consente di configurare le risposte automatiche nei confronti delle azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti e intraprende le azioni appropriate per risolverli.
- Il [controllo degli accessi in base al ruolo di Azure](/azure/active-directory/role-based-access-control-configure) consente un'accurata gestione degli accessi per Azure. L'accesso a una sottoscrizione è limitato all'amministratore della sottoscrizione stessa e l'accesso ad Azure Key Vault è limitato per tutti gli utenti.

Per altre informazioni sull'uso delle funzionalità di sicurezza del database SQL di Azure, vedere l'[applicazione demo di esempio Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).
   
### <a name="web-and-compute-resources"></a>Risorse di calcolo e Web

#### <a name="app-service-environment"></a>Ambiente del servizio app

Il [servizio app di Azure](/azure/app-service/) è un servizio gestito per la distribuzione di applicazioni Web. L'applicazione Contoso Webstore viene distribuita come [app Web del servizio app](/azure/app-service-web/app-service-web-overview).

[Ambiente del servizio app di Azure (versione 2)](/azure/app-service/app-service-environment/intro) è una funzionalità del servizio app che offre un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app su vasta scala. Si tratta di un piano di servizio Premium usato da questa architettura di base per consentire la conformità allo standard PCI DSS.

Gli ambienti del servizio app sono isolati perché possano eseguire solo le applicazioni di un unico cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali.

L'uso di ambienti del servizio app per questa architettura è consentito per i controlli e le configurazioni seguenti:

- Host all'interno di una rete virtuale protetta e di regole di sicurezza di rete
- Ambiente del servizio app configurato con un certificato ILB autofirmato per la comunicazione HTTPS
- [Modalità di bilanciamento del carico interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modalità 3)
- Disabilitazione di [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings), protocollo TLS deprecato dal punto di vista dello standard PCI DSS
- Modifica della [crittografia TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controllo delle [porte di rete del traffico in ingresso](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF: limitazione dei dati](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Traffico del database SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) consentito


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastion host)

Dato che l'ambiente del servizio app è protetto e bloccato, è necessario un meccanismo che consenta il rilascio o la modifica di DevOps in caso di necessità, ad esempio la possibilità di monitorare l'app Web tramite Kudu. La macchina virtuale è protetta dal bilanciamento del carico NAT, che consente di connettere la macchina virtuale su una porta diversa dalla porta TCP 3389. 

È stata creata una macchina virtuale come jumpbox (bastion host) con le seguenti configurazioni:

-   [Estensione antimalware](/azure/security/azure-security-antimalware)
-   [Estensione di OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Estensione di Diagnostica di Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption) tramite Azure Key Vault (rispetta i requisiti di Azure per enti pubblici, dello standard PCI DSS, di HIPAA e altri).
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso.

### <a name="security-and-malware-protection"></a>Sicurezza e protezione dal malware

[Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.  

[Azure Advisor](/azure/advisor/advisor-overview) è un consulente cloud personalizzato che facilita l'applicazione delle procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso e offre avvisi configurabili per i casi in cui software dannoso o indesiderato tenti di installarsi o eseguirsi nei sistemi Azure.

### <a name="operations-management"></a>Operations Management

#### <a name="application-insights"></a>Application Insights

È possibile usare [Application Insights](https://azure.microsoft.com/services/application-insights/) per ottenere informazioni dettagliate di utilità pratica grazie alle funzionalità di gestione delle prestazioni e analisi immediata.

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) è un servizio di Operations Management Suite (OMS) che consente di raccogliere e analizzare i dati generati dalle risorse nel cloud e negli ambienti locali.

#### <a name="oms-solutions"></a>Soluzioni OMS

Occorre prendere in considerazione e configurare queste soluzioni OMS aggiuntive:
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure Networking Analytics](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Rilevamento delle modifiche](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Analisi dell'insieme di credenziali delle chiavi](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Elenco dei servizi](/azure/operations-management-suite/operations-management-suite-service-map)
- [Sicurezza e controllo](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Gestione degli aggiornamenti](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integrazione nel Centro sicurezza

La distribuzione predefinita ha lo scopo di offrire una serie di consigli di base del Centro sicurezza, indicando uno stato di configurazione integro e protetto. Da Centro sicurezza di Azure è possibile abilitare la raccolta dati. Per altre informazioni, vedere [Centro sicurezza di Azure - Introduzione](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

I componenti per la distribuzione di questa soluzione sono disponibili nel [repository del codice del piano PCI][code-repo]. La distribuzione dell'architettura di base richiede diversi passaggi, che vengono eseguiti tramite Microsoft PowerShell versione 5. Per connettersi al sito Web, è necessario specificare un nome di dominio personalizzato (ad esempio, contoso.com). Per specificarlo, usare l'opzione `-customHostName` nel passaggio 2. Per altre informazioni, vedere [Acquistare un nome di dominio personalizzato per app Web di Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Un nome di dominio personalizzato non è necessario per distribuire ed eseguire la soluzione, ma senza di esso non è possibile connettersi al sito Web a scopi dimostrativi.

Gli script aggiungono utenti del dominio al tenant di Azure AD specificati. È consigliabile creare un nuovo tenant di Azure AD da usare per l'esecuzione di test.

Se si verificano problemi durante la distribuzione, vedere [FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md) (Domande frequenti e risoluzione dei problemi).

Per distribuire la soluzione, è consigliabile usare un'installazione di PowerShell pulita. In alternativa, verificare che siano in uso i moduli più recenti necessari per la corretta esecuzione degli script di installazione. In questo esempio viene effettuato l'accesso al jumpbox (bastion host) e vengono eseguiti i comandi seguenti. Si noti che in questo modo il comando relativo al dominio personalizzato viene abilitato.


1. Installare i moduli richiesti e configurare correttamente i ruoli di amministratore.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Per istruzioni dettagliate sull'utilizzo, vedere [Script Instructions - Setup Administrative Account and Permission](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md) (Istruzioni dello script: configurare account e autorizzazioni di amministratore).
    
2. Installare il modulo di gestione dell'aggiornamento della soluzione 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Per istruzioni dettagliate sull'utilizzo, vedere [Script Instructions - Deploy and Configure Azure Resources](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md) (Istruzioni dello script: distribuire e configurare risorse di Azure).
    
3. Registrazione e monitoraggio di OMS. Dopo la distribuzione della soluzione, è possibile aprire un'area di lavoro di [Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) ed è possibile usare i modelli di esempio presenti nel repository della soluzione per illustrare come configurare un dashboard di monitoraggio. Per i modelli di OMS di esempio, fare riferimento alla [cartella omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Si noti che i dati devono essere raccolti in OMS per la corretta distribuzione dei modelli. Ciò può richiedere fino a un'ora o più a seconda dell'attività del sito.
 
    Quando si configura la registrazione per OMS, si prenda in considerazione di includere queste risorse:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modello di rischio

Un diagramma di flusso dei dati e il modello di rischio di esempio per il [modello di rischio del progetto di elaborazione dei pagamenti](https://aka.ms/pciblueprintthreatmodel) di Contoso Webstore.

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matrice delle responsabilità del cliente

I clienti hanno la responsabilità di conservare una copia della [matrice di riepilogo delle responsabilità](https://aka.ms/pciblueprintcrm32), che descrive i requisiti dello standard PCI DSS che devono essere soddisfatti dal cliente e quelli che costituiscono la responsabilità di Microsoft Azure.

## <a name="pci-compliance-review"></a>Verifica della conformità allo standard PCI 

La soluzione è stata rivista da Coalfire Systems, Inc. (Qualified Security Assessor per lo standard PCI DSS). Le [indicazioni per la verifica della conformità allo standard PCI e per l'implementazione](https://aka.ms/pciblueprintprocessingoverview) presentano la revisione della soluzione da parte di un revisore e considerazioni per la trasformazione del piano in una distribuzione pronta per la produzione.

## <a name="disclaimer-and-acknowledgements"></a>Dichiarazione di non responsabilità e riconoscimenti

*settembre 2017*

- Questo documento è esclusivamente a scopo informativo. MICROSOFT E AVYAN NON RILASCIANO ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.  
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft o Avyan.  
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.  

  > [!NOTE]
  > Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.  

- La soluzione in questo documento è da intendersi come un'architettura di base e non deve essere usata così com'è per scopi di produzione. Per ottenere la conformità allo standard PCI, è necessario che i clienti si rivolgano a un Qualified Security Assessor.  
- I nomi dei clienti, i record delle transazioni e i dati correlati presenti in questa pagina sono tutti fittizi, creati in funzione di questa architettura di base e forniti solo a scopo illustrativo. Nessuna associazione o riferimento reale è intenzionale e non ne deve esserne desunto alcuno.  
- Questa soluzione è stata sviluppata congiuntamente da Microsoft e Avyan Consulting ed è disponibile con la [licenza MIT](https://opensource.org/licenses/MIT).
- Questa soluzione è stata rivista da Coalfire, il revisore di Microsoft per lo standard PCI-DSS. Il documento di [verifica della conformità allo standard PCI](https://aka.ms/pciblueprintcrm32) presenta una revisione indipendente di terze parti della soluzione e indica i componenti di cui è necessario occuparsi. 

### <a name="document-authors"></a>Autori del documento

- *Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan Consulting)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repository del codice"
