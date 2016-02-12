<properties
   pageTitle="Soluzioni Batch e HPC nel cloud | Microsoft Azure"
   description="Introduce i batch e scenari di elaborazione (Big Compute) ad alte prestazioni e soluzioni in Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/21/2016"
   ms.author="danlep"/>

# Soluzioni Batch e HPC nel cloud di Azure

Azure offre soluzioni cloud scalabili ed efficienti per batch computing e high performance computing (HPC), dette anche *Big Compute*. Si possono leggere qui di seguito le informazioni relative a carichi di lavoro Big Compute e servizi di Azure per supportarli oppure passare direttamente agli [scenari di soluzioni](#scenarios) più avanti in questo articolo. L'articolo è destinato principalmente ai decision maker in ambito tecnico, ai responsabili IT e ai fornitori di software indipendenti, ma può essere usato da altri professionisti IT e sviluppatori per acquisire familiarità con queste soluzioni.

Le organizzazioni hanno problemi di elaborazione su larga scala tra cui progettazione tecnica e analisi, rendering delle immagini, modellazione complessa, simulazioni Monte Carlo e calcoli di valutazione dei rischi finanziari. Azure consente alle organizzazioni di risolvere questi problemi e prendere decisioni in merito alle risorse, alle dimensioni e alla pianificazione necessarie. Con Azure, le organizzazioni possono:

* Creare soluzioni ibride, estendendo un cluster HPC locale per ripartire i carichi di lavoro di picco nel cloud

* Eseguire carichi di lavoro e strumenti di cluster HPC interamente in Azure

* Utilizzare un servizio gestito e scalabile di Azure quale [Batch](https://azure.microsoft.com/documentation/services/batch/) per eseguire carichi di lavoro complessi senza dover distribuire e gestire dell'infrastruttura di calcolo

Nonostante non rientri nell'ambito di questo articolo, Azure offre anche a sviluppatori e partner un set completo di funzionalità, opzioni di architettura e strumenti di sviluppo per creare flussi di lavoro Big Compute personalizzati su larga scala. Un ecosistema di partner in crescita è inoltre pronto a fornire suggerimenti per rendere produttivi i carichi di lavoro Big Compute nel cloud di Azure.


## Applicazioni Batch e HPC

A differenza delle applicazioni Web e molte applicazioni line-of-business, le applicazioni Batch e HPC presentano un inizio e una fine definiti e possono essere eseguite in base a una pianificazione o su richiesta, talvolta per ore o anche di più. La maggior parte rientrano in due categorie principali: *intrinsecamente parallele* (talvolta denominate "imbarazzantemente parallele" perché i problemi vengono risolti dalle applicazioni stesse con esecuzioni in parallelo su più computer o processori) e *strettamente associate*. Per ulteriori informazioni su questi tipi di applicazioni, vedere la tabella seguente. Alcuni approcci a soluzioni Azure funzionano meglio per un tipo o l'altro

>[AZURE.NOTE] Nelle soluzioni Batch e HPC, un'istanza in esecuzione di un'applicazione viene in genere denominata *processo* e ogni processo può essere suddiviso in *attività*. Inoltre, le risorse di calcolo in cluster per l'applicazione vengono spesso denominate *nodi di calcolo*.

Type | Caratteristiche | esempi
------------- | ----------- | ---------------
**Intrinsecamente parallela**<br/><br/>![Intrinsecamente parallela][parallel] |• Singoli computer seguono la logica dell'applicazione in modo indipendente<br/><br/>• L'aggiunta computer consente all'applicazione una migliore scalabilità e di ridurre i tempi di calcolo<br/><br/>• L'applicazione è costituita da file eseguibili separati oppure è suddivisa in un gruppo di servizi richiamati da un client (un'architettura orientata al servizio o applicazione SOA) |• Modellazione del rischio finanziario<br/><br/>• Rendering ed elaborazione delle immagini<br/><br/>• Codifica e transcodifica multimediale<br/><br/>• Simulazioni Monte Carlo<br/><br/>• Test del Software
**Strettamente associata**<br/><br/>![Strettamente][coupled] |• L'applicazione richiede nodi di calcolo per interagire o scambiare risultati intermedi<br/><br/>• I nodi di calcolo possono comunicare usando l'interfaccia MPI (Message Passing Interface), un protocollo di comunicazione comune per il calcolo parallelo<br/><br/>• L'applicazione è sensibile alla latenza di rete e alla larghezza di banda<br/><br/>• Le prestazioni dell'applicazione possono essere migliorate con tecnologie di rete ad alta velocità, come InfiniBand e l'accesso diretto a memoria remota (RDMA) |• Modellazione di serbatoi per petrolio e gas di <br/><br/>• Progettazione tecnica e analisi, ad esempio fluidodinamica computazionale<br/><br/>• Simulazioni fisiche, ad esempio incidenti stradali e reazioni nucleari<br/><br/>• Previsioni meteorologiche

### Considerazioni per l'esecuzione di applicazioni Batch e HPC nel cloud

È possibile migrare facilmente molte applicazioni progettate per l'esecuzione nel cluster HPC in locale in Azure o in un ambiente ibrido (cross-premise). Tuttavia, vi possono essere alcune limitazioni o considerazioni, tra cui:


* **Disponibilità di risorse cloud**: a seconda del tipo di risorse di calcolo cloud usate, potrebbe risultare impossibile basarsi sulla disponibilità continua del computer per la durata di esecuzione di un processo. La verifica dello stato di avanzamento e la gestione dello stato sono tecniche comuni per gestire possibili errori temporanei e maggiormente necessarie quando si usa risorse cloud.


* **Accesso ai dati**: tecniche di accesso ai dati comunemente disponibili in un cluster di rete aziendale, ad esempio NFS, possono richiedere una configurazione speciale nel cloud o potrebbe essere necessario adottare procedure di accesso ai dati e modelli diversi per il cloud.

* **Spostamento di dati**: per le applicazioni che elaborano quantità elevate di dati sono necessarie strategie per spostare i dati nello spazio di archiviazione cloud e nelle risorse di calcolo e potrebbe essere necessaria una rete cross-premise ad alta velocità, come [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Prendere inoltre in considerazione le limitazioni legali, normative o le limitazioni di criteri per l'archiviazione o l'accesso a tali dati.


* **Gestione delle licenze:** consultare il fornitore di qualsiasi applicazione commerciale per la gestione delle licenze o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo. Può essere necessario pianificare un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.


### Big Compute o Big Data?

La linea di demarcazione tra le applicazioni Big Compute e Big Data non è sempre chiara e alcune applicazioni possono avere le caratteristiche di entrambe. Entrambe prevedono l'esecuzione di calcoli su larga scala, in genere in cluster di computer. Tuttavia, gli approcci alla soluzione e gli strumenti di supporto possono essere diversi.

•**Big Compute**: tende a interessare applicazioni che si basano sulla potenza della CPU e memoria, ad esempio simulazioni tecniche, modellazione, valutazione dei rischi finanziari e rendering digitale. I cluster che supportano una soluzione Big Compute potrebbero includere i computer con processori multicore specializzati per eseguire il calcolo non elaborato e specializzato, hardware di rete ad alta velocità per connettere i computer.

• **Big Data**: risolve i problemi di analisi dei dati che prevedono grandi quantità di dati che non possono essere gestite da un singolo computer o un sistema di gestione database, ad esempio volumi elevati di log Web o altri dati di Business Intelligence. Big Data tende a basarsi più sulla capacità del disco e sulle prestazioni di I/O che sulla potenza della CPU e può usare strumenti specializzati, come Apache Hadoop, per gestire il cluster e partizionare i dati. Per informazioni su Azure HDInsight e altre soluzioni di Azure Hadoop, vedere [Hadoop](https://azure.microsoft.com/solutions/hadoop/).

## Gestione del calcolo e pianificazione dei processi

L'esecuzione di applicazioni Batch e HPC in genere include un'applicazione di *gestione cluster* e un'*utilità di pianificazione processi* per gestire le risorse di calcolo cluster e allocarle alle applicazioni che eseguono i processi. Queste funzioni possono essere eseguite da strumenti separati o da uno strumento o un servizio integrato.

* **Gestione cluster**: effettua il provisioning, rilascia e gestisce le risorse di calcolo (o nodi di calcolo). Un'applicazione di gestione cluster può automatizzare l'installazione di immagini del sistema operativo e applicazioni nei nodi di calcolo, aumentare le risorse di calcolo in base alle esigenze e monitorare le prestazioni dei nodi.

* **Utilità di pianificazione processi**: specifica le risorse (quali processori o memoria) richiesti da un'applicazione e le condizioni in base alle quali verrà eseguito. Un'utilità di pianificazione processi mantiene una coda dei processi e alloca le risorse in base alle priorità assegnate o ad altre caratteristiche.

Anche gli strumenti per il clustering e la pianificazione dei processi per i cluster basati su Windows e su Linux consentono la migrazione senza problemi ad Azure. Ad esempio, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), la soluzione cluster di elaborazione gratuita di Microsoft per carichi di lavoro HPC Windows e Linux, offre diverse opzioni per l'esecuzione in Azure. È anche possibile creare cluster Linux per eseguire strumenti open source, come Torque e SLURM, o eseguire strumenti commerciali, ad esempio [TIBCO DataSynapse GridServer](http://www.tibco.com/products/automation/application-development/grid-computing/gridserver) e [Univa Grid Engine](http://www.univa.com/products/grid-engine) in Azure.

Come illustrato nelle sezioni seguenti, è anche possibile usufruire di servizi di Azure per gestire le risorse di calcolo e pianificare i processi senza i tradizionali strumenti di gestione cluster o in aggiunta a questi ultimi.


## Scenari

Ecco tre scenari comuni per eseguire carichi di lavoro Big Compute in Azure, sfruttando le soluzioni cluster HPC esistenti, i servizi di Azure o una combinazione di entrambi. Sono elencate le considerazioni fondamentali per la scelta di ogni scenario, che però non sono esaustive. Altre informazioni sui servizi di Azure disponibili che si possono usare nella soluzione sono riportate più avanti in questo articolo.

 | Scenario | Perché sceglierlo
------------- | ----------- | ---------------
**Potenziamento di un cluster HPC in Azure**<br/><br/>[![Potenziamento dei cluster][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Altre informazioni:<br/>• [Potenziamento in Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurare un cluster di elaborazione ibrido con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>|• Combinare il cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) locale con risorse di Azure aggiuntive in una soluzione ibrida.<br/><br/>• Estendere i carichi di lavoro Big Compute per l'esecuzione in istanze di macchine virtuali di piattaforma distribuita come servizio (PaaS), attualmente solo per Windows Server.<br/><br/>• Accedere a un archivio dati o un server licenze locale tramite una rete virtuale di Azure facoltativa.|• È disponibile un cluster HPC Pack esistente e sono necessarie altre risorse <br/><br/>• Non si vuole acquistare e gestire un'infrastruttura cluster HPC aggiuntiva<br/><br/>• Si verificano periodi di picco temporanei o si hanno progetti speciali 
**Creare un cluster HPC interamente in Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Altre informazioni:<br/>• [Soluzioni cluster HPC in Azure](./big-compute-resources.md)<br/><br/>|• Distribuire rapidamente e in modo coerente applicazioni e strumenti cluster in macchine virtuali di infrastruttura distribuita come servizio (IaaS) Windows o Linux standard o personalizzate.<br/><br/>• Eseguire una vasta gamma di carichi di lavoro Big Compute usando la soluzione di pianificazione processi preferita.<br/><br/>• Usare servizi di Azure aggiuntivi, tra cui rete e archiviazione, per creare soluzioni complete basate sul cloud. |Non si vuole acquistare e gestire un'infrastruttura cluster HPC Windows o Linux aggiuntiva •<br/><br/>• Si verificano periodi di picco temporanei o di hanno progetti speciali<br/><br/>• È necessario un cluster aggiuntivo per un periodo di tempo, ma non si vuole investire in computer e spazio per la distribuzione<br/><br/>• Si vuole trasferire l'applicazione a elevato utilizzo di calcolo, per eseguirla come servizio interamente nel cloud
**Ridimensionare un'applicazione parallela in Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Altre informazioni:<br/>• [Nozioni di base su Azure Batch](./batch-technical-overview.md)<br/><br/>• [Introduzione alla libreria di Azure Batch per .NET](./batch-dotnet-get-started.md)|• Sviluppare con le API [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) per ridimensionare un'ampia gamma di carichi di lavoro Big Compute per l'esecuzione in pool di macchine virtuali di piattaforma distribuita come servizio (PaaS), attualmente solo per Windows Server.<br/><br/>• Usare un servizio di Azure per gestire la distribuzione e il ridimensionamento automatico delle macchine virtuali, la pianificazione dei processi, il ripristino di emergenza, lo spostamento dei dati, la gestione delle dipendenze e la distribuzione di applicazioni, senza richiedere un cluster HPC o un'utilità di pianificazione processi separata.|• Non si vogliono gestire risorse di calcolo o un'utilità di pianificazione processi, ma al contrario ci si vuole concentrare sull'esecuzione delle applicazioni<br/><br/>• Si vuole trasferire l'applicazione a elevato utilizzo di calcolo per eseguirla come servizio nel cloud<br/><br/>• Si vogliono ridimensionare automaticamente le risorse di calcolo in modo che corrispondano al carico di lavoro di calcolo 


## Servizi di Azure per Big Compute

In questa sezione sono illustrati i servizi di calcolo, gestione dati, rete e servizi correlati che si possono combinare per i flussi di lavoro e le soluzioni Big Compute. Per informazioni dettagliate su ognuno di questi servizi, vedere la [documentazione](https://azure.microsoft.com/documentation/) relativa ai servizi di Azure. Gli [scenari](#scenarios) descritti in precedenza in questo articolo illustrano solo alcune modalità d'uso di questi servizi.

>[AZURE.NOTE] Azure introduce regolarmente nuovi servizi che possono risultare utili per il proprio scenario. In caso di domande, contattare un [partner Azure](https://pinpoint.microsoft.com/it-IT/search?keyword=azure) o inviare un messaggio di posta elettronica **bigcompute@microsoft.com*.

### Servizi di calcolo

I servizi di calcolo di Azure sono alla base di una soluzione Big Compute e i diversi servizi offrono vantaggi per scenari diversi. A livello di base, questi servizi offrono modalità diverse alle applicazioni per l'esecuzione in istanze di calcolo basate su macchine virtuali fornite da Azure tramite la tecnologia Hyper-V di Windows Server. Queste istanze possono eseguire un'ampia gamma di strumenti e sistemi operativi Linux e Windows standard e personalizzati. Azure offre un'ampia gamma di [dimensioni delle istanze](../virtual-machines/virtual-machines-size-specs.md) con diverse configurazioni di core CPU, memoria, capacità del disco e altre caratteristiche. In base alle proprie esigenze è possibile ridimensionare le istanze a migliaia di core e quindi ridurle quando sono necessarie meno risorse.

>[AZURE.NOTE] È possibile sfruttare le istanze A8-A11 per migliorare le prestazioni di alcuni carichi di lavoro HPC, tra cui applicazioni MPI parallele che richiedono una rete di applicazioni a bassa latenza e velocità effettiva elevata. Vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Servizio | Descrizione
------------- | -----------
**[Servizi cloud](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Possono eseguire applicazioni Big Compute in istanze di ruolo di lavoro, ovvero macchine virtuali su cui è in esecuzione una versione di Windows Server e gestite completamente da Azure<br/><br/>• Promuovono l'utilizzo di applicazioni scalabili e affidabili con sovraccarico amministrativo ridotto, in esecuzione in un modello PaaS (Platform as-a Service)<br/><br/>• Potrebbero richiedere ulteriori strumenti o sviluppo per l'integrazione con soluzioni cluster HPC locali
**[Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Forniscono potenza di calcolo IaaS (Infrastructure as-a Service) mediante la tecnologia Microsoft Hyper-V<br/><br/>• Consentono di eseguire il provisioning e gestire computer cloud permanenti basati su immagini Windows Server o Linux standard, altre immagini e dischi di dati o provenienti dall'[Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Permettono l'esecuzione di strumenti cluster di elaborazione locali e di applicazioni interamente nel cloud
**[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Esegue carichi di lavoro su larga scala in parallelo e in batch in un servizio completamente gestito<br/><br/>• Garantisce la pianificazione dei processi e il ridimensionamento automatico di un pool di macchine virtuali gestito<br/><br/>• Consente agli sviluppatori di compilare ed eseguire applicazioni come un servizio o abilitare applicazioni esistenti per il cloud<br/>

### Servizi di archiviazione

In genere una soluzione Big Compute opera su un set di dati di input e genera dati per i relativi risultati. Tra i servizi di archiviazione di Azure usati in soluzioni Big Compute sono inclusi:

* [BLOB, tabelle e archiviazione delle code](https://azure.microsoft.com/documentation/services/storage/): gestione di grandi quantità di dati non strutturati, dati NoSQL e messaggi per flusso di lavoro e comunicazione, rispettivamente. Ad esempio, è possibile utilizzare l'archiviazione BLOB per grandi set di dati tecnici o le immagini di input o i file multimediali per i processi applicativi. È possibile utilizzare le code per la comunicazione asincrona in una soluzione. Vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).

* [File di archiviazione Azure](https://azure.microsoft.com/services/storage/files/): condivisioni di file e dati in Azure utilizzando il protocollo SMB standard, necessario per alcune soluzioni cluster HPC comuni.

### Servizi dati e analisi

Alcuni scenari di Big Compute implicano i flussi di dati su larga scala o generano i dati che necessitano di ulteriore elaborazione o analisi. Per gestire questa situazione, Azure offre una serie di servizi dati e l'analisi, tra cui:

* [Data factory](https://azure.microsoft.com/documentation/services/data-factory/): consente la creazione di flussi di lavoro basati sui dati (pipeline) che uniscono, aggregano e trasformano dati da archivi dati Internet, locali e basati sul cloud.

* [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/): fornisce le funzionalità principali di un sistema di gestione di database relazionali Microsoft SQL Server in un servizio gestito.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): distribuisce ed effettua il provisioning di cluster di Windows Server o Apache Hadoop basati su Linux nel cloud per gestire, analizzare e creare report su Big Data.

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/): consente di creare, testare, usare e gestire soluzioni di analisi predittiva in un servizio completamente gestito.

### Servizi aggiuntivi

La soluzione Big Compute può richiedere altri servizi di Azure per connettersi alle risorse locali o in altri ambienti. Tra gli esempi sono inclusi:

* [Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/): crea una sezione logicamente isolata in Azure per connettere le risorse di Azure al data center locale o un singolo computer client tramite IPSec; consente alle applicazioni Big Compute di accedere ai dati locali, ai servizi di Active Directory e ai server licenze

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/): crea una connessione privata tra data center Microsoft e l'infrastruttura locale o in un ambiente di condivisione percorso con una maggiore sicurezza, maggiore affidabilità, maggiore velocità e minori latenze rispetto alle connessioni tipiche su Internet.

* [Bus di servizio](https://azure.microsoft.com/documentation/services/service-bus/): sono disponibili diversi metodi per le applicazioni per comunicare o scambiare i dati, se si trovano in Azure, in un'altra piattaforma cloud o in un data center.

## Passaggi successivi

* Vedere [Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)](big-compute-resources.md) per trovare informazioni tecniche aggiuntive per la compilazione della soluzione.

* Discutere le opzioni di Azure con i partner inclusi Cycle Computing e UberCloud.

* Ottenere informazioni sulle soluzioni Big Compute di Azure offerte da [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/) e [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=AcomDC_0204_2016-->