---
title: Proteggere l'app Web per PCI DSS | Microsoft Docs
description: Questa app di esempio implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e il comportamento di sicurezza dell'organizzazione quando si sviluppa in Azure.
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
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992614"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Sviluppare un'infrastruttura sicura per un'app PCI

## <a name="overview"></a>Panoramica

Questa infrastruttura sicura per un'app PCI (Payment Card Industry) fornisce linee guida per la distribuzione di un ambiente PaaS (Payment Card Industry Platform as a Service) idoneo per la raccolta, l'archiviazione e il recupero dei dati di titolari di carte. Questa soluzione automatizza la distribuzione e la configurazione delle risorse di Azure per un'architettura di riferimento comune, mostrando i diversi modi in cui i clienti possono soddisfare requisiti specifici di sicurezza e conformità, e costituisce un'architettura di base con cui i clienti possono creare e configurare soluzioni personalizzate in Azure. La soluzione implementa un subset di requisiti simili agli standard di sicurezza dei dati del settore della carta di pagamento (PCI DSS 3,2).

Questo esempio distribuisce automaticamente un'architettura di riferimento dell'applicazione Web PaaS con controlli di sicurezza preconfigurati per consentire ai clienti di ottenere la conformità in modo analogo ai requisiti PCI DSS 3,2. La soluzione è costituita da modelli di Azure Resource Manager e script di PowerShell che semplificano la distribuzione e la configurazione delle risorse.

Attenersi alla procedura descritta in questo articolo in sequenza per assicurarsi che i componenti dell'applicazione siano configurati correttamente. Il database, il servizio app Azure, l'istanza Azure Key Vault e applicazione Azure istanza del gateway dipendono l'uno dall'altro.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare insieme i componenti e i servizi.

Questo esempio è destinato a sviluppatori esperti di Azure che operano nel settore della vendita al dettaglio e vogliono creare un'app per la vendita al dettaglio con un'infrastruttura di Azure sicura.

> [!NOTE]
> Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.

La distribuzione di un'applicazione in questo ambiente senza modifiche non è sufficiente a soddisfare completamente i requisiti di PCI DSS 3.2. Tenere presente quanto segue:

- Questa architettura offre una soluzione di base per aiutare i clienti a usare Azure in modo conforme a PCI DSS 3.2.
- I clienti hanno la responsabilità di svolgere una valutazione appropriata della sicurezza e della conformità di qualsiasi soluzione creata con questa architettura, in quanto i requisiti possono variare a seconda delle specifiche dell'implementazione di ogni cliente.

Durante lo sviluppo e la distribuzione di questa app si apprenderà come:

- Creare un'istanza di Azure Key Vault e archiviare e recuperare i relativi segreti.
- Distribuire database di Azure per Azure SQL, impostare dati protetti e autorizzare l'accesso ad esso.
- Distribuire l'app Web di Azure con un ambiente del servizio app che è un ambiente dedicato isolato con firewall front-end aEcess.
- Creare e configurare un'istanza di applicazione Azure gateway con un firewall che usa [OWASP primi 10 RuleSet](https://coreruleset.org/).
- Abilitare la crittografia dei dati in transito e a riposo usando i servizi di Azure.
- Configurare i criteri di Azure e le stampe blu per valutare le conformità

Dopo aver sviluppato e distribuito l'app, è necessario configurare l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

## <a name="architecture-diagram-and-components"></a>Diagramma e componenti dell'architettura
L'app è una tipica applicazione a più livelli con tre livelli. Il front-end, il back-end e il livello di database con componenti di monitoraggio e di gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-pci-web-app/architecture.png)

L'architettura è costituita da questi componenti:

- [Ambiente del servizio app V2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fornisce il ambiente del servizio app e il servizio di bilanciamento del carico per l'architettura dell'applicazione.
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornisce un servizio estendibile di gestione delle prestazioni delle applicazioni (APM) su più piattaforme.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Archivia e crittografa i segreti dell'app e gestisce la creazione di criteri di accesso.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fornisce il servizio di gestione delle identità e degli accessi basato sul cloud, l'accesso e l'accesso alle risorse.
- [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview). Fornisce il servizio per ospitare il dominio.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Ridimensiona le applicazioni e crea una disponibilità elevata per i servizi
- [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fornisce la soluzione per gli scenari di archiviazione dei dati moderni.
- [App Web di Azure](https://docs.microsoft.com/azure/app-service/overview/).  Fornisce un servizio basato su HTTP per l'hosting di applicazioni Web.
- [Database di Azure per AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Archivia in modo sicuro i dati dell'app.
- [Progetti di Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Fornisce specifiche e conformità con determinati standard e requisiti.
- [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/)
- [Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview). Valuta le risorse per la mancata conformità con i criteri assegnati.

## <a name="threat-model"></a>Modello di minaccia
La modellazione delle minacce è il processo di identificazione delle potenziali minacce per la sicurezza per l'azienda e l'applicazione e quindi per assicurarsi che sia presente un piano di mitigazione appropriato.

Questo esempio ha usato le [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) per implementare la modellazione delle minacce per l'app di esempio protetta. Grazie alla creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle fasi iniziali del processo di sviluppo. Questo consente di risparmiare tempo e denaro in seguito.

Questo è il modello di rischio per l'app di esempio:

![Modello di minaccia](./media/secure-pci-web-app/threat-model.png)

Nella schermata seguente vengono illustrate alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e chiede agli sviluppatori di considerare come mitigare i problemi.

![Output del modello di minaccia](./media/secure-web-app/threat-model-output.png)

Ad esempio, l'attacco SQL injection nell'output del modello di minaccia precedente è mitigato dalla purificazione degli input utente e dall'uso delle funzioni archiviate nel database di Azure per PostgreSQL. Questa attenuazione impedisce l'esecuzione arbitraria delle query durante le operazioni di lettura e scrittura dei dati.

Gli sviluppatori migliorano la sicurezza complessiva del sistema mitigando ciascuna minaccia nell'output del modello di minaccia.

## <a name="deployment"></a>Distribuzione
### <a name="prerequisites"></a>Prerequisiti
Per fare in modo che l'applicazione sia in esecuzione, è necessario installare gli strumenti seguenti:

- Un editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) della riga di comando di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) nel sistema. Git viene usato per clonare il codice sorgente localmente.
- [JQ](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice e intuitivo.

Questo esempio è costituito da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire le risorse in Azure. Per istruzioni dettagliate sulla distribuzione, vedere [qui](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Guida introduttiva

1.  Clonare o scaricare [questo](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repository GitHub nella workstation locale.
2.  Rivedere 0-Setup-AdministrativeAccountAndPermission.md ed eseguire i comandi indicati.
3.  Distribuire una soluzione di test con dati di esempio di Contoso o utilizzare un progetto pilota con un ambiente di produzione iniziale.

    Questo script distribuisce le risorse di Azure per una dimostrazione di un archivio Web usando i dati di esempio di contoso.

In questo esempio si esegue lo script di distribuzione che distribuisce l'app Web nel servizio app e crea le risorse.

Sono disponibili diversi modi per distribuire le app in Azure, tra cui:

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
Gruppi di sicurezza di https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) rete (contengono elenchi di controllo di accesso (ACL) che consentono o negano il traffico all'interno di una rete virtuale. I gruppi di sicurezza di rete possono essere usati per proteggere il traffico a livello di subnet o di singola VM. Esistono i seguenti gruppi di sicurezza di rete:

- 1 gruppo di sicurezza di rete per il gateway applicazione
- 1 gruppo di sicurezza di rete per l'ambiente del servizio app
- 1 gruppo di sicurezza di rete per il database SQL di Azure
- 1 gruppo di sicurezza di rete per il bastion host

Ogni gruppo di sicurezza di rete dispone di porte e protocolli specifici aperti in modo che la soluzione possa funzionare in modo sicuro e corretto. Per ogni gruppo di sicurezza di rete sono abilitate anche le configurazioni seguenti:

- Log di diagnostica ed eventi https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) (sono abilitati e archiviati in un account di archiviazione
- I log di monitoraggio di Azure sono connessi alla diagnostica del gruppo di sicurezza di rete (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Configurazione di NSG
La configurazione di NSG per ambiente del servizio app deve essere configurata come illustrato nell'immagine seguente.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

ogni subnet è associata al gruppo di sicurezza di rete corrispondente.

### <a name="config"></a>Configurazione
Le subnet sono configurate come illustrato nell'immagine seguente.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>DNS di Azure
Il Domain Name System (DNS) è responsabile della conversione (o risoluzione) del nome di un sito Web o di un nome di servizio nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi tramite l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record DNS usando le credenziali, le API, gli strumenti e la fatturazione disponibili per gli altri servizi di Azure. DNS di Azure supporta anche i domini DNS privati.

### <a name="protect-data"></a>Proteggere i dati
Per facilitare la protezione dei dati nel cloud, tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per tale stato. Le procedure consigliate per la crittografia e la protezione dei dati di Azure sono correlate agli stati seguenti dei dati:

- Inattivi: sono inclusi tutti gli oggetti, i contenitori e i tipi di archiviazione di informazioni esistenti in forma statica nei supporti fisici, siano essi dischi magnetici o dischi ottici.
- In transito: quando vengono trasferiti tra componenti, posizioni o programmi, i dati vengono considerati in transito. Esempi di questo stato sono il trasferimento in rete, attraverso un bus di servizio, da locale a cloud e viceversa e incluse le connessioni ibride come ExpressRoute, o durante un processo di input/output.

### <a name="azure-storage"></a>Archiviazione di Azure
Per soddisfare i requisiti dei dati crittografati, tutti i dati di [archiviazione di Azure](https://azure.microsoft.com/services/storage/) usano Azure Key Vault per mantenere il controllo delle chiavi che accedono ai dati e li crittografano. Ciò consente di proteggere i dati dei possessori di carte, supportando l'impegno a livello di sicurezza dell'organizzazione e i requisiti di conformità definiti da PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Crittografia dischi di Azure
Crittografia dischi di Azure sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="azure-sql-database"></a>Database SQL di Azure
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

- Azure Active Directory è il servizio Microsoft di gestione di identità e Directory basato sul cloud multi-tenant. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL di Azure.
- L'autenticazione per l'applicazione viene eseguita tramite Azure Active Directory. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Inoltre, la crittografia delle colonne del database usa Azure Active Directory per l'autenticazione dell'applicazione nel database SQL di Azure. Per altre informazioni, vedere [come proteggere i dati sensibili nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Il controllo degli accessi in base al ruolo di Azure consente agli amministratori di definire le autorizzazioni di accesso con granularità fine per concedere solo la quantità di accesso necessario agli utenti per eseguire i propri processi. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati dei possessori di carte. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- Azure Active Directory Privileged Identity Management consente ai clienti di ridurre al minimo il numero di utenti che hanno accesso a determinate informazioni, ad esempio i dati di titolari di carte. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- Azure Active Directory Identity Protection rileva le potenziali vulnerabilità che interessano le identità di un'organizzazione, configura le risposte automatiche per rilevare azioni sospette correlate alle identità di un'organizzazione ed esamina sospette eventi imprevisti per eseguire le azioni appropriate per risolverli.

### <a name="secrets-management"></a>Gestione dei segreti
La soluzione USA Azure Key Vault per la gestione di chiavi e segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le funzionalità seguenti di Azure Key Vault aiutano gli utenti a proteggere e accedere ai dati:

- I criteri di accesso avanzati vengono configurati in base alle necessità.
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
- Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Le chiavi sono di tipo RSA a 2048 bit protette dal modulo di protezione hardware.
- Con Key Vault, è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dei dati). File PFX e password) usando chiavi protette da moduli di protezione hardware (HSM)
- Usare il controllo degli accessi in base al ruolo per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito.
- Usare Key Vault per gestire i certificati TLS con il rinnovo automatico.
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
Con il Centro sicurezza di Azure, i clienti possono applicare e gestire centralmente i criteri di sicurezza tra i carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure accede inoltre alle configurazioni esistenti dei servizi di Azure in modo da fornire elementi consigliati su configurazione e servizi utili per migliorare le condizioni di sicurezza e proteggere i dati.

Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di avvisi di sicurezza predefiniti, che vengono attivati quando si verifica una minaccia o un'attività sospetta. Le regole di avviso personalizzate nel centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dal proprio ambiente.

Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un report di intelligence per le minacce per ogni minaccia rilevata, che consente ai team di risposta agli eventi imprevisti di analizzare e correggere le minacce.

### <a name="azure-application-gateway"></a>Gateway applicazione di Azure
L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con un web application firewall configurato e il set di regole OWASP abilitato. Altre funzionalità:

- SSL end-to-end
- Disabilitare TLS v 1.0 e v 1.1
- Abilita TLSv 1.2
- Web Application Firewall (modalità di prevenzione)
- Modalità di prevenzione con RuleSet OWASP 3,0
- Abilitare la registrazione diagnostica
- Probe di integrità personalizzati
- Il Centro sicurezza di Azure e Azure Advisor forniscono protezione e notifiche aggiuntive. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.

### <a name="logging-and-auditing"></a>Registrazione e controllo
I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:

- i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
- i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i registri eventi del sistema di Windows, i log di archiviazione di Azure, i log di controllo Key Vault e l'accesso del gateway applicazione e i log del firewall. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Log Analytics, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Il Centro sicurezza di Azure si integra inoltre con i log di monitoraggio di Azure, consentendo ai clienti di usare query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

Come parte di questa architettura sono incluse le [soluzioni di monitoraggio](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Azure seguenti:

- [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Valutazione SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la soluzione Controllo integrità SQL valuta il rischio e l'integrità degli ambienti server a intervalli regolari e offre ai clienti un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
- [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): La soluzione Integrità agente segnala il numero di agenti distribuiti e la relativa distribuzione geografica, nonché il numero di agenti che non rispondono e il numero di agenti che inviano dati operativi.
- [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.

### <a name="azure-monitor"></a>Monitoraggio di Azure
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) consente agli utenti di tenere traccia delle prestazioni, implementare la sicurezza e identificare le tendenze, permettendo alle organizzazioni di eseguire verifiche, creare avvisi e archiviare i dati, incluso il rilevamento delle chiamate API nelle relative risorse di Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

### <a name="azure-key-vault"></a>Azure Key Vault
Creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e mantenere la responsabilità per le attività operative come le seguenti:

- I dati archiviati in Key Vault includono:

   - Chiave di Application Insights
   - Chiave di accesso all'archiviazione dati
   - Stringa di connessione
   - Nome tabella dati
   - Credenziali utente

- I criteri di accesso avanzati vengono configurati in base alle necessità
- I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti
- Tutti i segreti e le chiavi in Key Vault hanno date di scadenza
- Tutte le chiavi nel Key Vault sono protette da HSM [chiave Type = HSM protected 2048-bit RSA Key]
- A tutti gli utenti e le identità vengono concesse le autorizzazioni minime necessarie tramite il controllo degli accessi in base al ruolo (RBAC)
- Le applicazioni non condividono un'istanza di Key Vault a meno che non si considerino attendibili reciprocamente e non debbano accedere agli stessi segreti in fase di esecuzione
- I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
- Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
È necessario configurare un tunnel VPN sicuro o un'istanza di [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) per stabilire in modo sicuro la connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita su Internet e consente ai clienti di "effettuare il tunneling" in modo sicuro all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La modalità tunnel IPsec viene utilizzata in questa opzione come meccanismo di crittografia.

Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

## <a name="cost-considerations"></a>Considerazioni sul costo
Se non si ha già un account Azure, è possibile crearne uno gratuito. Vai alla [pagina account gratuito](https://azure.microsoft.com/free/) per iniziare, Scopri cosa puoi fare con un account Azure gratuito e Scopri quali prodotti sono gratuiti per 12 mesi.

Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, è necessario pagare per alcune funzionalità Premium. Quando l'app viene ridimensionata e i livelli gratuiti e le versioni di valutazione offerte da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usa il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti possono essere utili per progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppo](secure-develop.md)
- [Distribuire](secure-deploy.md)
