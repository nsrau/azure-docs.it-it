---
title: Azure Security and Compliance Blueprint - Hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL
description: Azure Security and Compliance Blueprint - Hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: b30ef7d0b373f0d9a440b6649f819e429e28328c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314927"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Progetto di sicurezza e conformità di Azure: Hosting di applicazioni Web PaaS per i carichi di lavoro UK OFFICIAL

## <a name="azure-security-and-compliance-blueprints"></a>Progetti di sicurezza e conformità di Azure

I progetti di Azure sono costituiti da documenti con linee guida e modelli di automazione che consentono di distribuire architetture basate sul cloud per offrire soluzioni per scenari con requisiti di accreditamento o di conformità. I progetti di Azure sono raccolte di linee guida e modelli di automazione che consentono ai clienti di Microsoft Azure di accelerare la realizzazione degli obiettivi aziendali tramite il provisioning di un'architettura di base che può essere estesa per soddisfare eventuali ulteriori requisiti.

## <a name="overview"></a>Panoramica

Questo progetto Azure Security and Compliance Blueprint include linee guida e script di automazione per realizzare un'architettura di applicazione Web ospitata [piattaforma distribuita come servizio (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) di Microsoft Azure, appropriata per la gestione dei carichi di lavoro classificati come [UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Questa classificazione di sicurezza comprende la maggior parte delle informazioni create o elaborate dal settore pubblico. Sono inclusi i servizi e le attività aziendali di routine con potenziali conseguenze dannose in caso di smarrimento, furto o divulgazione. Il profilo di rischio tipico per la classificazione OFFICIAL è analogo a quello di un'azienda privata che fornisce servizi e informazioni di valore. La classificazione UK OFFICIAL tiene conto della necessità di difendere i dati o i servizi del governo del Regno Unito da minacce o compromissioni attuate da utenti malintenzionati con capacità e risorse delimitate, come (a titolo puramente esemplificativo) attivisti, gruppi di pressione monotematici, giornalisti investigativi, singoli pirati informatici competenti e la maggior parte dei singoli criminali e delle organizzazioni criminali.

Questo progetto è stato rivisto dal UK National Cyber Security Centre (NCSC) ed è conforme ai principi di sicurezza del cloud NCSC 14.

L'architettura usa componenti [PaaS](https://azure.microsoft.com/overview/what-is-paas/) di Azure per implementare un ambiente che consente ai clienti di evitare le spese e la complessità legate all'acquisto di licenze software, alla gestione dell'infrastruttura delle applicazioni sottostanti e del middleware o degli strumenti di sviluppo e di altre risorse. I clienti gestiscono le applicazioni e i servizi da loro sviluppati, con la possibilità di concentrarsi sull'offerta di valore aziendale e commerciale, mentre Microsoft Azure gestisce le altre risorse di Azure, ad esempio le macchine virtuali, l'archiviazione e la rete, caricando una parte maggiore della [suddivisione delle responsabilità](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) per la gestione dell'infrastruttura sulla piattaforma Azure. [Servizi app di Azure](https://azure.microsoft.com/services/app-service/) offre scalabilità automatica e disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatizzate da GitHub, Azure DevOps o qualsiasi repository Git come servizi predefiniti. Grazie a Servizi app, gli sviluppatori possono concentrarsi sulla generazione di valore aziendale, senza l'onere della gestione dell'infrastruttura. È possibile realizzare applicazioni Web innovative con Java, PHP, Node.js, Python, HTML o C# o anche eseguire la migrazione di applicazioni Web cloud o locali esistenti a Servizi app di Azure, sebbene occorra prevedere interventi attenti di collaudo e testing per verificare le prestazioni.

Questo progetto è incentrato sul provisioning di un'interfaccia basata sul Web [PaaS](https://azure.microsoft.com/overview/what-is-paas/) di base sicura per gli utenti del settore pubblico e anche per il back-office. Questo scenario di progettazione prende in considerazione l'uso di servizi Web ospitati in Azure, tramite i quali un utente pubblico può inviare, visualizzare e gestire i dati sensibili con sicurezza e che permettano anche a un operatore di back-office o di un ente pubblico di elaborare in modo sicuro i dati sensibili inviati dall'utente pubblico. I casi d'uso per questo scenario potrebbero includere:

- Un utente invia una dichiarazione dei redditi e un operatore della pubblica amministrazione elabora l'invio;
- Un utente richiede un servizio tramite un'applicazione basata su Web e un utente di back-office convalida ed eroga il servizio; oppure
- Un utente cerca e visualizza informazioni di dominio pubblico in merito a un servizio della pubblica amministrazione.

Usando modelli di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) e script dell'interfaccia della riga di comando di Azure, il progetto distribuisce un ambiente conforme ai [principi per la sicurezza cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) di UK National Cyber Security Centre (NCSC) 14 e ai [controlli di sicurezza critici](https://www.cisecurity.org/critical-controls.cfm) del Center for Internet Security (CIS). NCSC consiglia l'adozione dei principi per la sicurezza cloud da parte dei clienti per valutare le proprietà di sicurezza del servizio e per contribuire alla comprensione della suddivisione delle responsabilità tra cliente e fornitore. Microsoft ha reso disponibili informazioni relative a ogni principio per una migliore comprensione della suddivisione delle responsabilità. Questa architettura e i modelli corrispondenti di Azure Resource Manager sono supportati dal white paper Microsoft, [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1) (14 controlli di sicurezza del cloud per il cloud del Regno Unito con Microsoft Azure). Questa architettura è stata verificata dal centro NCSC e rispetta i principi per la sicurezza del cloud UK NCSC 14, consentendo quindi alle organizzazioni del settore pubblico di ottenere rapidamente l'idoneità ai requisiti di conformità tramite servizi basati sul cloud a livello globale e nel Regno Unito sul cloud Microsoft Azure. Questo modello distribuisce l'infrastruttura per il carico di lavoro. Il codice dell'applicazione e il software per il livello aziendale di supporto e per il livello dati devono essere installati e configurati dai clienti. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/ukofficial-paaswa-repo/).

Questo progetto è un'architettura di base. I clienti possono usare questo progetto come base per i carichi di lavoro basati sul Web con classificazione OFFICIAL ed espandere i modelli e le risorse in base ai propri requisiti. Questo progetto si basa sui principi del [progetto per applicazioni Web IaaS su tre livelli UK OFFICIAL](https://aka.ms/ukofficial-iaaswa) per offrire ai nostri clienti sia l'opzione [infrastruttura distribuita come servizio (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) che l'opzione PaaS come scelte di implementazione per l'hosting di carichi di lavoro basati sul Web.

Per distribuire questo progetto, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile iscriversi gratuitamente in modo semplice e rapido: Introduzione ad Azure. Fare clic [qui](https://aka.ms/ukofficial-paaswa-repo/) per le istruzioni di distribuzione.

## <a name="architecture-and-components"></a>Architettura e componenti

Questo progetto offre una soluzione di hosting di applicazione Web in un ambiente cloud di Azure che supporta carichi di lavoro UK OFFICIAL. L'architettura consente di implementare un ambiente protetto che sfrutta le funzionalità di piattaforma distribuita come servizio (PaaS) di Azure. All'interno dell'ambiente, vengono distribuite due app Web del servizio app (una per gli utenti pubblici e una per gli utenti del back-office), con un livello di app API per fornire i servizi aziendali per il front-end Web. Viene distribuito un database SQL di Azure come archivio dati relazionale gestito per l'applicazione. La connettività a questi componenti dall'esterno della piattaforma e tra tutti questi componenti è crittografata con TLS 1.2 per garantire la privacy dei dati durante il trasporto, con accesso autenticato da Azure Active Directory.

![Diagramma dell'architettura di riferimento dell'hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL](images/ukofficial-paaswa-architecture.png?raw=true "Diagramma dell'architettura di riferimento dell'hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL")

Come parte dell'architettura della distribuzione vengono distribuite anche funzionalità di provisioning dell'archiviazione sicura, monitoraggio e registrazione, gestione unificata della sicurezza e protezione avanzata dalle minacce, nonché funzionalità di gestione per garantire che i clienti dispongano di tutti gli strumenti necessari per proteggere e monitorare l'ambiente per questa soluzione.

Questa soluzione usa i servizi di Azure seguenti. Informazioni dettagliate sull'architettura di distribuzione sono disponibili nella sezione [Architettura di distribuzione](#deployment-architecture).

- Azure Active Directory
- Servizio app
- App Web
- App per le API
- DNS di Azure
- Key Vault
- Monitoraggio di Azure (log)
- Application Insights
- Azure Resource Manager
- Centro sicurezza di Azure
-  database SQL di Azure
- Archiviazione di Azure

## <a name="deployment-architecture"></a>Architettura di distribuzione

La sezione seguente descrive in modo dettagliato gli elementi di sviluppo e implementazione.

### <a name="security"></a>Security

#### <a name="identity-and-authentication"></a>Identità e autenticazione

Questo progetto assicura che l'accesso alle risorse sia protetto tramite i servizi di gestione di directory e identità. Questa architettura usa in modo completo l'[identità come perimetro di sicurezza](https://docs.microsoft.com/azure/security/security-paas-deployments). 

Le tecnologie seguenti offrono funzionalità di gestione delle identità nell'ambiente Azure:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti della soluzione sono stati creati in Azure Active Directory, inclusi gli utenti che accedono al database SQL.
- L'autenticazione per l'applicazione Web rivolta agli operatori e l'accesso per l'amministrazione delle risorse di Azure vengono eseguiti tramite Azure AD. Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- La crittografia delle colonne del database usa Azure AD per autenticare l'applicazione nel database SQL di Azure. Per altre informazioni, vedere [Always Encrypted: Proteggere i dati sensibili nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- L'applicazione Web rivolta ai cittadini è configurata per l'accesso pubblico. Per consentire la creazione e l'autenticazione degli account tramite provider Active Directory o di identità di social networking, è possibile integrare [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) se necessario.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) rileva le potenziali vulnerabilità e gli account rischiosi, offre raccomandazioni per ottimizzare il comportamento di sicurezza delle identità dell'organizzazione, consente di configurare risposte automatiche per le azioni sospette rilevate in relazione alle identità dell'organizzazione, ricerca la causa degli eventi sospetti e intraprende le azioni appropriate per risolverli.
- Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) consente un'accurata gestione degli accessi per Azure. L'accesso a una sottoscrizione è limitato all'amministratore della sottoscrizione stessa e l'accesso ad Azure Key Vault è limitato solo agli utenti che richiedono l'accesso per la gestione delle chiavi.
- Grazie alla possibilità di sfruttare l'[accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) i clienti possono applicare controlli di sicurezza aggiuntivi per l'accesso alle app e agli utenti nel loro ambiente, in base a condizioni specifiche come posizione, dispositivo, stato e rischio per l'accesso.
- [Protezione DDoS di Azure](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model), insieme alle procedure consigliate di progettazione delle applicazioni, offre un meccanismo di difesa dagli attacchi DDoS, con monitoraggio del traffico sempre attivo e la mitigazione in tempo reale degli attacchi comuni a livello di rete. Con un'architettura PaaS, la protezione DDoS a livello di piattaforma è trasparente per il cliente e incorporata nella piattaforma, ma è importante sottolineare che la responsabilità della progettazione della sicurezza dell'applicazione è a carico del cliente.

#### <a name="data-in-transit"></a>Dati in transito

I dati in transito dall'esterno e tra i componenti di Azure vengono protetti con [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), che usa la crittografia simmetrica basata su un segreto condiviso per crittografare le comunicazioni durante la trasmissione in rete. Per impostazione predefinita, il traffico di rete è protetto tramite TLS 1.2.

#### <a name="security-and-malware-protection"></a>Sicurezza e protezione dal malware

[Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) è un consulente cloud personalizzato che facilita l'applicazione delle procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) è una funzionalità di protezione in tempo reale che aiuta a identificare e rimuovere virus, spyware e altro software dannoso. Questa funzionalità viene installata per impostazione predefinita nell'infrastruttura di macchine virtuali PaaS sottostanti ed è gestita dall'infrastruttura di Azure in modo trasparente per il cliente.

### <a name="paas-services-in-this-blueprint"></a>Servizi PaaS in questo progetto

#### <a name="azure-app-service"></a>Servizio app di Azure

Servizio app di Azure rende disponibile un ambiente di hosting Web completamente gestito per le applicazioni Web sviluppate con Java, PHP, Node.js, Python, HTML e C# senza dover gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) o qualsiasi repository basato su Git.

Il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/) e consente l'autenticazione degli utenti con [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) o con account di accesso di social networking ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter) e [autenticazione Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)).

I piani Basic, Standard e Premium sono destinati a carichi di lavoro di produzione e vengono eseguiti in istanze dedicate delle macchine virtuali. Ogni istanza può supportare più applicazioni e domini. I servizi app supportano anche [restrizioni degli indirizzi IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) per proteggere il traffico verso indirizzi IP attendibili, se necessario, nonché [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) per la connessione sicura ad altri servizi PaaS, ad esempio [Key Vault](https://azure.microsoft.com/services/key-vault/) e [database SQL di Azure](https://azure.microsoft.com/services/sql-database/). Nei casi in cui è richiesta maggiore sicurezza, il piano Isolato ospita le app in un ambiente di Azure privato e dedicato ed è ottimale per le app che richiedono connessioni sicure alla rete locale o prestazioni e scalabilità aggiuntive.

Questo modello distribuisce le funzionalità seguenti del servizio app:

- Livello del piano di servizio app [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)
- Più [slot di distribuzione](https://docs.microsoft.com/azure/app-service/deploy-staging-slots) del servizio app: Dev, Preview, QA, UAT e naturalmente Production (slot predefinito).
- [Identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) per connettersi ad [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (questa funzionalità potrebbe essere usata anche per fornire l'accesso al [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) 
- Integrazione con [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) per monitorare le prestazioni
- [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- [Avvisi](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) delle metriche 
- [App per le API di Azure](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Database SQL di Azure

Il database SQL è un servizio gestito di database relazionale per utilizzo generico in Microsoft Azure che supporta strutture come dati relazionali, JSON, dati spaziali e XML. Il database SQL offre database SQL singoli gestiti, database SQL gestiti in un [pool elastico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) e [istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) di SQL (in anteprima pubblica). Questo servizio offre [prestazioni con scalabilità dinamica](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) e opzioni come gli [indici columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) per funzionalità di analisi e report avanzatissime e [OLTP in memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) per l'elaborazione XTP (Extreme Transaction Processing). Microsoft gestisce agevolmente tutte le operazioni di applicazione di patch e aggiornamento della base di codice SQL, rimuovendo tutte le attività di gestione dell'infrastruttura sottostante.

Database SQL di Azure in questo progetto

L'istanza di database SQL di Azure usa le misure di sicurezza del database seguenti:

- [Regole del firewall a livello di server e a livello di database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) o tramite [endpoint di servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regole della rete virtuale](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- La funzionalità [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) consente di proteggere il database SQL di Azure e Azure Data Warehouse dalla minaccia di attività dannose. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione.
- Con l'[autenticazione di Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni.
- Usare Azure Active Directory per l'amministrazione del database
- [Log di controllo](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) per gli account di archiviazione
- [Avvisi](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) delle metriche per le connessioni di database non riuscite
- [Rilevamento delle minacce a SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Colonne Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Archiviazione di Azure

[Archiviazione di Microsoft Azure](https://azure.microsoft.com/services/storage/) è un servizio cloud gestito da Microsoft che offre risorse di archiviazione con disponibilità, sicurezza, durabilità, scalabilità e ridondanza elevate. Archiviazione di Azure è costituito dai servizi di archiviazione BLOB, archiviazione file e archiviazione code.

#### <a name="azure-storage-in-this-blueprint"></a>Archiviazione di Azure in questo progetto

Questo modello usa i componenti di Archiviazione di Azure seguenti:

- [Crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Solo connessioni HTTPS consentite

#### <a name="data-at-rest"></a>Dati inattivi

Con la [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), tutti i dati scritti in Archiviazione di Azure vengono crittografati con la crittografia AES a 256 bit, una delle crittografie a blocchi più sicure tra quelle disponibili. È possibile usare le chiavi di crittografia gestite da Microsoft con SSE oppure le [proprie chiavi di crittografia](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Gli account di archiviazione possono essere protetti tramite [endpoint del servizio rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regole della rete virtuale](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Informazioni dettagliate sulla protezione di Archiviazione di Azure sono disponibili nella [guida alla sicurezza](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Gestione dei segreti

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) viene usato per proteggere le chiavi e i segreti delle applicazioni per garantire che non siano accessibili da terze parti. Key Vault non è progettato per essere usato come archivio per le password utente. Consente di creare più contenitori sicuri denominati insiemi di credenziali. Questi insiemi di credenziali sono supportati da moduli di protezione hardware. Gli insiemi di credenziali consentono di ridurre le probabilità di perdita accidentale di informazioni di sicurezza centralizzando l'archiviazione dei segreti delle applicazioni. Gli insiemi di credenziali delle chiavi controllano e registrano anche l'accesso a tutti gli elementi archiviati al loro interno. Azure Key Vault può gestire la richiesta e rinnovo dei certificati TLS (Transport Layer Security), offrendo le funzionalità necessarie per una soluzione affidabile di gestione del ciclo di vita dei certificati.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault in questo progetto

- Contiene la chiave di accesso per l'archiviazione, con accesso in lettura concesso all'[identità gestita](https://docs.microsoft.com/azure/app-service/overview-managed-identity) dell'app Web rivolta ai clienti
- Contiene la password dell'amministratore di database di SQL Server (in un insieme di credenziali separato)
- Registrazione diagnostica

### <a name="monitoring-logging-and-audit"></a>Monitoraggio, registrazione e controllo

#### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

[Log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) è un servizio di Azure che consente di raccogliere e analizzare i dati generati dalle risorse nel cloud e negli ambienti locali.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Monitoraggio di Azure log in questo progetto

- SQL Assessment
- Diagnostica dell'insieme di credenziali delle chiavi
- Connessione di Application Insights
- Azure Activity Log

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, Viene usato per monitorare le applicazioni Web in tempo reale per rilevare automaticamente le anomalie delle prestazioni, analizzare le prestazioni, diagnosticare problemi e raccogliere informazioni sulle interazioni degli utenti con l'app. Application Insights può essere distribuito su varie piattaforme, tra cui .NET, Node.js e Java EE, ospitate in locale o nel cloud. Si integra con il processo DevOps e offre punti di connessione per diversi altri strumenti di sviluppo.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights in questo progetto

Questo modello usa i componenti seguenti di Application Insights:

- Dashboard di Application Insights per ogni sito (operatore, cliente e API)

#### <a name="azure-activity-logs"></a>Log di attività di Azure

Il [log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) controlla gli eventi del piano di controllo per le sottoscrizioni. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche.

#### <a name="azure-monitor"></a>Monitoraggio di Azure

[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) permette il monitoraggio di base per i servizi di Azure consentendo la raccolta di metriche, log attività e log di diagnostica. Attualmente Monitoraggio di Azure offre log e metriche dell'infrastruttura di livello base per la maggior parte dei servizi in Microsoft Azure

## <a name="threat-model"></a>Modello di minaccia

Il diagramma di flusso di dati per questa architettura di riferimento è disponibile per il [download](https://aka.ms/ukofficial-paaswa-tm) o è riportato più avanti. Il modello può aiutare i clienti comprendere i punti di rischio potenziale nell'infrastruttura del sistema quando vengono apportate modifiche.

![Modello di minaccia dell'hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL](images/ukofficial-paaswa-threat-model.png?raw=true "Modello di minaccia dell'hosting di applicazioni Web PaaS per carichi di lavoro UK OFFICIAL")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentazione relativa alla conformità ai principi per la sicurezza cloud NCSC

Il Crown Commercial Service, un'agenzia che si impegna per migliorare le attività commerciali e di approvvigionamento da parte del governo, ha rinnovato la classificazione dei servizi cloud aziendali Microsoft specifici su G-Cloud v6, coprendo tutte le offerte Microsoft per il livello OFFICIAL. Per informazioni dettagliate su Azure e G-Cloud, vedere il [riepilogo della valutazione della sicurezza UK G-Cloud per Azure](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Questo progetto è conforme ai 14 principi per la sicurezza del cloud documentati nei [principi per la sicurezza del cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) di NCSC per contribuire a creare un ambiente che supporta carichi di lavoro classificati come UK OFFICIAL.

La cartella di lavoro di Excel [Azure Security and Compliance Blueprint - UK OFFICIAL Customer Responsibility Matrix](https://aka.ms/ukofficial-crm) (Azure Security and Compliance Blueprint - Matrice di responsabilità dei clienti UK OFFICIAL) elenca tutti i 14 principi per la sicurezza cloud e indica per ogni principio o sottoparte di principio se l'implementazione del principio è a carico di Microsoft, del cliente o è condivisa tra entrambi.

La cartella di lavoro di Excel [Azure Security and Compliance Blueprint - PaaS Web Application for UK OFFICIAL Principle Implementation Matrix](https://aka.ms/ukofficial-paaswa-pim) (Azure Security and Compliance Blueprint - Applicazione Web PaaS per la matrice di implementazione dei principi UK OFFICIAL) elenca tutti i 14 principi per la sicurezza cloud e indica per ogni principio o sottoparte di principio per cui è designata una responsabilità del cliente nella matrice di responsabilità dei clienti 1) se il progetto implementa il principio e 2) una descrizione del modo in cui l'implementazione risulta conforme ai requisiti del principio.  

Cloud Security Alliance (CSA) ha inoltre pubblicato la matrice di controllo cloud per supportare i clienti nella valutazione dei provider cloud e per identificare le domande per cui sono necessarie risposte prima del passaggio ai servizi cloud. Microsoft Azure ha a sua volta risposto al questionario CSA CAIQ ([CSA Consensus Assessment Initiative Questionnaire](https://www.microsoft.com/TrustCenter/Compliance/CSA)), che illustra il modo in cui Microsoft gestisce i principi suggeriti.

## <a name="third-party-assessment"></a>Valutazione di terze parti

Questo progetto è stato rivisto dal UK National Cyber Security Centre (NCSC) ed è conforme ai principi di sicurezza del cloud NCSC 14

I modelli di automazione sono stati testati dal team UK Customer Success Unit Azure Cloud Solution Architect e dal partner Microsoft [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Distribuire la soluzione

Questo progetto di automazione Azure Security and Compliance Blueprint è costituito da file di configurazione JSON e script di PowerShell gestiti dal servizio API di Azure Resource Manager per distribuire risorse in Azure. Istruzioni di distribuzione dettagliate sono disponibili [qui](https://aka.ms/ukofficial-paaswa-repo).

Per la distribuzione sono stati forniti tre approcci: un approccio semplice rapido con l'[interfaccia della riga di comando di Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) adatto per l'implementazione rapida di un ambiente di test; un approccio con parametri con l'[interfaccia della riga di comando di Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) che consente una maggiore configurazione per gli ambienti del carico di lavoro; e una distribuzione basata sul portale di Azure in cui l'operatore può specificare i parametri di distribuzione tramite il portale di Azure. 

1.  Clonare o scaricare [questo](https://aka.ms/ukofficial-paaswa-repo) repository GitHub nella workstation locale.
2.  Revisione [Metodo 1: interfaccia della riga di comando di Azure 2 (versione rapida)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) ed esecuzione dei i comandi specificati.
3.  Revisione [Metodo 1a: interfaccia della riga di comando di Azure 2 (configurazione della distribuzione tramite argomenti script)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) ed eseguire i comandi specificati
4.  Revisione [Metodo 2: processo di distribuzione con il portale di Azure](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) ed esecuzione dei comandi elencati

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="api-management"></a>Gestione API

È possibile usare [Gestione API di Azure](https://azure.microsoft.com/services/api-management/) insieme al servizio app API per fornire ulteriori livelli di sicurezza, limitazione delle richieste, controlli da esporre, proxy e API di protezione.

### <a name="azure-b2c"></a>Azure B2C

È possibile implementare [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) come controllo per consentire agli utenti di registrarsi, creare un'identità e abilitare l'autorizzazione e il controllo di accesso per l'applicazione Web pubblica.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

- Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
- Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
- I clienti possono copiare e usare questo documento per scopi di riferimento interni.
- Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
- Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
- Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.
