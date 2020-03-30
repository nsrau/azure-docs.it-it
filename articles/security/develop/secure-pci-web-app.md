---
title: App Web sicura per PCI DSS Documenti Microsoft
description: Questa app di esempio implementa le procedure consigliate di sicurezza che migliorano l'applicazione e la sicurezza dell'organizzazione durante lo sviluppo in Azure.This sample app implements security best practices that improve your application and your organization's security posture when you develop on Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992614"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Sviluppare un'infrastruttura sicura per un'app PCIDevelop a secure infrastructure for a PCI app

## <a name="overview"></a>Panoramica

Questa infrastruttura sicura per un'app PCI (Payment Card Industry) fornisce indicazioni per la distribuzione di un ambiente PaaS (Payment Card Industry) adatto per la raccolta, l'archiviazione e il recupero dei dati dei titolari di carta. Questa soluzione automatizza la distribuzione e la configurazione delle risorse di Azure per un'architettura di riferimento comune, mostrando i diversi modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità, e costituisce un'architettura di base con cui i clienti possono creare e configurare soluzioni personalizzate in Azure. La soluzione implementa un sottoinsieme di requisiti simili agli standard di sicurezza dei dati del settore delle carte di pagamento (PCI DSS 3.2).

In questo esempio viene distribuita automaticamente un'architettura di riferimento dell'applicazione Web PaaS con controlli di sicurezza preconfigurati per consentire ai clienti di ottenere la conformità in modo simile ai requisiti PCI DSS 3.2. La soluzione è costituita da modelli di Azure Resource Manager e script di PowerShell che semplificano la distribuzione e la configurazione delle risorse.

È necessario seguire i passaggi descritti in questo articolo in sequenza per assicurarsi che i componenti dell'applicazione siano configurati correttamente. Il database, il servizio app di Azure, l'istanza dell'insieme di credenziali delle chiavi di Azure e l'istanza del gateway applicazione di Azure dipendono l'uno dall'altro.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare i componenti e i servizi.

Questo esempio è destinato a sviluppatori esperti in Azure che lavorano nel settore retail e desiderano creare un'app di vendita al dettaglio con un'infrastruttura di Azure sicura.

> [!NOTE]
> Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.

La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti di PCI DSS 3.2. Tenere presente quanto segue:

- Questa architettura offre una soluzione di base per aiutare i clienti a usare Azure in modo conforme a PCI DSS 3.2.
- I clienti hanno la responsabilità di svolgere una valutazione appropriata della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

Nello sviluppo e nella distribuzione di questa app, si apprende come:

- Creare un'istanza dell'insieme di credenziali delle chiavi di Azure e archiviare e recuperare segreti da essa.
- Distribuire il database di Azure per SQL di Azure, configurare i dati protetti e autorizzare l'accesso.
- Distribuire l'app Web di Azure con l'ambiente del servizio app, che è un'isolata dedicata con firewall front-end aEcess.
- Creare e configurare un'istanza del gateway applicazione di Azure con un firewall che usa Set di regole [OWASP Top 10](https://coreruleset.org/).
- Abilitare la crittografia dei dati in transito e inattivi usando i servizi di Azure.Enable encryption of data in transit and at rest by using Azure services.
- Configurare i criteri di Azure e le stampe blu per valutare le conformitàSet up the Azure policy and blue prints to evaluate the compliances

Dopo aver sviluppato e distribuito questa app, sarà stata configurata l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
L'app è una tipica applicazione a più livelli con tre livelli. Il livello front-end, back-end e database con componenti di monitoraggio e gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-pci-web-app/architecture.png)

L'architettura è costituita dai seguenti componenti:

- [Ambiente del servizio app v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fornisce l'ambiente del servizio app e il servizio di bilanciamento del carico per l'architettura dell'applicazione.
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornisce un servizio APM (Application Performance Management) estensibile su più piattaforme.
- [Archivio chiavi di Azure](https://docs.microsoft.com/azure/key-vault/). Archivia e crittografa i segreti della nostra app e gestisce la creazione di criteri di accesso che li circondano.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fornisce il servizio di gestione di identità e accessi basato su cloud, l'accesso e l'accesso alle risorse.
- [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview). Fornisce il servizio per ospitare il dominio.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Ridimensiona le tue applicazioni e crea un'elevata disponibilità per i tuoi servizi
- [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fornisce la soluzione per scenari di archiviazione dati moderni.
- [App Web di Azure](https://docs.microsoft.com/azure/app-service/overview/).  Fornisce un servizio basato su HTTP per l'hosting di applicazioni Web.
- [Database di Azure per AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Archivia in modo sicuro i dati della nostra app.
- [Blueprint di Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Fornisce specifiche e conformità a determinati standard e requisiti.
- [Centro sicurezza di AzureAzure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview). Valuta le risorse per la non conformità con i criteri assegnati.

## <a name="threat-model"></a>Modello di minaccia
La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'azienda e l'applicazione e quindi garantire che sia in atto un piano di attenuazione appropriato.

In questo esempio è stato usato [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) per implementare la modellazione delle minacce per l'app di esempio sicura. Con la creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle prime fasi del processo di sviluppo. Ciò consente di risparmiare tempo e denaro in un secondo momento.

Questo è il modello di minaccia per l'app di esempio:This is the threat model for the sample app:

![Modello di minaccia](./media/secure-pci-web-app/threat-model.png)

Alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce sono illustrate nella schermata seguente. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e richiede agli sviluppatori di pensare a come mitigare i problemi.

![Output del modello di minaccia](./media/secure-web-app/threat-model-output.png)

For example, SQL injection in the preceding threat model output is mitigated by sanitizing user inputs and by using stored functions in Azure Database for PostgreSQL. Questa attenuazione impedisce l'esecuzione arbitraria di query durante le operazioni di lettura e scrittura dei dati.

Gli sviluppatori migliorano la sicurezza complessiva del sistema attenuando ciascuna delle minacce nell'output del modello di minaccia.

## <a name="deployment"></a>Distribuzione
### <a name="prerequisites"></a>Prerequisiti
Per mettere in funzione l'applicazione, è necessario installare questi strumenti:

- Editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) sul vostro sistema. Git viene utilizzato per clonare il codice sorgente in locale.
- [jq](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice.

Questo esempio è composto da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire le risorse in Azure.This sample is composed of JSON configuration files and PowerShell scripts that are handled by Azure Resource Manager's API service to deploy resources within Azure. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Guida introduttiva

1.  Clonare o scaricare [questo](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repository GitHub nella workstation locale.
2.  Rivedere 0-Setup-AdministrativeAccountAndPermission.md ed eseguire i comandi indicati.
3.  Distribuire una soluzione di test con dati di esempio di Contoso o utilizzare un progetto pilota con un ambiente di produzione iniziale.

    Questo script distribuisce le risorse di Azure per una dimostrazione di un archivio Web usando i dati di esempio di Contoso.This script deploys Azure resources for a demonstration of a web store using Contoso sample data.

In questo esempio si esegue lo script di distribuzione che distribuisce l'app Web nel servizio app e vengono create le risorse.

Esistono molti modi per distribuire le app in Azure, tra cui:There are many ways to deploy apps on Azure, including:

- Modelli di Gestione risorse di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- Portale di Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="network"></a>Rete
L'architettura definisce una rete privata virtuale con spazio degli indirizzi 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Gruppi dihttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sicurezza di rete(contengono elenchi di controllo di accesso (ACL) che consentono o negano il traffico all'interno di una rete virtuale.Network security groups(contain Access Control Lists (ACLs) that allow or deny traffic within a virtual network. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Esistono i seguenti gruppi di sicurezza di rete:

- 1 gruppo di sicurezza di rete per il gateway applicazione
- 1 gruppo di sicurezza di rete per l'ambiente del servizio app
- 1 gruppo di sicurezza di rete per il database SQL di Azure
- 1 gruppo di sicurezza di rete per il bastion host

Ognuno dei gruppi di sicurezza di rete ha porte e protocolli specifici aperti in modo che la soluzione possa funzionare in modo sicuro e corretto. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

- Registri di diagnosticahttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) ed eventi (sono abilitati e archiviati in un account di archiviazioneDiagnostic logs and events( are enabled and stored in a storage account
- I log di Monitoraggio di Azure sono connessi alla diagnostica del gruppo di sicurezza di rete(Azure Monitor logs are connected to the network security group's diagnostics(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Configurazione del gruppo di sicurezza di base
La configurazione del gruppo di sicurezza di rete per l'ambiente del servizio app deve essere configurata come illustrato nell'immagine seguente.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

### <a name="config"></a>File di configurazione
Le subnet vengono configurate come illustrato nell'immagine seguente.
 ![File di configurazione](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>DNS di Azure
Il DNS (Domain Name System) è responsabile della traduzione (o risoluzione) di un nome di sito Web o di servizio nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

### <a name="protect-data"></a>Proteggere i dati
Per facilitare la protezione dei dati nel cloud, tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per tale stato. Le procedure consigliate per la crittografia e la protezione dei dati di Azure sono correlate agli stati seguenti dei dati:

- Inattivi: sono inclusi tutti gli oggetti, i contenitori e i tipi di archiviazione di informazioni esistenti in forma statica nei supporti fisici, siano essi dischi magnetici o dischi ottici.
- In transito: quando vengono trasferiti tra componenti, posizioni o programmi, i dati vengono considerati in transito. Esempi di questo stato sono il trasferimento in rete, attraverso un bus di servizio, da locale a cloud e viceversa e incluse le connessioni ibride come ExpressRoute, o durante un processo di input/output.

### <a name="azure-storage"></a>Archiviazione di Azure
Per soddisfare i requisiti dei dati crittografati inattivi, in tutta [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) viene utilizzato l'insieme di credenziali delle chiavi di Azure per mantenere il controllo delle chiavi che accedono e crittografano i dati. Ciò consente di proteggere i dati dei possessori di carte, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Crittografia dischi di Azure si avvale della funzionalità BitLocker di Windows per abilitare la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="azure-sql-database"></a>database SQL di Azure
L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- L'[autenticazione e l'autorizzazione di Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) consentono la gestione delle identità degli utenti del database e di altri servizi Microsoft in una posizione centrale.
- Il [controllo del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo in un account di Archiviazione di Azure.
- Il database SQL di Azure è configurato per l'uso di [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), che esegue la crittografia e la decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni per proteggere le informazioni inattive. Transparent Data Encryption garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato.
- Le [regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impediscono completamente l'accesso ai server di database fino alla concessione delle autorizzazioni appropriate. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.
- Il [servizio di rilevamento delle minacce di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) consente di rilevare e di rispondere a minacce potenziali non appena si verificano, visualizzando avvisi relativi alla sicurezza per attività sospette del database, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali.
- Le [colonne crittografate](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) assicurano che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo l'abilitazione della crittografia dei dati, solo le applicazioni client o i server applicazioni con accesso alle chiavi possono accedere ai dati di testo non crittografato.
- La [maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili nascondendoli agli utenti o alle applicazioni senza privilegi. La maschera dati dinamica può individuare automaticamente dati potenzialmente sensibili e suggerire le maschere appropriate da applicare. In questo modo è più semplice identificare e ridurre l'accesso ai dati in modo da evitare l'uscita dal database tramite accesso non autorizzato. I clienti sono tenuti a modificare le impostazioni della maschera dati dinamica in modo da adattarle al proprio schema del database.

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono le funzionalità necessarie per gestire l'accesso ai dati dei possessori di carte nell'ambiente di Azure:

- Azure Active Directory  è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure.
- L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory.For](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)more information, see Integrating applications with Azure Active Directory . Inoltre, la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione nel database SQL di Azure. Per altre informazioni, vedere come proteggere i dati sensibili nel database SQL di Azure.For more information, see [how to protect sensitive data in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Il controllo degli accessi in base al ruolo di Azure consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati dei possessori di carte. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- Azure Active Directory Privileged Identity Management consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni, ad esempio i dati dei possessori di carte. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- Azure Active Directory Identity Protection rileva potenziali vulnerabilità che interessano le identità di un'organizzazione, configura le risposte automatiche alle azioni sospette rilevate relative alle identità di un'organizzazione e analizza le versioni sospette incidenti di intraprendere le misure appropriate per risolverli.

### <a name="secrets-management"></a>Gestione dei segreti
la soluzione usa Azure Key Vault per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere e accedere ai dati:

- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- Con Key Vault è possibile crittografare chiavi e segreti, ad esempio chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dei dati, . File PFX e password) utilizzando chiavi protette da moduli di sicurezza hardware (HSM)
- Utilizzare il controllo degli accessi in base al ruolo per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito.
- Utilizzare Vault chiave per gestire i certificati TLS con rinnovo automatico.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
con il Centro sicurezza di Azure i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di avvisi di sicurezza predefiniti che vengono attivati in caso di minaccia o di attività sospetta. Le regole di avviso personalizzate nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un report di intelligence per le minacce per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.

### <a name="azure-application-gateway"></a>Gateway applicazione di Azure
L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

- SSL end-to-end
- Disabilitare TLS v1.0 e v1.1
- Abilita TLSv1.2
- Web application firewall (modalità di prevenzione)
- Modalità di prevenzione con set di regole OWASP 3.0
- Abilitare la registrazione diagnostica
- Probe di integrità personalizzati
- Centro sicurezza di Azure e Azure Advisor offrono protezione aggiuntiva e notifiche. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="logging-and-auditing"></a>Registrazione e controllo
I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:

- i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i log del sistema di eventi di Windows, i log di Archiviazione di Azure, i log di controllo dell'insieme di credenziali delle chiavi e i log di accesso al gateway applicazione e Firewall.These logs include Windows event system logs, Azure Storage logs, Key Vault audit logs, and Application Gateway access and Firewall logs. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
Questi log vengono consolidati nei log di Monitoraggio di [Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Log Analytics, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Inoltre, Il Centro sicurezza di Azure si integra con i log di Monitoraggio di Azure, consentendo ai clienti di usare le query Kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Le [soluzioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di monitoraggio di Azure seguenti sono incluse come parte di questa architettura:The following Azure monitoring solutions are included as a part of this architecture:

- [Valutazione di Active Directory:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)la soluzione di controllo dell'integrità di Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco con priorità di consigli specifici per l'infrastruttura server distribuita.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e presenta un elenco classificato in ordine di priorità di consigli specifici per l'infrastruttura di server distribuita.
- [Integrità agente:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)la soluzione Integrità agente segnala il numero di agenti distribuiti e la relativa distribuzione geografica, nonché il numero di agenti che non rispondono e il numero di agenti che inviano dati operativi.
- [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): questa soluzione fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

### <a name="azure-monitor"></a>Monitoraggio di Azure
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, implementare la sicurezza e identificare le tendenze, permettendo alle organizzazioni di eseguire verifiche, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure
Creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e mantenere la responsabilità per le attività operative come di seguito:

- I dati archiviati in Key Vault includono:

   - Chiave di Application Insights
   - Chiave di accesso all'archiviazione datiData Storage Access key
   - Stringa di connessione
   - Data table name
   - Credenziali dell'utente

- I criteri di accesso avanzati vengono configurati in base alle necessità
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza
- Tutte le chiavi nell'insieme di credenziali delle chiavi sono protette da HSM [Tipo di chiave - Chiave RSA a 2048 bit protetta da HSM]
- A tutti gli utenti/identità vengono concesse le autorizzazioni minime necessarie tramite il controllo degli accessi in base al ruolo
- Le applicazioni non condividono un'istanza di Key Vault a meno che non si considerino attendibili reciprocamente e non debbano accedere agli stessi segreti in fase di esecuzione
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione avviene tramite Internet e consente ai clienti di "tunnel" in modo sicuro le informazioni all'interno di un collegamento crittografato tra la rete del cliente e Azure.This connection takes place over the Internet and allows customers to securely "tunnel" information inside an encrypted link between the customer's network and Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La modalità tunnel IPsec viene usata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="cost-considerations"></a>Considerazioni sul costo
Se non si dispone già di un account Azure, è possibile crearne uno gratuito. Vai alla [pagina dell'account gratuito](https://azure.microsoft.com/free/) per iniziare, scopri cosa puoi fare con un account Azure gratuito e scopri quali prodotti sono gratuiti per 12 mesi.

Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, devi pagare alcune funzionalità premium. Man mano che l'app viene ridimensionata e i livelli e le versioni di valutazione gratuiti offerti da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti consentono di progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppare](secure-develop.md)
- [Distribuire](secure-deploy.md)
