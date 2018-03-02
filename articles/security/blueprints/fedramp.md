---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP
description: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9b605e500925e8435b15ec8055f8d8f376888aaf
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP

## <a name="overview"></a>Panoramica

Il [programma FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov) è un programma governativo degli Stati Uniti che stabilisce un approccio standard per quanto riguarda valutazione della sicurezza, autorizzazione e monitoraggio continuo per i prodotti e i servizi cloud. Questo progetto di automazione Azure Security and Compliance Blueprint contiene le linee guida per la distribuzione di un ambiente di infrastruttura distribuita come servizio (IaaS) conforme a FedRAMP adatto per una semplice applicazione Web per Internet. Questa soluzione automatizza la distribuzione e la configurazione delle risorse di Azure per un'architettura di riferimento comune, mostrando i diversi modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità, e costituisce un'architettura di base con cui i clienti possono creare e configurare soluzioni personalizzate in Azure. La soluzione implementa un subset di controlli basati sui principi di base di FedRAMP High e su NIST SP 800-53. Per altre informazioni sui requisiti di FedRAMP High e su questa soluzione, vedere [FedRAMP High Requirements - High-Level Overview](fedramp-controls-overview.md) (Requisiti di FedRAMP High - Panoramica generale). ***Nota: questa soluzione viene distribuita in Azure per enti pubblici.***

Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione. La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti dei principi di base di FedRAMP High. Tenere presente quanto segue:
- Questa architettura offre una soluzione di base per aiutare i clienti a usare Azure in modo conforme a FedRAMP.
- I clienti hanno la responsabilità di svolgere una valutazione appropriata della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente. 

Per una rapida panoramica del funzionamento di questa soluzione, guardare questo [video](https://aka.ms/fedrampblueprintvideo), che ne spiega e descrive la distribuzione.

Fare clic [qui](https://aka.ms/fedrampblueprintrepo) per le istruzioni di distribuzione.

## <a name="solution-components"></a>Componenti della soluzione

Questo progetto di automazione Azure Security and Compliance Blueprint distribuisce automaticamente un'architettura di riferimento per applicazioni Web IaaS con controlli di sicurezza preconfigurati per aiutare i clienti a realizzare la conformità ai requisiti di FedRAMP. La soluzione è costituita da modelli di Azure Resource Manager e script di PowerShell che semplificano la distribuzione e la configurazione delle risorse. Viene fornita la [documentazione sulla conformità](#compliance-documentation) correlata, che indica i controlli di sicurezza ereditati da Azure e le risorse e le configurazioni distribuite allineate ai controlli di sicurezza NIST SP 800-53, per consentire alle organizzazioni di adeguarsi agli obblighi in materia di conformità.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Questa soluzione distribuisce un'architettura di riferimento per un'applicazione Web IaaS con back-end di database. L'architettura include un livello Web, un livello dati, un'infrastruttura Active Directory, un gateway applicazione e un servizio di bilanciamento del carico. Le macchine virtuali distribuite nei livelli Web e dati sono configurate in un set di disponibilità, mentre le istanze di SQL Server sono configurate in un gruppo di disponibilità AlwaysOn per disponibilità elevata. Le macchine virtuali sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo. Un jumpbox di gestione (bastion host) fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite.

![testo alternativo](images/fedramp-architectural-diagram.png?raw=true "Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP")

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

* **Macchine virtuali di Azure**
    - (1) Gestione/bastion (Windows Server 2016 Datacenter)
    - (2) Controller di dominio Active Directory (Windows Server 2016 Datacenter)
    - (2) Nodo del cluster SQL Server (SQL Server 2016 su Windows Server 2012 R2)
    - (1) Controllo di SQL Server (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **Set di disponibilità**
    - (1) Controller di dominio Active Directory
    - (1) Controllo e nodi del cluster SQL
    - (1) Web/IIS
* **Rete virtuale di Azure**
    - (1) /16 reti virtuali
    - (5) /24 subnet
    - Impostazioni DNS configurate in entrambi i controller di dominio
* **Servizio di bilanciamento del carico di Azure**
    - (1) Servizio di bilanciamento del carico SQL
* **Gateway applicazione Azure**
    - (1) Gateway applicazione WAF abilitato
      - Modalità firewall: prevenzione
      - Set di regole: OWASP 3.0
      - Listener: porta 443
* **Archiviazione di Azure**
    - (7) Account di archiviazione con ridondanza geografica
* **Backup di Azure**
    - (1) Insieme di credenziali di Servizi di ripristino
* **Insieme di credenziali chiave Azure**
    - (1) Key Vault
* **Azure Active Directory**
* **Azure Resource Manager**
* **Log Analytics di Azure**
* **Automazione di Azure**
    - (1) Account di automazione
* **Operations Management Suite**
    - (1) Area di lavoro OMS

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

### <a name="network-segmentation-and-security"></a>Segmentazione e sicurezza della rete

#### <a name="application-gateway"></a>gateway applicazione

L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con web application firewall (WAF) e il set di regole OWASP abilitati. Altre funzionalità:

- [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Offload SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Disabilitazione di [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modalità WAF)
- [Modalità di prevenzione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con set di regole OWASP 3.0

#### <a name="virtual-network"></a>Rete virtuale

L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Questa soluzione distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate all'interno di una rete virtuale. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione.

Vedere la configurazione per i [gruppi di sicurezza di rete](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribuita con questa soluzione. Le organizzazioni possono configurare gruppi di sicurezza di rete modificando il file indicato sopra usando [questa documentazione](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) come riferimento.

Ognuna delle subnet ha un gruppo di sicurezza di rete dedicato:
- 1 gruppo di sicurezza di rete per il gateway applicazione (LBNSG)
- 1 gruppo di sicurezza di rete per il jumpbox (MGTNSG)
- 1 gruppo di sicurezza di rete per i controller di dominio primario e di backup (ADNSG)
- 1 gruppo di sicurezza di rete per SQL Server e il controllo di condivisione file (SQLNSG)
- 1 gruppo di sicurezza di rete per il livello Web (WEBNSG)

#### <a name="subnets"></a>Subnet

Ogni subnet è associata al rispettivo gruppo di sicurezza di rete.

### <a name="data-at-rest"></a>Dati inattivi

L'architettura protegge i dati inattivi attraverso diverse misure di crittografia.

#### <a name="azure-storage"></a>Archiviazione di Azure

Per soddisfare i requisiti di crittografia dei dati inattivi, tutti gli account di archiviazione usano la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>Database SQL

Il database SQL è configurato per l'uso di [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), che esegue la crittografia e la decrittografia in tempo reale dei dati e dei file di log per proteggere le informazioni inattive. TDE garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Crittografia dischi di Azure viene usato per crittografare i dischi delle macchine virtuali IaaS Windows. [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="logging-and-auditing"></a>Registrazione e controllo

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornisce una registrazione completa delle attività di sistema e degli utenti, nonché dell'integrità del sistema. 

- **Log attività:** i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.
- **Log di diagnostica:** i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono tutti i log generati da ogni risorsa. Questi log includono log eventi del sistema Windows, log di archiviazione di Azure, log di controllo di Azure Key Vault e log degli accessi e del firewall del gateway applicazione.
- **Archiviazione dei log:**  i log attività e i log di diagnostica di Azure possono essere connessi ad Azure Log Analytics per l'elaborazione, l'archiviazione e la creazione di dashboard. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

### <a name="secrets-management"></a>Gestione dei segreti

La soluzione usa Azure Key Vault per gestire chiavi e segreti.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) consente di proteggere le chiavi di crittografia e i segreti usati da applicazioni e servizi cloud. 
- La soluzione è integrata con Azure Key Vault per gestire le chiavi e i segreti di crittografia dei dischi delle macchine virtuali IaaS.

### <a name="identity-management"></a>Gestione delle identità

Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud.
- L'autenticazione a un'applicazione Web distribuita dal cliente può essere eseguita tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente un'accurata gestione degli accessi per Azure. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione, mentre l'accesso alle risorse può essere limitato in base al ruolo utente.
- Un'istanza di Active Directory IaaS distribuita fornisce la gestione delle identità a livello di sistema operativo per le macchine virtuali IaaS distribuite.
   
### <a name="compute-resources"></a>Risorse di calcolo

#### <a name="web-tier"></a>Livello Web

La soluzione distribuisce macchine virtuali di livello Web in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). I set di disponibilità assicurano che le macchine virtuali vengano distribuite in più cluster hardware isolati per migliorare la disponibilità.

#### <a name="database-tier"></a>Livello database

La soluzione distribuisce macchine virtuali di livello database in un set di disponibilità come [gruppo di disponibilità AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Il gruppo di disponibilità Always On offre funzionalità di ripristino di emergenza e disponibilità elevata. 

#### <a name="active-directory"></a>Active Directory

Tutte le macchine virtuali distribuite dalla soluzione sono aggiunte a un dominio e vengono usati criteri di gruppo di Active Directory per applicare le configurazioni di sicurezza e conformità a livello di sistema operativo. Le macchine virtuali Active Directory vengono distribuite in un set di disponibilità.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastion host)

Un jumpbox di gestione (bastion host) fornisce una connessione sicura agli amministratori per accedere alle risorse distribuite. Il gruppo di sicurezza di rete associato alla subnet di gestione in cui si trova la macchina virtuale jumpbox consente connessioni solo sulla porta TCP 3389 per RDP. 

### <a name="malware-protection"></a>Protezione antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Macchine virtuali offre una funzionalità di protezione in tempo reale che consente di identificare e rimuovere virus, spyware e altro software dannoso con avvisi configurabili per i casi in cui un software dannoso o indesiderato tenta di installare o eseguire se stesso su macchine virtuali protette.

### <a name="patch-management"></a>Gestione delle patch

Le macchine virtuali Windows distribuite tramite questo progetto di automazione Azure Security and Compliance Blueprint vengono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione distribuisce anche la soluzione OMS di automazione di Azure tramite la quale è possibile creare distribuzioni di aggiornamento per distribuire patch nei server Windows in base alle esigenze.

### <a name="operations-management"></a>Operations Management

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) è un servizio di Operations Management Suite (OMS) che consente la raccolta e l'analisi dei dati generati dalle risorse in Azure e negli ambienti locali.

#### <a name="oms-solutions"></a>Soluzioni OMS

Le soluzioni OSM seguenti sono preinstallate come parte di questa soluzione:
- [Valutazione di AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Sicurezza e controllo](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Gestione degli aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Log attività di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Rilevamento delle modifiche](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Documentazione sulla conformità

### <a name="customer-responsibility-matrix"></a>Matrice delle responsabilità del cliente

La [matrice delle responsabilità del cliente](https://aka.ms/blueprinthighcrm) (cartella di lavoro di Excel) elenca tutti i controlli di sicurezza richiesti dai principi di base di FedRAMP High. La matrice indica, per ogni controllo (o sottoparte di un controllo), se la responsabilità dell'implementazione per il controllo è di Microsoft, del cliente o condivisa tra i due. 

### <a name="control-implementation-matrix"></a>Matrice di implementazione dei controlli

La [matrice di implementazione dei controlli](https://aka.ms/blueprintwacim) (cartella di lavoro di Excel) elenca tutti i controlli di sicurezza richiesti dai principi di base di FedRAMP High. La matrice indica, per ogni controllo (o sottoparte di un controllo) per cui è assegnata una responsabilità al cliente nella matrice delle responsabilità del cliente, 1) se la soluzione di automazione implementa il controllo e 2) una descrizione dell'allineamento dell'implementazione ai requisiti di controllo. Queste informazioni sono disponibili anche [qui](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Questo progetto di automazione Azure Security and Compliance Blueprint è costituito da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire risorse in Azure. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/fedrampblueprintrepo). ***Nota: questa soluzione viene distribuita in Azure per enti pubblici.***

#### <a name="quickstart"></a>Guida introduttiva
1. Clonare o scaricare [questo](https://aka.ms/fedrampblueprintrepo) repository GitHub nella workstation locale.

2. Eseguire lo script di PowerShell di predistribuzione: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Fare clic sul pulsante di seguito, accedere al portale di Azure, immettere i parametri del modello di Azure Resource Manager richiesti e quindi fare clic su **Acquista**.

    [![Distribuzione in Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.  
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.  
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.  
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.  
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
