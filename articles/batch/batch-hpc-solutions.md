---
title: Soluzioni Batch e HPC nel cloud - Azure | Microsoft Docs
description: Informazioni sugli scenari Batch e High Performance Computing (HPC e Big Compute) e sulle opzioni per le soluzioni in Azure
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 32569d7e75a7a4ddee28041c0487ff158c20fd78
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Batch e soluzioni HPC per carichi di lavoro di elaborazione su larga scala

Azure offre soluzioni cloud scalabili ed efficienti per batch computing e high performance computing (HPC), dette anche *Big Compute*. Si possono leggere qui di seguito le informazioni relative a carichi di lavoro Big Compute e servizi di Azure per supportarli oppure passare direttamente agli [scenari di soluzioni](#scenarios) più avanti in questo articolo. L'articolo è destinato principalmente ai decision maker in ambito tecnico, ai responsabili IT e ai fornitori di software indipendenti, ma può essere usato da altri professionisti IT e sviluppatori per acquisire familiarità con queste soluzioni.

Le organizzazioni hanno problemi di elaborazione su larga scala: progettazione tecnica e analisi, rendering delle immagini, modellazione complessa, simulazioni Monte Carlo, calcoli di valutazione dei rischi finanziari e altri ancora. Azure consente alle organizzazioni di risolvere questi problemi in merito alle risorse, alla scalabilità e alla pianificazione necessarie. Con Azure, le organizzazioni possono:

* Creare soluzioni ibride, estendendo un cluster HPC locale per ripartire i carichi di lavoro di picco nel cloud
* Eseguire carichi di lavoro e strumenti di cluster HPC interamente in Azure
* Usare servizi di Azure gestiti e scalabili come [Batch](https://azure.microsoft.com/documentation/services/batch/) per eseguire carichi di lavoro a elevato utilizzo di risorse di calcolo senza dover distribuire e gestire un'infrastruttura di calcolo

Nonostante non rientri nell'ambito di questo articolo, Azure offre anche a sviluppatori e partner un set completo di funzionalità, opzioni di architettura e strumenti di sviluppo per creare flussi di lavoro Big Compute personalizzati su larga scala. Un ecosistema di partner in crescita è inoltre pronto a fornire suggerimenti per rendere produttivi i carichi di lavoro Big Compute nel cloud di Azure.

## <a name="batch-and-hpc-applications"></a>Applicazioni Batch e HPC
A differenza delle applicazioni Web e molte applicazioni line-of-business, le applicazioni Batch e HPC presentano un inizio e una fine definiti e possono essere eseguite in base a una pianificazione o su richiesta, talvolta per ore o anche di più. La maggior parte rientrano in due categorie principali: *intrinsecamente parallele* (talvolta denominate "imbarazzantemente parallele" perché i problemi vengono risolti dalle applicazioni stesse con esecuzioni in parallelo su più computer o processori) e *strettamente associate*. Per ulteriori informazioni su questi tipi di applicazioni, vedere la tabella seguente. Alcuni approcci a soluzioni Azure funzionano meglio per un tipo o l'altro

> [!NOTE]
> Nelle soluzioni Batch e HPC, un'istanza in esecuzione di un'applicazione viene in genere denominata *processo* e ogni processo può essere suddiviso in *attività*. Inoltre, le risorse di calcolo in cluster per l'applicazione vengono spesso denominate *nodi di calcolo*.
> 
> 

| Type | Caratteristiche | esempi |
| --- | --- | --- |
| **Intrinsecamente parallela**<br/><br/>![Intrinsecamente parallela][parallel] |• Singoli computer seguono la logica dell'applicazione in modo indipendente<br/><br/> • L'aggiunta di computer consente di ridimensionare l'applicazione e di ridurre i tempi di calcolo<br/><br/>• L'applicazione è costituita da file eseguibili separati oppure è suddivisa in un gruppo di servizi richiamati da un client (un'architettura orientata al servizio o applicazione SOA) |• Modellazione del rischio finanziario<br/><br/>• Rendering ed elaborazione di immagini<br/><br/>• Codifica e transcodifica multimediale<br/><br/>• Simulazioni Monte Carlo<br/><br/>• Test del Software |
| **Tightly coupled**<br/><br/>![Tightly coupled][coupled] |• L'applicazione richiede nodi di calcolo per interagire o scambiare risultati intermedi<br/><br/>• I nodi di calcolo possono comunicare tramite l'interfaccia MPI (Message Passing Interface), un protocollo di comunicazione comune per il calcolo parallelo<br/><br/>• L'applicazione è sensibile alla larghezza di banda e alla latenza di rete<br/><br/>• Le prestazioni dell'applicazione possono essere migliorate con tecnologie di rete ad alta velocità, come InfiniBand e l'accesso diretto a memoria remota (RDMA) |• Modellazione di serbatoi per petrolio e gas di <br/><br/>• Progettazione tecnica e analisi, ad esempio fluidodinamica computazionale<br/><br/>• Simulazioni fisiche, ad esempio incidenti stradali e reazioni nucleari<br/><br/>• Previsioni meteorologiche |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considerazioni per l'esecuzione di applicazioni Batch e HPC nel cloud
È possibile migrare facilmente molte applicazioni progettate per l'esecuzione nel cluster HPC in locale in Azure o in un ambiente ibrido (cross-premise). Tuttavia, vi possono essere alcune limitazioni o considerazioni, tra cui:

* **Disponibilità di risorse cloud** : a seconda del tipo di risorse di calcolo cloud usate, potrebbe risultare impossibile basarsi sulla disponibilità continua del computer mentre un processo è in esecuzione. La verifica dello stato di avanzamento e la gestione dello stato sono tecniche comuni per gestire possibili errori temporanei e maggiormente necessarie quando si usano risorse cloud.
* **Accesso ai dati** : le tecniche di accesso ai dati normalmente disponibili nei cluster aziendali, ad esempio NFS, possono richiedere una particolare configurazione nel cloud. Potrebbe anche essere necessario adottare procedure e modelli di accesso ai dati diversi per il cloud.
* **Spostamento di dati** : per le applicazioni che elaborano quantità elevate di dati sono necessarie strategie per spostare i dati nello spazio di archiviazione cloud e nelle risorse di calcolo. Potrebbe essere necessaria una rete cross-premise ad alta velocità, come [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Prendere inoltre in considerazione le limitazioni legali, normative o le limitazioni di criteri per l'archiviazione o l'accesso a tali dati.
* **Gestione delle licenze:** consultare il fornitore di qualsiasi applicazione commerciale per la gestione delle licenze o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo. Può essere necessario pianificare un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.

### <a name="big-compute-or-big-data"></a>Big Compute o Big Data?
La linea di demarcazione tra le applicazioni Big Compute e Big Data non è sempre chiara e alcune applicazioni possono avere le caratteristiche di entrambe. Entrambe prevedono l'esecuzione di calcoli su larga scala, in genere in cluster di computer. Tuttavia, gli approcci alla soluzione e gli strumenti di supporto possono essere diversi.

• **Big Compute** : tende a interessare applicazioni che si basano sulla potenza della CPU e memoria, ad esempio simulazioni tecniche, modellazione, valutazione dei rischi finanziari e rendering digitale. L'infrastruttura per una soluzione Big Compute potrebbe includere computer con processori multicore specializzati per eseguire il calcolo non elaborato e hardware di rete ad alta velocità specializzato per connettere i computer.

• **Big Data**: risolve i problemi di analisi dei dati che prevedono grandi quantità di dati che non possono essere gestite da un singolo computer o un sistema di gestione database, ad esempio volumi elevati di log Web o altri dati di business intelligence. Big Data si basa tendenzialmente sulla capacità del disco e sulle prestazioni di I/O più che sulla potenza della CPU. Sono disponibili anche strumenti Big Data specializzati, ad esempio Apache Hadoop, per gestire il cluster e la partizione dei dati. Per informazioni su Azure HDInsight e altre soluzioni di Azure Hadoop, vedere [Hadoop](https://azure.microsoft.com/solutions/hadoop/).

## <a name="compute-management-and-job-scheduling"></a>Gestione del calcolo e pianificazione dei processi
L'esecuzione di applicazioni Batch e HPC in genere include un'applicazione di *gestione cluster* e un'*utilità di pianificazione dei processi* per gestire le risorse di calcolo cluster e allocarle alle applicazioni che eseguono i processi. Queste funzioni possono essere eseguite da strumenti separati o da uno strumento o un servizio integrato.

* **Gestione cluster** : effettua il provisioning, rilascia e gestisce le risorse di calcolo (o nodi di calcolo). Un'applicazione di gestione cluster può automatizzare l'installazione di immagini del sistema operativo e applicazioni nei nodi di calcolo, aumentare le risorse di calcolo in base alle esigenze e monitorare le prestazioni dei nodi.
* **Utilità di pianificazione processi** : specifica le risorse (quali processori o memoria) richiesti da un'applicazione e le condizioni in base alle quali viene eseguita. Un'utilità di pianificazione processi mantiene una coda dei processi e alloca le risorse in base alle priorità assegnate o ad altre caratteristiche.

Anche gli strumenti per il clustering e la pianificazione dei processi per i cluster basati su Windows e su Linux consentono la migrazione senza problemi ad Azure. Ad esempio, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), la soluzione cluster di elaborazione gratuita di Microsoft per carichi di lavoro HPC Windows e Linux, offre diverse opzioni per l'esecuzione in Azure. È anche possibile creare cluster Linux per eseguire strumenti open source, ad esempio Torque e SLURM. In Azure è anche possibile usare soluzioni commerciali a griglia, ad esempio [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) e [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Come illustrato nelle sezioni seguenti, è anche possibile usufruire di servizi di Azure per gestire le risorse di calcolo e pianificare i processi senza i tradizionali strumenti di gestione cluster o in aggiunta a questi ultimi.

## <a name="scenarios"></a>Scenari
Ecco tre scenari comuni per eseguire carichi di lavoro Big Compute in Azure, usando le soluzioni cluster HPC esistenti, i servizi di Azure o una combinazione di entrambi. Sono elencate le considerazioni fondamentali per la scelta di ogni scenario, che però non sono esaustive. Altre informazioni sui servizi di Azure disponibili che si possono usare nella soluzione sono riportate più avanti in questo articolo.

| Scenario | Perché sceglierlo |
| --- | --- | --- |
| **Potenziamento di un cluster HPC in Azure**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Altre informazioni:<br/>• [Potenziamento in istanze del ruolo di lavoro di Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurare un cluster di elaborazione ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Potenziamento in Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Combinare [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) o un altro cluster locale con altre risorse di Azure in una soluzione ibrida.<br/><br/>• Estendere i carichi di lavoro Big Compute per l'esecuzione in istanze di macchine virtuali di piattaforma distribuita come servizio (PaaS), attualmente solo per Windows Server.<br/><br/>• Accedere a un archivio dati o un server licenze locale tramite una rete virtuale di Azure facoltativa |
| **Creare un cluster HPC interamente in Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Altre informazioni:<br/>• [Soluzioni cluster HPC in Azure](big-compute-resources.md)<br/><br/> |• Distribuire rapidamente e in modo coerente applicazioni e strumenti cluster in macchine virtuali di infrastruttura distribuita come servizio (IaaS) Windows o Linux standard o personalizzate.<br/><br/>• Eseguire diversi carichi di lavoro Big Compute usando la soluzione di pianificazione processi preferita.<br/><br/>• Usare servizi di Azure aggiuntivi, tra cui rete e archiviazione, per creare soluzioni basate sul cloud complete. |
| **Scalabilità orizzontale di un'applicazione parallela in Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Altre informazioni:<br/>• [Nozioni di base su Azure Batch](batch-technical-overview.md)<br/><br/>• [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) |• Eseguire lo sviluppo con [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) per aumentare il numero di istanze di vari carichi di lavoro Big Compute per l'esecuzione in pool di macchine virtuali Windows o Linux.<br/><br/>• Usare un servizio della piattaforma Azure per gestire la distribuzione e la scalabilità automatica delle macchine virtuali, la pianificazione dei processi, il ripristino di emergenza, lo spostamento dei dati, la gestione delle dipendenze e la distribuzione di applicazioni. |

## <a name="azure-services-for-big-compute"></a>Servizi di Azure per Big Compute
In questa sezione sono illustrati i servizi di calcolo, gestione dati, rete e servizi correlati che si possono combinare per i flussi di lavoro e le soluzioni Big Compute. Per informazioni dettagliate sui servizi di Azure, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/). Gli [scenari](#scenarios) descritti in precedenza in questo articolo illustrano solo alcune modalità d'uso di questi servizi.

> [!NOTE]
> Azure introduce regolarmente nuovi servizi che possono risultare utili per il proprio scenario. In caso di domande, contattare un [partner Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) o inviare un messaggio di posta elettronica all'indirizzo *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Servizi di calcolo
I servizi di calcolo di Azure sono alla base di una soluzione Big Compute e i diversi servizi offrono vantaggi per scenari diversi. A livello di base, questi servizi offrono modalità diverse alle applicazioni per l'esecuzione in istanze di calcolo basate su macchine virtuali fornite da Azure tramite la tecnologia Hyper-V di Windows Server. Queste istanze possono eseguire strumenti e sistemi operativi Linux e Windows standard e personalizzati. Azure offre un'ampia gamma di [dimensioni delle istanze](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) con diverse configurazioni di core CPU, memoria, capacità del disco e altre caratteristiche. In base alle proprie esigenze, è possibile ridimensionare le istanze a migliaia di core e quindi passare a un piano inferiore quando sono necessarie meno risorse.

> [!NOTE]
> Usare le [istanze a elevato calcolo, ad esempio la serie H](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) di Azure per migliorare le prestazioni e la scalabilità di carichi di lavoro HPC. Queste istanze supportano anche applicazioni MPI parallele che richiedono una rete di applicazione a bassa latenza e a velocità elevata. Sono anche disponibili le macchine virtuali di [serie N](../virtual-machines/windows/sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) con GPU NVIDIA per espandere l'intervallo di scenari di elaborazione e la visualizzazione in Azure.  
> 
> 

| Service | Descrizione |
| --- | --- |
| **[Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Forniscono un'infrastruttura distribuita come servizio (IaaS) di calcolo con tecnologia Microsoft Hyper-V<br/><br/>• Consentono di effettuare in modo flessibile il provisioning e la gestione di computer cloud permanenti basati su immagini standard di Windows Server o Linux provenienti da [Azure Marketplace](https://azure.microsoft.com/marketplace/) o immagini e dischi dati forniti dall'utente<br/><br/>• Possono essere distribuite e gestite come [set di scalabilità di macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) per creare servizi su larga scala da macchine virtuali identiche, con ridimensionamento automatico per aumentare o ridurre automaticamente la capacità<br/><br/>• Consentono l'esecuzione di applicazioni e strumenti cluster di elaborazione locali interamente nel cloud<br/><br/> |
| **[Servizi cloud](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Possono eseguire applicazioni Big Compute in istanze di ruolo di lavoro, ovvero macchine virtuali su cui è in esecuzione una versione di Windows Server e gestite completamente da Azure<br/><br/>• Consentono applicazioni scalabili e affidabili con costi generali amministrativi ridotti, eseguite secondo il modello PaaS (piattaforma distribuita come servizio)<br/><br/>• Potrebbero richiedere ulteriori strumenti o sviluppo per l'integrazione con soluzioni cluster HPC locali |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Esegue carichi di lavoro su larga scala in parallelo e in batch in un servizio completamente gestito<br/><br/>• Garantisce la pianificazione dei processi e il ridimensionamento automatico di un pool gestito di macchine virtuali<br/><br/>• Consente agli sviluppatori di compilare ed eseguire applicazioni come un servizio o abilitare applicazioni esistenti per il cloud<br/> |

### <a name="storage-services"></a>Servizi di archiviazione
In genere una soluzione Big Compute opera su un set di dati di input e genera dati per i relativi risultati. Tra i servizi di archiviazione di Azure usati in soluzioni Big Compute sono inclusi:

* [BLOB, tabelle e archiviazione delle code](https://azure.microsoft.com/documentation/services/storage/) : gestione di grandi quantità di dati non strutturati, dati NoSQL e messaggi per flusso di lavoro e comunicazione, rispettivamente. Ad esempio, è possibile usare l'archiviazione BLOB per set di dati tecnici di grandi dimensioni o per le immagini di input o i file multimediali elaborati dall'applicazione. È possibile utilizzare le code per la comunicazione asincrona in una soluzione. Vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).
* [Archiviazione file di Azure](https://azure.microsoft.com/services/storage/files/) : consente la condivisione di file e dati comuni in Azure con il protocollo SMB standard, necessario per alcune soluzioni cluster HPC.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) : fornisce un file system distribuito Apache Hadoop iperscalabile per il cloud, utile per analisi batch in tempo reale e interattive.

### <a name="data-and-analysis-services"></a>Servizi dati e analisi
Alcuni scenari di Big Compute implicano i flussi di dati su larga scala o generano i dati che necessitano di ulteriore elaborazione o analisi. Azure offre diversi servizi dati e analisi, tra cui:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) : consente la creazione di flussi di lavoro basati sui dati (pipeline) che uniscono, aggregano e trasformano dati di archivi dati locali, basati sul cloud e Internet.
* [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/) : fornisce le funzionalità principali di un sistema di gestione di database relazionali Microsoft SQL Server in un servizio gestito.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) : effettua la distribuzione e il provisioning di cluster Apache Hadoop basati su Linux o Windows Server nel cloud per la gestione, l'analisi e la creazione di report di Big Data.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) : consente di creare, testare, usare e gestire soluzioni di analisi predittiva in un servizio completamente gestito.

### <a name="additional-services"></a>Servizi aggiuntivi
La soluzione Big Compute può richiedere altri servizi di Azure per connettersi alle risorse locali o in altri ambienti. Tra gli esempi sono inclusi:

* [Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) : crea una sezione isolata logicamente in Azure per connettere le risorse di Azure tra loro o al data center locale. Con una rete virtuale cross-premise, le applicazioni Big Compute possono accedere ai dati locali, ai servizi Active Directory e ai server licenze.
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) : crea una connessione privata tra i data center Microsoft e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. ExpressRoute offre un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.
* [Bus di servizio](https://azure.microsoft.com/documentation/services/service-bus/) : sono disponibili diversi metodi per le applicazioni per comunicare o scambiare i dati, se si trovano in Azure, in un'altra piattaforma cloud o in un data center.

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni tecniche per la creazione della propria soluzione, vedere [Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)](big-compute-resources.md) .
* Esaminare le opzioni di Azure con partner, inclusi Cycle Computing e UberCloud.
* Ottenere informazioni sulle soluzioni Big Compute di Azure offerte da [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) e [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

