---
title: "Linee guida per la sicurezza e la conformità di Azure - Carichi di lavoro regolamentati per il settore dei servizi finanziari FFIEC"
description: "Linee guida per la sicurezza e la conformità di Azure - Carichi di lavoro regolamentati per il settore dei servizi finanziari FFIEC"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: a1167f56f595f905c6338868806351345c06b91a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Linee guida per la sicurezza e la conformità di Azure - Carichi di lavoro regolamentati per il settore dei servizi finanziari FFIEC

## <a name="overview"></a>Panoramica

Il progetto Linee guida per la sicurezza e la conformità di Azure - Carichi di lavoro regolamentati per il settore dei servizi finanziari FFIEC consente di implementare una piattaforma sicura e conforme come applicazione web as-a-service (PaaS) progettata per la gestione di dati sensibili nel cloud. Il progetto è costituito da script automatizzati e linee guida che presentano un'architettura di riferimento semplice e un design in grado di semplificare l'adozione di soluzioni Microsoft Azure. Questo progetto illustra una soluzione end-to-end in grado di soddisfare le esigenze delle organizzazioni alla ricerca di un modo per ridurre il carico di lavoro e i costi di implementazione nel cloud.

Il progetto è pensato per soddisfare i requisiti di conformità ai rigorosi standard imposti dall'American Institute of Certified Public Accountants, come ad esempio SOC 1, SOC 2, Payment Card Industry Data Security Standards, DSS 3.2 del consiglio e FFIEC in materia di raccolta, archiviazione e recupero di dati finanziari sensibili. Esso illustra la gestione corretta di tali dati tramite l'implementazione di una soluzione in grado di gestire i dati finanziari in un ambiente sicuro, conforme e su più livelli. La soluzione viene implementata come soluzione PaaS end-to-end basata su Azure. 

Il progetto deve essere usato dai clienti come base per la comprensione e l'applicazione dei requisiti di gestione dei dati protetti nel cloud. La soluzione non deve essere usata in un'implementazione di produzione as-is, ma per comprendere, progettare e implementare servizi di Azure; essa è progettata come una baseline per un uso sicuro e conforme di Microsoft Azure da parte dei clienti.

Eventuali soluzioni di produzione personalizzate dovranno essere certificate da un revisore accreditato sulla base di questo progetto. Le soluzioni possono variare in base alle specifiche di implementazione e alla geografia di ogni cliente.

Per una panoramica rapida del funzionamento di questa soluzione, guardare questo [video](https://aka.ms/fsiblueprintvideo), che ne spiega e descrive l'implementazione.

## <a name="solution-components"></a>Componenti della soluzione

L'architettura include i seguenti componenti e usa le funzionalità di implementazione della soluzione di conformità PCI DSS di Azure.

- **Diagramma dell'architettura**. Il diagramma illustra l'architettura di riferimento usata per la soluzione Contoso Webstore.
- **Modelli di distribuzione**. In questa distribuzione vengono usati [modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) per distribuire automaticamente i componenti dell'architettura in Microsoft Azure specificando i parametri di configurazione durante l'installazione.
- **Script di distribuzione automatizzata**. Questi script agevolano la distribuzione della soluzione end-to-end. Gli script sono costituiti da:
    - Uno script di installazione dei moduli e degli [amministratori globali](/azure/active-directory/active-directory-assign-admin-roles-azure-portal), usato per installare i moduli PowerShell e verificare che questi ultimi e i ruoli di amministratore globale siano configurati correttamente. 
    - Uno script PowerShell di installazione viene usato per distribuire la soluzione end-to-end, fornita tramite un file con estensione zip e un file con estensione bacpac contenenti un'applicazione Web demo precompilata con il contenuto del [database SQL di esempio](https://github.com/Microsoft/azure-sql-security-sample) . Il codice sorgente per questa soluzione è disponibile per la revisione in [Payment Processing Blueprint code repository][code-repo](Repository del codice del progetto per l'elaborazione dei pagamenti). 

## <a name="architectural-diagram"></a>Diagramma dell'architettura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Scenario utente

Il progetto è riferito al caso d'uso seguente.

> Questo scenario illustra in che modo un negozio Web fittizio ha trasferito dati sensibili in una soluzione PaaS cloud basata su Azure. La soluzione di esempio illustra la gestione e la raccolta di informazioni di base e dati sensibili selezionati sugli utenti. Questa operazione prende spunto da ambienti di elaborazione pagamenti conformi PCI DSS descritti nelle Linee guida per la sicurezza e la conformità di Azure. Per altre informazioni sull'ampliamento di questo lavoro, il documento ["Review and Guidance for Implementation"](https://aka.ms/pciblueprintprocessingoverview) (Panoramica e linee guida per l'implementazione) offre una revisione degli ambienti conformi PCI DSS.

### <a name="use-case"></a>Caso d'uso
Il piccolo negozio Web chiamato *Contoso Webstore* è pronto per spostare dati finanziari, incluse informazioni di pagamento dei clienti, sul cloud. 

Per realizzare questi obiettivi, l'amministratore di Contoso Webstore sta cercando una soluzione di rapida implementazione e decide di usare il modello di verifica (PoC, Proof of Concept) per parlare con gli stakeholder di come sia possibile usare Azure per raccogliere, archiviare e recuperare dati finanziari in conformità con rigorosi requisiti di compliance.

> L'utente ha la responsabilità di svolgere revisioni appropriate della sicurezza e della conformità di qualsiasi soluzione creata con l'architettura usata dal modello di verifica, in quanto i requisiti possono variare a seconda delle specifiche e della geografia dell'implementazione. 

### <a name="elements-of-the-foundational-architecture"></a>Elementi dell'architettura di base

L'architettura di base è progettata con gli elementi fittizi seguenti:

Sito di dominio `contosowebstore.com`

I ruoli utente sono usati per illustrare il caso d'uso e consentire la comprensione dell'interfaccia utente.

#### <a name="role-site-and-subscription-admin"></a>Ruolo: amministrazione del sito e della sottoscrizione

|Elemento      |Esempio|
|----------|------|
|Nome utente: |`adminXX@contosowebstore.com`|
| Nome: |`Global Admin Azure PCI Samples`|
|Tipo di utente:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- L'account amministratore non può leggere informazioni finanziarie non mascherate. Tutte le azioni vengono registrate.
- L'account amministratore non può gestire il database SQL né accedervi.
- L'account amministratore può gestire Active Directory e le sottoscrizioni.

#### <a name="role-sql-administrator"></a>Ruolo: amministratore SQL

|Elemento      |Esempio|
|----------|------|
|Nome utente: |`sqlAdmin@contosowebstore.com`|
| Nome: |`SQLADAdministrator PCI Samples`|
| Nome: |`SQL AD Administrator`|
|Cognome: |`PCI Samples`|
|Tipo di utente:| `Administrator`|

- L'account sqladmin non può visualizzare informazioni finanziarie non filtrate. Tutte le azioni vengono registrate.
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
- Edna può sovrascrivere le informazioni finanziarie.
- L'account di Edna non può visualizzare informazioni finanziarie non filtrate.



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - Prezzi stimati

L'architettura di base e l'applicazione Web di esempio hanno una struttura a corrispettivi mensili e un costo di utilizzo all'ora che devono essere tenuti in considerazione al momento di definire le dimensioni della soluzione. Questi costi possono essere stimati tramite la [calcolatrice di determinazione costi di Azure](https://azure.microsoft.com/pricing/calculator/). A partire da settembre 2017, il costo mensile stimato per questa soluzione è di circa 2500 dollari, incluso un addebito per l'utilizzo di 1000 dollari al mese per Ambiente del servizio app versione 2. Questi costi variano a seconda dell'utilizzo e sono soggetti a modifiche. Per una stima più accurata, è compito del cliente calcolare i costi mensili stimati al momento della distribuzione. 

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

>- gateway applicazione
>- Azure Active Directory
>- Ambiente del servizio app versione 2
>- Log Analytics OMS
>- Azure Key Vault
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
- [Offload SSL](/azure/application-gateway/application-gateway-ssl-portal) abilitato
- [TLS v1.0 and v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) disabilitato
- [Web application firewall](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modalità WAF)
- [Modalità di prevenzione](/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0
- [Registrazione diagnostica](/azure/application-gateway/application-gateway-diagnostics) abilitata
- [Probe di integrità personalizzati](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), che offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

#### <a name="virtual-network"></a>Rete virtuale

L'architettura di base definisce una rete privata virtuale con spazio degli indirizzi 10.0.0.0/16.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato:

- Un gruppo di sicurezza di rete perimetrale per firewall e WAF di gateway applicazione
- Un gruppo di sicurezza di rete per la gestione del jumpbox (bastion host)
- Un gruppo di sicurezza di rete per l'ambiente del servizio app

Per ognuno dei gruppi di sicurezza di rete sono aperti porte e protocolli specifici per garantire il funzionamento protetto e corretto della soluzione. 

Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

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

#### <a name="azure-sql-database"></a>database SQL di Azure

L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- [Autenticazione e autorizzazione AD](/azure/sql-database/sql-database-aad-authentication)
- [Controllo del database SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Regole del firewall](/azure/sql-database/sql-database-firewall-configure), che consentono pool di ruoli di lavoro ASE di base e la gestione dell'IP dei client
- [Rilevamento delle minacce a SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Colonne Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Dynamic Data Masking del database SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) tramite lo script PowerShell di post-distribuzione

### <a name="logging-and-auditing"></a>Registrazione e controllo

[Operations Management Suite (OMS)](/azure/operations-management-suite/) può offrire a Contoso Webstore funzionalità di registrazione estese per tutte le attività del sistema e degli utenti, inclusa la registrazione di dati finanziari. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. 

- **Log attività.**  I [log attività](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.
- **Log di diagnostica.**  I [log di diagnostica](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono tutti i log generati da ogni risorsa. Questi log includono i log eventi di sistema di Windows e i log di archiviazione BLOB di Azure, delle tabelle e delle code.
- **Log del firewall.**  Il gateway applicazione fornisce log completi relativi a diagnostica e accesso. I log del firewall sono disponibili per le risorse del gateway applicazione con WAF abilitato.
- **Archiviazione di log.**  Tutti i log di diagnostica sono configurati per la scrittura in un account di archiviazione di Azure centralizzato e crittografato per l'archiviazione con un periodo di conservazione definito (2 giorni). I log vengono quindi connessi a Log Analytics di Azure per l'elaborazione, l'archiviazione e la visualizzazione nel dashboard. [Log Analytics](https://azure.microsoft.com/services/log-analytics) è un servizio di OMS che semplifica la raccolta e l'analisi dei dati generati dalle risorse nel cloud e negli ambienti locali.

### <a name="encryption-and-secrets-management"></a>Gestione della crittografia e dei segreti

Contoso Webstore crittografa tutti i dati sensibili e usa Azure Key Vault per la gestione delle chiavi, impedendo il recupero dei dati di titolari di carte.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) consente di proteggere le chiavi di crittografia e i segreti usati da applicazioni e servizi cloud. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) viene usato per crittografare tutti i dati finanziari dei clienti.
- I dati vengono archiviati nel disco tramite [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption) e BitLocker.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft multi-tenant di gestione di identità e directory basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.
- L'autenticazione per l'applicazione viene eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Anche la crittografia delle colonne del database usa Azure AD per l'autenticazione dell'applicazione al database SQL di Azure. Per altre informazioni, vedere [Always Encrypted: Proteggere i dati sensibili nel database SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità che potrebbero interessare le identità dell'organizzazione, consente di configurare le risposte automatiche nei confronti delle azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti e intraprende le azioni appropriate per risolverli.
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
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) disabilitato
- [Crittografia TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) modificata
- Controllo delle [porte di rete del traffico in ingresso](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF - limitazione dei dati](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Traffico del database SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) consentito


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastion host)

Dato che l'ambiente del servizio app è protetto e bloccato, è necessario un meccanismo che consenta il rilascio o la modifica di DevOps in caso di necessità, ad esempio la possibilità di monitorare l'app Web tramite Kudu. Una macchina virtuale è protetta dal bilanciamento del carico NAT, che consente di connettere la macchina virtuale su una porta diversa dalla porta TCP 3389. 

È stata creata una macchina virtuale come jumpbox (bastion host) con le seguenti configurazioni:

-   [Estensione antimalware](/azure/security/azure-security-antimalware)
-   [Estensione di OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Estensione di Diagnostica di Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption) tramite Azure Key Vault 
-   [Criteri di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) per ridurre il consumo di risorse della macchina virtuale quando non è in uso

### <a name="security-and-malware-protection"></a>Sicurezza e protezione dal malware

[Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.  

[Azure Advisor](/azure/advisor/advisor-overview) è un consulente cloud personalizzato che facilita l'applicazione delle procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) per servizi cloud e macchine virtuali di Azure è una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso e offre avvisi configurabili per i casi in cui software dannoso o indesiderato tenti di installarsi o eseguirsi nei sistemi Azure.

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

I componenti per la distribuzione di questa soluzione sono disponibili nel [repository del codice del progetto][code-repo]. La distribuzione dell'architettura di base richiede diversi passaggi, che vengono eseguiti tramite Microsoft PowerShell versione 5. Per connettersi al sito Web, è necessario specificare un nome di dominio personalizzato (ad esempio, contoso.com). Per specificarlo, usare l'opzione `-customHostName` nel passaggio 2. Per altre informazioni, vedere [Acquistare un nome di dominio personalizzato per app Web di Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Un nome di dominio personalizzato non è necessario per distribuire ed eseguire la soluzione, ma senza di esso non è possibile connettersi al sito Web a scopi dimostrativi.

Gli script aggiungono utenti del dominio al tenant di Azure AD specificati. Microsoft consiglia di creare un nuovo tenant di Azure AD da usare per l'esecuzione di test.

Se si verificano problemi durante la distribuzione, vedere [FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md) (Domande frequenti e risoluzione dei problemi).

Per distribuire la soluzione, Microsoft consiglia di usare un'installazione di PowerShell pulita. In alternativa, verificare che siano in uso i moduli più recenti necessari per la corretta esecuzione degli script di installazione. In questo esempio viene effettuato l'accesso al jumpbox (bastion host) e vengono eseguiti i comandi seguenti. Si noti che in questo modo il comando relativo al dominio personalizzato viene abilitato.


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
    
2. Installare il modulo di gestione dell'aggiornamento della soluzione. 
 
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
 

    
## <a name="threat-model"></a>Modello di minaccia

Un diagramma di flusso dei dati e il modello di rischio di esempio per il [modello di rischio del progetto](https://aka.ms/pciblueprintthreatmodel) di Contoso Webstore.

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matrice delle responsabilità del cliente

I clienti sono tenuti a conservare una copia della [matrice di riepilogo delle responsabilità](https://aka.ms/fsiblueprintcrm), che descrive i requisiti FFIEC che rientrano nelle responsabilità del cliente e quelli di cui è responsabile Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Dichiarazione di non responsabilità e riconoscimenti

*settembre 2017*

- Questo documento è esclusivamente a scopo informativo. MICROSOFT E AVYAN NON RILASCIANO ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.  
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft o Avyan.  
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.  

  > [!NOTE]
  > Alcune indicazioni contenute in questo documento possono comportare un maggiore uso di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.  

- La soluzione in questo documento è da intendersi come un'architettura di base e non deve essere usata così com'è per scopi di produzione. Per l'ottenimento della conformità è necessario che i clienti si rivolgano al revisore per convalidare la soluzione personalizzata finale.  
- I nomi dei clienti, i record delle transazioni e i dati correlati presenti in questa pagina sono tutti fittizi, creati in funzione di questa architettura di base e forniti solo a scopo illustrativo. Nessuna associazione o riferimento reale è intenzionale e non ne deve esserne desunto alcuno.  
- Questa soluzione è stata sviluppata congiuntamente da Microsoft e Avyan Consulting ed è disponibile con la [licenza MIT](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>Autori del documento

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repository del codice"
