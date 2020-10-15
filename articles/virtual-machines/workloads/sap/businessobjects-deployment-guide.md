---
title: Distribuzione della piattaforma SAP BusinessObjects BI in Azure | Microsoft Docs
description: Pianificare, distribuire e configurare la piattaforma SAP BusinessObjects BI in Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 9d1759e5b809bc40e63fb6024fb7f107ad347da6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094356"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Guida alla pianificazione e all'implementazione di SAP BusinessObjects BI Platform in Azure

## <a name="overview"></a>Panoramica

Lo scopo di questa guida è fornire linee guida per la pianificazione, la distribuzione e la configurazione della piattaforma SAP BusinessObjects BI, nota anche come piattaforma SAP BOBI in Azure. Questa guida è destinata a comprendere i servizi e le funzionalità di Azure comuni pertinenti per la piattaforma BOBI di SAP. Questa guida non è un elenco completo di tutte le opzioni di configurazione possibili. Vengono illustrate le soluzioni comuni ai tipici scenari di distribuzione.

Questa guida non è destinata a sostituire le guide standard per l'installazione e l'amministrazione di SAP BOBI Platform, il sistema operativo o qualsiasi documentazione del database.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Pianificare e implementare la piattaforma SAP BusinessObjects BI in Azure

Microsoft Azure offre un'ampia gamma di servizi, tra cui calcolo, archiviazione, rete e molti altri, per consentire alle aziende di compilare le proprie applicazioni senza lunghi cicli di approvvigionamento. Macchine virtuali di Azure consente alle aziende di distribuire risorse di calcolo scalabili e su richiesta per diverse applicazioni SAP, ad esempio applicazioni basate su SAP NetWeaver, SAP hybris, piattaforma SAP BusinessObjects BI, in base alle esigenze aziendali. Azure supporta anche la connettività cross-premise, che consente alle aziende di integrare macchine virtuali di Azure nei propri domini locali, nei cloud privati e nel panorama del sistema SAP.

Questo documento fornisce indicazioni sulla pianificazione e sull'implementazione per la piattaforma SAP BusinessObjects BI in Azure. Integra la documentazione di installazione di SAP e le note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni di SAP BOBI.

### <a name="architecture-overview"></a>Panoramica dell'architettura

La piattaforma SAP BusinessObjects BI è un sistema autonomo che può esistere in una singola macchina virtuale di Azure o può essere ridimensionato in un cluster di molte macchine virtuali di Azure che eseguono componenti diversi. La piattaforma SAP BOBI è costituita da sei livelli concettuali: livello client, livello Web, livello di gestione, livello di archiviazione, livello di elaborazione e livello dati. Per ulteriori informazioni su ogni livello, consultare la guida dell'amministratore nel portale della guida della [piattaforma di Business Intelligence per SAP BusinessObjects](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) . Di seguito sono riportati i dettagli di alto livello su ogni livello:

- **Livello client:** Contiene tutte le applicazioni client desktop che interagiscono con la piattaforma BI per fornire diversi tipi di funzionalità di creazione di report, analisi e amministrazione.
- **Livello Web:** Contiene le applicazioni Web distribuite nei server applicazioni Web JAVA. Le applicazioni Web forniscono funzionalità della piattaforma BI agli utenti finali tramite un Web browser.
- **Livello di gestione:** Coordina e controlla tutti i componenti che rendono la piattaforma BI. Include il server di gestione centrale (CMS) e il server eventi e i servizi associati
- **Livello di archiviazione:** È responsabile della gestione dei file, ad esempio documenti e report. Gestisce inoltre la memorizzazione nella cache dei report per salvare le risorse di sistema quando l'utente accede ai report.
- **Livello di elaborazione:** Analizza i dati e produce report e altri tipi di output. Si tratta dell'unico livello che accede ai database contenenti dati del report.
- **Livello dati:** È costituito dai server di database che ospitano i database di sistema CMS e dall'archivio dati di controllo.

La piattaforma SAP BI è costituita da una raccolta di server in esecuzione in uno o più host. È essenziale scegliere la strategia di distribuzione corretta in base al dimensionamento, alle esigenze aziendali e al tipo di ambiente. Per installazioni di piccole dimensioni come lo sviluppo o il test, è possibile usare una singola macchina virtuale di Azure per il server applicazioni Web, il server di database e tutti i server della piattaforma BI. Nel caso in cui si usi l'offerta database-As-a-Service (DBaaS) da Azure, il server di database verrà eseguito separatamente da altri componenti. Per l'installazione di medie e grandi dimensioni, è possibile avere server in esecuzione in più macchine virtuali di Azure.

Nella figura seguente viene mostrata l'architettura della distribuzione su larga scala della piattaforma SAP BOBI in macchine virtuali di Azure, in cui ogni componente viene distribuito e inserito nei set di disponibilità che possono sostenere il failover in caso di problemi di servizio.

![Architettura della piattaforma SAP BusinessObjects BI in Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Dettagli architettura

- Bilanciamento del carico

  Nella distribuzione a istanze diverse di SAP BOBI, i server applicazioni Web (o il livello Web) sono in esecuzione in due o più host. Per distribuire il carico utente in modo uniforme tra i server Web, è possibile usare un servizio di bilanciamento del carico tra gli utenti finali e i server Web. In Azure è possibile usare [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) o [applicazione Azure gateway](../../../application-gateway/overview.md) per gestire il traffico verso i server Web.

- Server applicazioni Web

  Il server Web ospita le applicazioni Web di SAP BOBI Platform, ad esempio CMC e BI Launch Pad. Per ottenere la disponibilità elevata per il server Web, è necessario distribuire almeno due server applicazioni Web per gestire la ridondanza e il bilanciamento del carico. In Azure, questi server applicazioni Web possono essere posizionati nei set di disponibilità o nelle zone di disponibilità per una migliore disponibilità.

  Tomcat è l'applicazione Web predefinita per la piattaforma SAP BI. Per ottenere la disponibilità elevata per Tomcat, abilitare la replica della sessione usando [l'intercettore di appartenenza statico](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) in Azure. Assicura che l'utente possa accedere all'applicazione Web SAP BI anche quando il servizio Tomcat viene interrotta.

  > [!Important]
  > Per impostazione predefinita, Tomcat usa il multicast IP e la porta per il clustering, che non è supportato in Azure (Nota SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Server della piattaforma BI

  I server della piattaforma BI includono tutti i servizi che fanno parte dell'applicazione SAP BOBI (livello di gestione, livello di elaborazione e livello di archiviazione). Quando un server Web riceve una richiesta, rileva ogni server della piattaforma BI (in particolare, tutti i server CMS in un cluster) e bilancia automaticamente il carico delle richieste. In caso di errore di uno degli host della piattaforma BI, il server Web invia automaticamente le richieste ad altri host.

  Per ottenere la disponibilità elevata o la ridondanza per la piattaforma BI, è necessario distribuire l'applicazione in almeno due macchine virtuali di Azure. In base al ridimensionamento, è possibile ridimensionare la piattaforma di Business Intelligence per l'esecuzione in più macchine virtuali di Azure.

- Server repository file (FRS)

  Il server del repository di file contiene tutti i report e altri documenti di business intelligence che sono stati creati. Nella distribuzione a istanze multiple, i server della piattaforma BI sono in esecuzione in più macchine virtuali e ogni macchina virtuale deve avere accesso a questi report e ad altri documenti di business intelligence. Quindi, è necessario condividere un file System tra tutti i server della piattaforma BI.

  In Azure è possibile usare [file Premium di Azure](../../../storage/files/storage-files-introduction.md) o [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) per il server del repository di file. Entrambi i servizi di Azure hanno una ridondanza incorporata.

  > [!Important]
  > Il protocollo SMB per File di Azure è disponibile a livello generale, ma il supporto del protocollo NFS per File di Azure è attualmente in fase di anteprima. Per ulteriori informazioni, vedere il [supporto per NFS 4,1 per file di Azure è ora disponibile in anteprima](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- Database di controllo & CMS
  
  La piattaforma SAP BOBI richiede un database per archiviare i dati di sistema, a cui viene fatto riferimento come database CMS. Viene usato per archiviare le informazioni sulla piattaforma BI, ad esempio i dettagli relativi a utenti, server, cartelle, documenti, configurazione e autenticazione.

  Azure offre l'offerta database [MySQL](https://azure.microsoft.com/en-us/services/mysql/) e database [SQL di Azure](https://azure.microsoft.com/en-us/services/sql-database/) (DBaaS) che può essere usato per database CMS e database di controllo. Poiché si tratta di un'offerta PaaS, i clienti non devono preoccuparsi dell'operatività, della disponibilità e della manutenzione dei database. Il cliente può anche scegliere il proprio database per CMS e il repository di controllo in base alle esigenze aziendali.

## <a name="support-matrix"></a>Matrice di supporto

Questa sezione descrive il supporto di diversi componenti SAP BOBI come la versione della piattaforma SAP BusinessObjects BI, il sistema operativo e i database in Azure.

### <a name="sap-businessobjects-bi-platform"></a>Piattaforma SAP BusinessObjects BI

L'infrastruttura distribuita come servizio (IaaS) di Azure consente di distribuire e configurare la piattaforma SAP BusinessObjects BI in calcolo di Azure. Supporta la versione seguente della piattaforma SAP BOBI:

- Piattaforma SAP BusinessObjects BI 4,3
- Piattaforma SAP BusinessObjects BI Platform 4,2 SP04 +
- Piattaforma SAP BusinessObjects BI Platform 4,1 SP05 +

La piattaforma SAP BI viene eseguita su diversi sistemi operativi e database. È possibile trovare supporto della piattaforma SAP BOBI tra il sistema operativo e la versione del database nella [matrice di disponibilità del prodotto](https://apps.support.sap.com/sap/support/pam) per SAP Bobi.

### <a name="operating-system"></a>Sistema operativo

Azure supporta i sistemi operativi seguenti per la distribuzione della piattaforma SAP BusinessObjects BI.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

La versione del sistema operativo elencata in [Product Availability Matrix (PAM) per la piattaforma SAP BUSINESSOBJECTS bi](https://support.sap.com/pam) è supportata purché siano compatibili per l'esecuzione nell'infrastruttura di Azure.

### <a name="databases"></a>Database

La piattaforma BI richiede il database per CMS e l'archivio dati di controllo, che può essere installato in tutti i database supportati elencati nella [matrice di disponibilità del prodotto SAP](https://support.sap.com/pam) che include quanto segue:

- Microsoft SQL Server

- [Database SQL di Azure](https://azure.microsoft.com/en-us/services/sql-database/) (database supportato solo per la piattaforma SAP Bobi in Windows)

  Si tratta di un motore di database SQL Server completamente gestito, basato sulla più recente versione Enterprise stabile di SQL Server. Il database SQL di Azure gestisce la maggior parte delle funzioni di gestione del database, ad esempio l'aggiornamento, l'applicazione di patch e il monitoraggio senza coinvolgimento dell'utente. Con il database SQL di Azure è possibile creare un livello di archiviazione dei dati a disponibilità elevata e ad alte prestazioni per le applicazioni e le soluzioni in Azure. Per altri dettagli, vedere la documentazione del [database SQL di Azure](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) .

- [Database di Azure per MySQL](https://azure.microsoft.com/en-us/services/mysql/) (attenersi alle stesse linee guida per la compatibilità come indicato per MySQL AB in SAP PAM)

  Si tratta di un servizio di database relazionale basato su MySQL Community Edition. Essendo un'offerta di database distribuita come servizio (DBaaS) completamente gestita, è in grado di gestire carichi di lavoro cruciali con prestazioni prevedibili e scalabilità dinamica. Dispone di disponibilità elevata incorporata, backup automatici, applicazione di patch software, rilevamento automatico degli errori e ripristino temporizzato per un massimo di 35 giorni, che riducono sostanzialmente le attività operative. Per altri dettagli, vedere [la documentazione di database di Azure per MySQL](../../../mysql/overview.md) .

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (per la versione e la restrizione, vedere la nota SAP [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Questo documento illustra le linee guida per la distribuzione della **piattaforma SAP Bobi in Windows con il database SQL di Azure** e la **piattaforma SAP Bobi in Linux con database di Azure per MySQL**. È anche l'approccio consigliato per l'esecuzione di SAP BusinessObjects BI Platform in Azure.

## <a name="sizing"></a>Ridimensionamento

Il dimensionamento è un processo che consente di determinare i requisiti hardware necessari per eseguire l'applicazione in modo efficiente. Per la piattaforma SAP BOBI, il dimensionamento deve essere eseguito usando lo strumento di dimensionamento SAP denominato [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). Lo strumento fornisce i succhi di pagina in base all'input, che deve quindi essere mappato ai tipi di macchine virtuali di Azure certificate per SAP. Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) fornisce l'elenco dei prodotti SAP e dei tipi di VM di Azure supportati insieme a SAP. Per ulteriori informazioni sul dimensionamento, vedere [Guida alle dimensioni di SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Per le esigenze di archiviazione per la piattaforma SAP BOBI, Azure offre diversi tipi di [Managed disks](../../managed-disks-overview.md). Per la directory di installazione di SAP BOBI, è consigliabile usare un disco gestito Premium e per il database in esecuzione su macchine virtuali, seguire le indicazioni fornite nella [distribuzione DBMS per il carico di lavoro SAP](dbms_guide_general.md).

Azure supporta due offerte DBaaS per il livello dati della piattaforma SAP BOBI: [database SQL di Azure](https://azure.microsoft.com/en-us/services/sql-database) (applicazione bi in esecuzione in Windows) e [database di Azure per MySQL](https://azure.microsoft.com/en-us/services/mysql) (applicazione bi in esecuzione su Linux e Windows). Quindi, in base al risultato di ridimensionamento, è possibile scegliere di acquistare il modello più adatto alle proprie esigenze.

> [!Tip]
> Per informazioni di riferimento per il ridimensionamento rapido, prendere in considerazione 800 SAP = 1 vCPU durante il mapping del risultato di SAP del livello database della piattaforma SAP BOBI al database di Azure come servizio (database SQL di Azure o database di Azure per MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Modelli di ridimensionamento per il database SQL di Azure

Il database SQL di Azure offre i tre modelli di acquisto seguenti:

- Basato su vCore

  Consente di scegliere il numero di Vcore, la quantità di memoria e la quantità e la velocità di archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi. Questo modello è adatto per i clienti che hanno valore flessibilità, controllo e trasparenza.

  Sono disponibili tre [Opzioni del livello di servizio](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) nel modello vCore che includono-per utilizzo generico, business critical e iperscalabilità. Il livello di servizio definisce l'architettura di archiviazione, lo spazio, i limiti di I/O e le opzioni di continuità aziendale correlate alla disponibilità e al ripristino di emergenza. Di seguito sono riportati i dettagli di alto livello su ogni opzione del livello di servizio:

  1. **Per utilizzo generico** livello di servizio è ideale per i carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione orientate al budget, bilanciate e scalabili. Per altre informazioni, vedere [Opzioni e limiti delle risorse](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Business critical** livello di servizio offre alle applicazioni aziendali la massima resilienza agli errori usando diverse repliche isolate e fornisce le massime prestazioni di I/O per ogni replica di database. Per altre informazioni, vedere [Opzioni e limiti delle risorse](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. Il livello di servizio **iperscalabile** è ideale per i carichi di lavoro aziendali con requisiti di archiviazione e scalabilità a scalabilità elevata. Offre maggiore resilienza agli errori consentendo la configurazione di più di una replica di database isolata. Per altre informazioni, vedere [Opzioni e limiti delle risorse](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- Basato su DTU

  Il modello di acquisto basato su DTU offre una combinazione di risorse di calcolo, memoria e I/O in tre livelli di servizio, per supportare carichi di lavoro di database leggeri e pesanti. Le dimensioni di calcolo di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione. È ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.

  I [livelli di servizio](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) nel modello di acquisto basato su DTU si differenziano in base a una gamma di dimensioni di calcolo con una quantità fissa di archiviazione inclusa, un periodo di conservazione fisso di backup e un prezzo fisso.

- Senza server

  Il modello serverless ridimensiona automaticamente le risorse di calcolo in base alle esigenze dei carichi di lavoro, addebitando la quantità di risorse di calcolo usate al secondo. Il livello di calcolo senza Server sospende automaticamente i database durante i periodi di inattività quando vengono fatturate solo le risorse di archiviazione e riprende automaticamente i database al ritorno dell'attività. Per altre informazioni, vedere [Opzioni e limiti delle risorse](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  È più adatto per l'utilizzo intermittente e non prevedibile con un utilizzo medio di calcolo ridotto nel tempo. Questo modello può quindi essere usato per la distribuzione di SAP BOBI non di produzione.

> [!Note]
> Per SAP BOBI, è consigliabile usare il modello basato su vCore e scegliere per utilizzo generico o business critical livello di servizio in base alle esigenze aziendali.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Dimensionamento dei modelli per database di Azure per MySQL

Database di Azure per MySQL è dotato di tre diversi piani tariffari. Sono differenziate in base alla quantità di calcolo in Vcore, alla memoria per vCore e alla tecnologia di archiviazione usata per archiviare la data. Di seguito sono riportati i dettagli generali sulle opzioni e per altre informazioni sui diversi attributi, vedere il piano [tariffario](../../../mysql/concepts-pricing-tiers.md) per database di Azure per MySQL.

- Basic

  Viene usato per i carichi di lavoro di destinazione che richiedono un calcolo leggero e prestazioni di I/O.

- Utilizzo generico

  È adatto per la maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile.

- Con ottimizzazione per la memoria

  Per carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per un'elaborazione più rapida delle transazioni e una concorrenza più elevata.

> [!Note]
> Per SAP BOBI, è consigliabile usare per utilizzo generico o il piano tariffario con ottimizzazione per la memoria in base al carico di lavoro aziendale.

## <a name="azure-resources"></a>Risorse di Azure

### <a name="choosing-regions"></a>Scelta delle aree

L'area di Azure è una raccolta di data center che contiene l'infrastruttura da eseguire e ospita diversi servizi di Azure. Questa infrastruttura include un numero elevato di nodi che funzionano come nodi di calcolo o nodi di archiviazione oppure eseguono funzionalità di rete. Non tutte le aree offrono gli stessi servizi.

La piattaforma SAP BI contiene componenti diversi che potrebbero richiedere tipi di VM specifici, archiviazione come File di Azure o Azure NetApp Files o database come servizio (DBaaS) per il livello dati che potrebbe non essere disponibile in determinate aree. È possibile trovare le informazioni esatte sui tipi di VM, i tipi di archiviazione di Azure o altri servizi di Azure nei [prodotti disponibili in base](https://azure.microsoft.com/en-us/global-infrastructure/services/) al sito dell'area. Se i sistemi SAP sono già in esecuzione in Azure, probabilmente è stata identificata l'area. In tal caso, è necessario innanzitutto verificare che i servizi necessari siano disponibili in tali aree per decidere l'architettura della piattaforma SAP BI.

### <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono località separate fisicamente entro un'area di Azure. Ogni zona di disponibilità è costituita da uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti.

Per ottenere la disponibilità elevata in ogni livello per la piattaforma SAP BI, è possibile distribuire le macchine virtuali in una zona di disponibilità implementando un Framework a disponibilità elevata, che può fornire il migliore contratto di Service in Azure. Per il contratto di Service della macchina virtuale in Azure, controllare la versione più recente dei [contratti di Service per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Per il livello dati, il servizio database di Azure come servizio (DBaaS) fornisce un Framework a disponibilità elevata per impostazione predefinita. È sufficiente selezionare le funzionalità relative a disponibilità elevata, ridondanza e resilienza per l'area e il servizio per attenuare i tempi di inattività del database da interruzioni pianificate e non pianificate, senza che sia necessario configurare componenti aggiuntivi. Per altre informazioni sul contratto di contratto per l'offerta DBaaS supportata in Azure, vedere [disponibilità elevata in database di Azure per MySQL](../../../mysql/concepts-high-availability.md) e [disponibilità elevata per il database SQL di Azure](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Set di disponibilità

Il set di disponibilità è una funzionalità di raggruppamento logico per isolare le risorse delle macchine virtuali (VM) tra loro durante la distribuzione. Azure garantisce che le macchine virtuali inserite in un set di disponibilità vengano eseguite su più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. Se si verifica un errore hardware o software, solo un subset delle macchine virtuali è interessato e la soluzione complessiva rimane operativa. Quindi, quando le macchine virtuali vengono inserite nei set di disponibilità, il controller di infrastruttura di Azure distribuisce le VM in diversi domini di [errore](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) e di [aggiornamento](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) per evitare che tutte le macchine virtuali siano inaccessibili a causa della manutenzione dell'infrastruttura o dell'errore in un dominio di errore.

La piattaforma SAP BI contiene molti componenti diversi e durante la progettazione dell'architettura è necessario assicurarsi che ognuno di questi componenti sia resiliente a causa di eventuali problemi. È possibile ottenerlo inserendo le macchine virtuali di Azure di ogni componente nei set di disponibilità. Tenere presente che, quando si combinano macchine virtuali di diverse famiglie di macchine virtuali all'interno di un set di disponibilità, è possibile che si verifichino problemi che impediscono di includere un determinato tipo di VM in tale set di disponibilità. Quindi, avere un set di disponibilità separato per l'applicazione Web, l'applicazione BI per la piattaforma SAP BI, come evidenziato nella panoramica dell'architettura.

Anche il numero di domini di aggiornamento e di errore che possono essere usati da un set di disponibilità di Azure all'interno di un'unità di scala di Azure è finito. Quindi, se si continua ad aggiungere macchine virtuali a un singolo set di disponibilità, due o più macchine virtuali finiranno nello stesso dominio di errore o di aggiornamento. Per altre informazioni, vedere la sezione [set di disponibilità di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) del documento relativo alla pianificazione e implementazione di macchine virtuali di Azure per SAP.

Per comprendere il concetto di set di disponibilità di Azure e il modo in cui i set di disponibilità sono correlati ai domini di errore e di aggiornamento, vedere [Manage Availability](../../manage-availability.md) article.

> [!Important]
> I concetti di zone di disponibilità di Azure e di set di disponibilità di Azure si escludono a vicenda. Ciò significa che è possibile distribuire due o più macchine virtuali in una zona di disponibilità specifica o in un set di disponibilità di Azure, ma non in entrambi.

### <a name="virtual-machines"></a>Macchine virtuali

La macchina virtuale di Azure è un'offerta di servizio che consente di distribuire immagini personalizzate in Azure come istanze di infrastruttura distribuita come servizio (IaaS). Semplifica la manutenzione e la gestione delle applicazioni offrendo risorse di calcolo e di archiviazione su richiesta per ospitare, ridimensionare e gestire applicazioni Web e applicazioni connesse.

Azure offre varie macchine virtuali per tutte le esigenze dell'applicazione. Tuttavia, per il carico di lavoro SAP, Azure ha ridotto la selezione a famiglie di macchine virtuali diverse, adatte per carichi di lavoro SAP e carichi di lavoro SAP HANA più specificamente. Per informazioni dettagliate, vedere il [software SAP supportato per le distribuzioni di Azure](sap-supported-product-on-azure.md).

In base al dimensionamento della piattaforma SAP BI, è necessario eseguire il mapping del requisito alla macchina virtuale di Azure, supportata in Azure per il prodotto SAP. La nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) è un valido punto di partenza per elencare i tipi di VM di Azure supportati per i prodotti SAP in Windows e Linux. È anche importante tenere presente che, oltre la selezione dei tipi di VM puramente supportati, è anche necessario controllare se tali tipi di VM sono disponibili in un'area specifica. È possibile verificare la disponibilità di tipo di macchina virtuale nella pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/) . Per scegliere il modello di determinazione dei prezzi, è possibile fare riferimento alle [macchine virtuali di Azure per il carico di lavoro SAP](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Archiviazione

Archiviazione di Azure è un servizio cloud gestito da Azure che offre archiviazione a disponibilità elevata, sicura, durevole, scalabile e ridondante. Alcuni tipi di archiviazione hanno un uso limitato per gli scenari SAP. Tuttavia, molti tipi di archiviazione di Azure sono particolarmente adatti o ottimizzati per scenari specifici del carico di lavoro SAP. Per altre informazioni, vedere la guida relativa ai [tipi di archiviazione di Azure per il carico di lavoro SAP](planning-guide-storage.md) , in quanto evidenzia diverse opzioni di archiviazione adatte per SAP.

Archiviazione di Azure dispone di diversi tipi di archiviazione disponibili per i clienti e i dettagli per lo stesso possono essere letti nell'articolo [quali tipi di dischi sono disponibili in Azure?](../../disks-types.md) La piattaforma SAP BOBI usa l'archiviazione di Azure seguente per compilare l'applicazione:

- Dischi gestiti di Azure

  Si tratta di un volume di archiviazione a livello di blocco gestito da Azure. È possibile usare i dischi per i server e i database dell'applicazione della piattaforma SAP BOBI, quando sono installati in macchine virtuali di Azure. Sono disponibili diversi tipi di [Managed Disks di Azure](../../managed-disks-overview.md) , ma è consigliabile usare unità [SSD Premium](../../disks-types.md#premium-ssd) per l'applicazione e il database di SAP Bobi Platform.

  Nell'esempio seguente vengono usate le unità SSD Premium per la directory di installazione della piattaforma BOBI. Per il database installato nella macchina virtuale, è possibile usare Managed disks per i dati e il volume di log in base alle linee guida. I database CMS e di controllo sono in genere di piccole dimensioni e non hanno gli stessi requisiti di prestazioni di archiviazione di altri database SAP OLTP/OLAP.

- File Premium di Azure o Azure NetApp Files

  Nella piattaforma SAP BOBI, file repository server (FRS) si riferisce alle directory del disco in cui vengono archiviati contenuti come report, universi e connessioni che vengono usati da tutti i server applicazioni del sistema. [I file Premium di Azure](../../../storage/files/storage-files-introduction.md) o l'archiviazione [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) possono essere usati come file system condivisi per le applicazioni Bobi di SAP. Poiché questa offerta di archiviazione non è disponibile in tutte le aree, vedere [prodotti disponibili in base al sito dell'area](https://azure.microsoft.com/en-us/global-infrastructure/services/) per trovare informazioni aggiornate.

  Se il servizio non è disponibile nell'area geografica, è possibile creare un server NFS dal quale è possibile condividere il file system all'applicazione SAP BOBI. Tuttavia, sarà necessario prendere in considerazione anche la disponibilità elevata.

![Layout di archiviazione della piattaforma SAP BusinessObjects BI in Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Rete

SAP BOBI è una piattaforma di Business Intelligence per la creazione di report e analisi che non tiene i dati aziendali. Il sistema è quindi connesso ad altri server di database da cui recupera tutti i dati e fornisce informazioni agli utenti. Azure fornisce un'infrastruttura di rete che consente il mapping di tutti gli scenari che possono essere realizzati con la piattaforma SAP BI, come la connessione a un sistema locale, sistemi in una rete virtuale diversa e altro ancora. Per altre informazioni, vedere [Microsoft Azure rete per il carico di lavoro SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Per l'offerta di database come servizio, qualsiasi database appena creato (database SQL di Azure o database di Azure per MySQL) ha un firewall che blocca tutte le connessioni esterne. Per consentire l'accesso al servizio DBaaS dalle macchine virtuali della piattaforma BI, è necessario specificare una o più regole del firewall a livello di server per consentire l'accesso al server DBaaS. Per altre informazioni, vedere la sezione [regole firewall](../../../mysql/concepts-firewall-rules.md) per database di Azure per MySQL e [controlli di accesso alla rete](../../../azure-sql/database/network-access-controls-overview.md) per il database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuzione della piattaforma SAP BusinessObjects BI in Linux](businessobjects-deployment-guide-linux.md)
- [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP](planning-guide.md)
- [Distribuzione di Macchine virtuali di Azure per SAP](deployment-guide.md)
- [Distribuzione DBMS di Macchine virtuali di Azure per SAP](dbms-guide.md)