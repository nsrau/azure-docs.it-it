<properties
   pageTitle="Batch e le soluzioni HPC nel Cloud | Microsoft Azure"
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
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Batch e soluzioni HPC

In questo articolo vengono presentate le soluzioni di Azure per Batch e HPC (High Performance Computing), note anche come *Big Compute*. Anche se l'articolo è principalmente destinato ai responsabili delle decisioni tecniche, ai responsabili IT e ai fornitori di software indipendenti, altri professionisti IT e sviluppatori possono utilizzarlo per acquisire familiarità con queste soluzioni.

Le organizzazioni hanno problemi di elaborazione su larga scala tra cui progettazione tecnica e analisi, rendering delle immagini, modellazione complessa, simulazioni Monte Carlo e calcoli di valutazione dei rischi finanziari. Per risolvere questi problemi e prendere decisioni con le risorse, la scalabilità e la pianificazione necessarie, Azure offre funzionalità e servizi di calcolo scalabili e su richiesta. Con Azure, le organizzazioni possono:

* Creare soluzioni ibride, estendendo un cluster HPC locale per ripartire i carichi di lavoro di picco nel cloud

* Eseguire i carichi di lavoro cluster HPC interamente in Azure utilizzando il set di strumenti e le applicazioni esistenti

* Utilizzare un servizio gestito e scalabile di Azure quale [Batch](http://azure.microsoft.com/documentation/services/batch/) per eseguire carichi di lavoro complessi senza dover distribuire e gestire dell'infrastruttura di calcolo

Azure offre inoltre strumenti di sviluppo e i servizi per le organizzazioni e fornitori di software per creare soluzioni Big Compute personalizzate ed end-to-end.


## Background: applicazioni Batch e HPC

A differenza delle applicazioni Web e molte applicazioni line-of-business, le applicazioni Batch e HPC presentano un inizio e una fine definiti e possono essere eseguite in base a una pianificazione o su richiesta, talvolta per ore o anche di più. La maggior parte rientrano in due categorie principali: *intrinsecamente parallele* (talvolta denominate "imbarazzantemente parallele" perché i problemi vengono risolti dalle applicazioni stesse con esecuzioni in parallelo su più computer o processori) e *strettamente associate*. Per ulteriori informazioni su questi tipi di applicazioni, vedere la tabella seguente. Alcuni approcci a soluzioni Azure funzionano meglio per un tipo o l'altro

>[AZURE.NOTE]Nelle soluzioni Batch e HPC, un'istanza in esecuzione di un'applicazione viene in genere denominata *processo* e ogni processo può essere suddiviso in *attività*. Inoltre, le risorse di calcolo in cluster per l'applicazione vengono spesso denominate *nodi di calcolo*.

Type | Caratteristiche | esempi
------------- | ----------- | ---------------
**Intrinsecamente parallela**<br/><br/>![Intrinsecamente parallela][parallel] |• Singoli computer seguono la logica dell'applicazione in modo indipendente<br/><br/>• L'aggiunta computer consente all'applicazione una migliore scalabilità e di ridurre i tempi di calcolo<br/><br/>• L'applicazione è costituita da file eseguibili separati oppure è suddivisa in un gruppo di servizi richiamati da un client (un'architettura orientata al servizio o applicazione SOA) |• Modellazione del rischio finanziario<br/><br/>• Rendering ed elaborazione delle immagini<br/><br/>• Codifica e transcodifica multimediale<br/><br/>• Simulazioni Monte Carlo<br/><br/>• Test del Software
**Strettamente associata**<br/><br/>![Strettamente][coupled] |• L'applicazione richiede nodi di calcolo per interagire o scambiare i risultati intermedi<br/><br/>• I nodi di calcolo possono comunicare utilizzando l'interfaccia MPI (Message Passing), un protocollo di comunicazione comune per il calcolo parallelo<br/><br/>• L'applicazione è sensibile alla latenza di rete e alla larghezza di banda<br/><br/>• Le prestazioni dell'applicazione possono essere migliorate tramite l'infrastruttura di calcolo che supporta le tecnologie di rete ad alta velocità come InfiniBand e l'accesso diretto a memoria remota (RDMA) |• Modellazione di serbatoi per petrolio e gas di <br/><br/>• Progettazione tecnica e analisi, ad esempio fluidodinamica computazionale<br/><br/>• Simulazioni fisiche, ad esempio incidenti stradali e reazioni nucleari<br/><br/>• Previsioni meteorologiche

### Considerazioni per l'esecuzione di applicazioni Batch e HPC nel cloud

È possibile migrare facilmente molte applicazioni progettate per l'esecuzione nel cluster HPC in locale in Azure o in un ambiente ibrido (cross-premise). Tuttavia, vi possono essere alcune limitazioni o considerazioni, tra cui:


* **Disponibilità ininterrotta di risorse cloud** - A seconda del tipo di risorse di calcolo cloud selezionate per la soluzione, non è possibile basarsi sulla disponibilità continua del computer per la durata dell'esecuzione di un processo. La verifica dello stato di avanzamento e la gestione dello stato sono tecniche comuni per gestire possibili errori temporanei e maggiormente necessarie quando si usa risorse cloud.


* **Accesso ai dati**: tecniche di accesso ai dati comunemente disponibili in un cluster di rete aziendale, ad esempio NFS, possono richiedere una configurazione speciale nel cloud o potrebbe essere necessario adottare procedure di accesso ai dati e modelli diversi per il cloud.

* **Spostamento di dati**: per le applicazioni che elaborano quantità elevate di dati, sono necessarie strategie per spostare i dati nelle risorse di archiviazione del cloud e nelle risorse di calcolo, nonché potrebbe essere necessario prendere in considerazione reti ad alta velocità cross-premise, quali [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/). Prendere inoltre in considerazione le limitazioni legali, normative o le limitazioni di criteri per l'archiviazione o l'accesso a tali dati.


* **Gestione delle licenze:** consultare il fornitore di qualsiasi applicazione commerciale per la gestione delle licenze o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze prepagate. Potrebbe essere necessario pianificare per un server di gestione licenze nel cloud per la soluzione o una connessione a un server licenze locale.


### Big Compute o Big Data?

Le linee di demarcazione tra le applicazioni Big Compute e Big Data non sempre sono chiare e alcune applicazioni possono presentare entrambi le caratteristiche. Entrambi i tipi prevedono l'esecuzione di calcoli su larga scala, in genere nei cluster di computer che eseguono in locale nel cloud o in entrambi. Tuttavia, gli approcci alla soluzione e gli strumenti di supporto potrebbero essere diversi.

•**Big Compute**: tende a interessare applicazioni che si basano sulla potenza della CPU e memoria, ad esempio simulazioni tecniche, modellazione, valutazione dei rischi finanziari e rendering digitale. I cluster che supportano una soluzione Big Compute potrebbero includere i computer con processori multicore specializzati per eseguire il calcolo non elaborato e specializzato, hardware di rete ad alta velocità per connettere i computer.

• **Big Data**: risolve i problemi di analisi dei dati che prevedono grandi quantità di dati che non possono essere gestite da un singolo computer o un sistema di gestione database, ad esempio volumi elevati di log Web o altri dati di Business Intelligence. Big Data tende a basarsi sempre più sulla capacità del disco e sulle prestazioni I/O rispetto alla potenza della CPU e una soluzione Big Data utilizza spesso strumenti specifici come Apache Hadoop per gestire il cluster e per partizionare i dati. Per informazioni su Azure HDInsight e altre soluzioni di Azure Hadoop, vedere [Hadoop](http://azure.microsoft.com/solutions/hadoop/).

## Gestione delle risorse e pianificazione dei processi

L'esecuzione di applicazioni Batch e HPC in genere include un'applicazione di *gestione cluster* e di un'*utilità di pianificazione processi* per gestire le risorse di cluster di calcolo e allocarle alle applicazioni che eseguono i processi. Queste funzioni potrebbero essere eseguite da strumenti separati o da uno strumento integrato.

* **Gestione cluster**: effettua il provisioning, rilascia e gestisce le risorse di calcolo (o nodi di calcolo). A seconda dello strumento, un'applicazione di gestione cluster potrebbe automatizzare l'installazione di immagini del sistema operativo e applicazioni nei nodi di calcolo, aumentare le risorse di calcolo in base alle esigenze e monitorare le prestazioni dei nodi.

* **Utilità di pianificazione processi**: specifica le risorse (quali processori o memoria) richiesti da un'applicazione e le condizioni in base alle quali verrà eseguito. Un'utilità di pianificazione processi mantiene una coda dei processi e alloca le risorse in base alle priorità assegnate o ad altre caratteristiche.

Anche gli strumenti per il clustering e per la pianificazione processi per cluster Windows e Linux o quelli sviluppati in modo indipendente, consentono la migrazione ad Azure. Ad esempio, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) è soluzione cluster di calcolo gratuita di Microsoft per computer basati su Windows e Windows Server. Per ridurre la necessità di risorse di calcolo locali dedicate, è possibile estendere un cluster HPC Pack per l'utilizzo di nodi di calcolo di Azure su richiesta o la distribuzione di un cluster completamente in macchine virtuali di Azure.

### Flussi di lavoro big Compute

La gestione del cluster e gli strumenti di pianificazione processi consentono di eseguire un flusso di lavoro Big Compute con passaggi prevedibili. A seconda della soluzione, è possibile omettere alcuni passaggi riportati di seguito o introdurre strumenti aggiuntivi personalizzati. I flussi di lavoro con utilizzo intensivi di dati può comportare passaggi di pre- e post-elaborazione di dati aggiuntivi (non elencati).

1. **Provisioning**: preparazione dell'ambiente di calcolo con le risorse di calcolo e l'infrastruttura necessarie, nonché le risorse di archiviazione per eseguire le applicazioni.

2. **Fase temporanea**: spostamento dei dati di input e delle applicazioni nell'ambiente di calcolo

3. **Pianificazione**: configurazione dei processi, delle attività e allocazione delle relative risorse di calcolo, quando disponibili

4. **Monitoraggio**: fornisce informazioni sullo stato di avanzamento di attività e processi e consente di gestire errori o eccezioni

5. **Fine**: visualizzazione dei risultati e post-elaborazione dei dati di output, se necessario

## Servizi di Azure per Big Compute

Azure offre una gamma di servizi di calcolo, gestione dati, reti e servizi correlati che è possibile utilizzare per i flussi di lavoro e le soluzioni Big Compute. Per informazioni dettagliate su ognuno di questi servizi, vedere la documentazione relativa ai servizi di Azure. Vedere [Scenari della soluzione](#solution-scenarios) in questo articolo per alcuni approcci comuni con le applicazioni in Batch e HPC.

>[AZURE.NOTE]Nuovi servizi vengono introdotti regolarmente nella piattaforma Azure e possono essere utili per lo scenario. L'utilizzo dei servizi di anteprima è consigliata solo per le distribuzioni di test o di prova, non per carichi di lavoro di produzione. In caso di domande, contattare un [partner Azure](https://pinpoint.microsoft.com/it-IT/search?keyword=azure) o inviare un messaggio di posta elettronica **bigcompute@microsoft.com*.

### Servizi di calcolo

Questi servizi in Azure sono alla base di una soluzione Big Compute. I servizi di calcolo nella tabella seguente vengono utilizzati di frequente e offrono vantaggi per i diversi scenari. A livello di base, questi servizi offrono modalità diverse alle applicazioni per l'esecuzione in istanze di calcolo basate su macchine virtuali fornite da Azure tramite la tecnologia Hyper-V di Windows Server. A seconda del servizio, è possibile eseguire queste istanze con un'ampia gamma di strumenti e sistemi operativi Linux e Windows standard e personalizzati. Azure offre un'ampia [gamma di dimensioni delle istanze](../virtual-machines/virtual-machines-sizes-specs.md) con configurazioni diverse di core CPU, memoria, capacità del disco e altre caratteristiche. In base alle proprie esigenze è possibile ridimensionare le istanze a migliaia di core e quindi ridurle quando sono necessarie meno risorse.

>[AZURE.NOTE]È possibile sfruttare le istanze A8-A11 per migliorare le prestazioni di alcuni carichi di lavoro complessi, tra cui applicazioni MPI parallele che richiedono una bassa latenza e la rete ad alta velocità effettiva dell'applicazione. Vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Servizio | Descrizione
------------- | -----------
**[Servizi cloud](http://azure.microsoft.com/documentation/services/cloud-services)**<br/><br/> |• Possono eseguire applicazioni Big Compute in istanze di ruolo di lavoro, ovvero macchine virtuali su cui è in esecuzione una versione di Windows Server e gestite completamente da Azure<br/><br/>• Promuovono l'utilizzo di applicazioni scalabili e affidabili con sovraccarico amministrativo ridotto, in esecuzione in un modello PaaS (Platform as-a Service)<br/><br/>• Potrebbero richiedere ulteriori strumenti o sviluppo per l'integrazione con soluzioni cluster HPC locali
**[Macchine virtuali](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/><br/> |• Forniscono potenza di calcolo IaaS (Infrastructure as-a Service) mediante la tecnologia Microsoft Hyper-V<br/><br/>• Consentono di eseguire il provisioning e gestire computer cloud permanenti basati su immagini Windows Server o Linux standard, altre immagini e dischi di dati o provenienti dall'[Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Permettono l'esecuzione di strumenti cluster di elaborazione locali e di applicazioni interamente nel cloud
**[Batch](http://azure.microsoft.com/documentation/services/batch)**<br/><br/> |• Esegue carichi di lavoro su larga scala in parallelo e in batch, ad esempio il rendering delle immagini, nonché la codifica e transcodifica multimediale in un servizio completamente gestito<br/><br/>• Garantisce la pianificazione dei processi e la scalabilità automatica di un pool di macchine virtuali gestito<br/><br/>• Consente agli sviluppatori di compilare ed eseguire applicazioni come un servizio o abilitare al cloud applicazioni esistenti<br/>

### Servizi di archiviazione

In genere una soluzione Big Compute opera su un set di dati di input e genera dati per i relativi risultati. Alcuni dei servizi di archiviazione Azure utilizzati in molte soluzioni Big Compute includono:

* [BLOB, tabelle e archiviazione delle code](http://azure.microsoft.com/documentation/services/storage): gestione di grandi quantità di dati non strutturati, dati NoSQL e messaggi per flusso di lavoro e comunicazione, rispettivamente. Ad esempio, è possibile utilizzare l'archiviazione BLOB per grandi set di dati tecnici o le immagini di input o i file multimediali per i processi applicativi. È possibile utilizzare le code per la comunicazione asincrona in una soluzione. Vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md) per ulteriori informazioni su queste soluzioni di archiviazione.

* [File di archiviazione Azure](http://azure.microsoft.com/services/storage/files/): condivisioni di file e dati in Azure utilizzando il protocollo SMB standard, necessario per alcune soluzioni cluster HPC comuni.

### Servizi dati e analisi

Alcuni scenari di Big Compute implicano i flussi di dati su larga scala o generano i dati che necessitano di ulteriore elaborazione o analisi. Per gestire questa situazione, Azure offre una serie di servizi dati e l'analisi, tra cui:

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory): consente la creazione di flussi di lavoro basati sui dati (pipeline) per riunire, aggregare e trasformare dati originati da archivi dati Internet, locali e basati sul cloud.

* [Database SQL](http://azure.microsoft.com/documentation/services/sql-database): fornisce le funzionalità principali di un sistema di gestione di database relazionali Microsoft SQL Server in un servizio di piattaforma gestita.

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight): distribuzione e provisioning di cluster Apache Hadoop basati su Windows Server o Linux nel cloud per gestire, analizzare e creare report sui Big Data con elevata affidabilità e disponibilità.

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning): consente di creare, testare, utilizzare e gestire soluzioni di analisi predittive in un servizio di piattaforma completamente gestito.

### Servizi aggiuntivi

La soluzione Big Compute potrebbe avere la necessità di includere altri servizi di infrastruttura e della piattaforma Azure per connettersi alle risorse locali o in altri ambienti. Tra gli esempi sono inclusi:

* [Rete virtuale](http://azure.microsoft.com/documentation/services/virtual-network): crea una sezione logicamente isolata in Azure per connettere le risorse di Azure al data center locale o un singolo computer client tramite IPSec; consente alle applicazioni Big Compute di accedere ai dati locali, ai servizi di Active Directory e ai server licenze

* [ExpressRoute](http://azure.microsoft.com/documentation/services/expressroute): crea una connessione privata tra data center Microsoft e l'infrastruttura locale o in un ambiente di condivisione percorso con una maggiore sicurezza, maggiore affidabilità, maggiore velocità e minori latenze rispetto alle connessioni tipiche su Internet.

* [Bus di servizio](http://azure.microsoft.com/documentation/services/service-bus): sono disponibili diversi metodi per le applicazioni per comunicare o scambiare i dati, se si trovano in Azure, in un'altra piattaforma cloud o in un data center.

## Scenari della soluzione

Di seguito sono riportati gli scenari comuni per eseguire carichi di lavoro Big Compute in Azure, sfruttando le soluzioni esistenti del cluster HPC, servizi di Azure o una combinazione dei due.

>[AZURE.NOTE]Per le organizzazioni con carichi di lavoro a utilizzo intensivo di calcolo che non sono adatti a strumenti di cluster HPC standard o che non eseguono la migrazione direttamente ai servizi Azure, Azure offre agli sviluppatori e ai partner un set completo di funzionalità di calcolo, servizi, soluzioni di architettura e strumenti di sviluppo. Azure può supportare flussi di lavoro Big Compute personalizzati che garantiscono una scalabilità fino a migliaia di core di calcolo.

### Scenario 1. Potenziamento di un cluster HPC locale in Azure

**Quando si sceglierà questo?**: è possibile che già si disponga di un cluster HPC in locale che esegue il calcolo dei carichi di lavoro con utilizzi intensivi, ma esso necessita di risorse di calcolo aggiuntive per periodi di picco, ad esempio report di fine del mese o progetti speciali. Anziché acquistare, distribuire e gestire hardware e software che potrebbe essere inattivo per la maggior parte dei casi, è possibile utilizzare Azure per aggiungere potenza di calcolo su richiesta per il cluster esistente.

Ad esempio, se il cluster HPC locale esistente viene compilato con[Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)è possibile aggiungere ulteriori risorse di calcolo sotto forma di istanze del ruolo di lavoro di Azure in esecuzione in un servizio cloud. Vedere la figura seguente. Per ulteriori informazioni e istruzioni dettagliate, vedere[potenziamento in Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

![Potenziamento dei cluster][burst_cluster]

>[AZURE.NOTE]Se si desidera ridurre al minimo il footprint del cluster HPC Pack, è possibile ridurre il cluster locale solo il nodo head HPC Pack. Quindi, aggiungere che tutte le risorse su richiesta in Azure di calcolo. Per un'esercitazione in cui viene illustrato questo scenario, vedere [Configurare un cluster di elaborazione ibrido con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md).

Questa soluzione ibrida sfrutta un investimento esistente in un cluster locale, ma è possibile ridimensionare l'infrastruttura locale predefinito per i carichi di lavoro tipici (non di punta). Se è necessario accedere a un archivio di dati o del server licenze locale, è possibile impostare una rete virtuale di Azure per connettere il cluster locale in Azure.

### Scenario 2. Creare un cluster HPC in Azure

**Quando si sceglierà questo?**: è possibile che si disponga già di un investimento sostanziale in un cluster HPC locale di Windows o Linux compresa la gestione e la pianificazione di strumenti e applicazioni personalizzate. Potrebbe essere necessario capacità cluster aggiuntivo per eseguire le applicazioni e i set di strumenti esistente ma non desidera investire in un'infrastruttura locale aggiuntive o modificare le applicazioni. Oppure potrebbe essere necessario creare un nuovo cluster per un breve o lungo periodo di tempo, ma non desidera pagare il costo di acquisto, la gestione e quest'ultimo si o allocare lo spazio per l'installazione e distribuzione.

È possibile utilizzare gli strumenti di Automazione di Azure per creare un cluster HPC in macchine virtuali di Azure per creare la capacità che è necessario. A seconda delle macchine virtuali che si distribuisce, è possibile eseguire un'ampia gamma di HPC e carichi di lavoro paralleli, tra cui strettamente associata applicazioni MPI.

>[AZURE.NOTE]Contattare il fornitore della soluzione cluster locale e applicazioni per requisiti aggiuntivi e procedure consigliate per l'esecuzione in un cloud pubblico offre un'infrastruttura come servizio (IaaS).

Ad esempio, è possibile creare un cluster HPC con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) nelle macchine virtuali per i servizi infrastrutturali di Azure (IaaS) per l'esecuzione di carichi di lavoro Windows o Linux, come illustrato nella figura seguente semplificata. Un utente del cluster può inviare un processo in modo sicuro al cluster del cloud tramite strumenti di invio a processi standard HPC Pack in esecuzione su un computer client. Vedere [Opzioni del cluster HPC con Microsoft HPC Pack in Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md).

![Cluster in IaaS][iaas_cluster]

**Installazione automatizzata** per distribuire un numero elevato di macchine virtuali Windows Server o Linux è possibile utilizzare immagini di VM standard o personalizzate e gli strumenti di Automazione di Azure, ad esempio l’[interfaccia della riga di comando di Azure](../xplat-cli-install.md)o[Azure PowerShell](../powershell-install-configure.md). Tra gli esempi sono inclusi:

* Per distribuire un cluster HPC Pack in servizi di infrastruttura di Azure, è possibile eseguire uno [script di Azure PowerShell](https://msdn.microsoft.com/library/azure/dn864734.aspx) flessibile da un computer client, lo script utilizza un'immagine di macchina virtuale Windows Server con HPC Pack preinstallato. È inoltre possibile utilizzare un[modello delle Guide rapide](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)di Azure con Azure PowerShell o la CLI di Azure per distribuire un cluster HPC Pack.

* È possibile utilizzare un [modello delle Guide rapide](https://azure.microsoft.com/documentation/templates/slurm/)di Azure con Azure PowerShell o la CLI di Azure per distribuire un cluster di Linux che esegue la gestione del carico di lavoro open source [SLURM](https://computing.llnl.gov/linux/slurm/).

Inserisce un intero cluster HPC nel cloud, è possibile ottenere vantaggi chiari.

* Un'organizzazione può annullare processi di HPC senza dover acquistare e la gestione aggiuntiva su hardware locale e può controllare le dimensioni del cluster di utilizzare risorse di calcolo in modo efficiente.

* Molte applicazioni del cluster locali possono essere eseguite senza modifiche o limitate, in un cluster basato su cloud.

* Rispetto a una soluzione ibrida che consente di estendere un cluster locale al cloud, esecuzione di un'applicazione interamente nel cloud consente di semplificare l'accesso ai dati. Anziché suddividere i dati tra le installazioni locali e cloud o gestire una parte dei dati di accesso dell'applicazione in modalità remota, tutti i dati dell'applicazione possono archiviati nel cloud.

* Alcuni fornitori di software ottimizzare le applicazioni per l'esecuzione in cluster basati su cloud. Ad esempio, distribuendo il [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) di MathWorks in un cluster HPC Pack in macchine virtuali di Azure, è possibile eseguire processi MATLAB paralleli interamente con risorse di calcolo basate su cloud.

### Scenario 3. Scalabilità orizzontale di un'applicazione parallela in Azure

**Quando si sceglierà questo?**: se si utilizza un'applicazione a elevato utilizzo di calcolo, ad esempio una simulazione Monte Carlo, il rendering di animazione o la transcodifica multimediale nella workstation locale o un piccolo cluster. Non si desidera gestire le risorse di calcolo o un'utilità di pianificazione del processo; al contrario, si desidera concentrarsi sull'esecuzione dell'applicazione in modo efficiente per la risoluzione dei problemi aziendali. In alternativa, è possibile scaricare l'applicazione con utilizzo intensivo di calcolo o un'applicazione di terze parti, affinché venga eseguito come servizio interamente nel cloud.

A seconda del carico di lavoro, è possibile sfruttare un servizio Big Compute esistente in Azure, ospitato da Microsoft o un altro fornitore di servizi, per semplificare la gestione dell'infrastruttura e l'applicazione per la soluzione. Alcuni servizi di hosting di applicazioni specifiche per i clienti in settori selezionati. Alcuni servizi collegare applicazioni locali, consentendo una soluzione ibrida. Altri, come i[Servizi multimediali di Azure](http://azure.microsoft.com/documentation/services/media-services) sono servizi della piattaforma dedicati.

Per abilitare facilmente cloud e scalare un'applicazione eseguita oggi,[Batch](http://azure.microsoft.com/documentation/services/batch)fornisce API per pianificare i processi e gestire risorse di calcolo in un servizio. Batch gestisce la distribuzione e la scalabilità automatica delle macchine virtuali, pianificazione dei processi, il ripristino di emergenza, lo spostamento dei dati, la gestione delle dipendenze, distribuzione di applicazioni e tutte le altre funzionalità richieste per eseguire processi nel cloud. Attualmente Batch è ideale per applicazioni intrinsecamente parallele come risorse di calcolo di rendering delle immagini, modellazione dei rischi finanziari e simulazioni Monte Carlo in esecuzione in Windows Server.

Vedere la figura riportata di seguito per un flusso di lavoro tipico, che uno sviluppatore può creare batch.

![Azure Batch][batch_proc]

1. Caricare file di input (ad esempio dati di origine o immagini, .exe applicazione richiesta o i file di script e le relative dipendenze) di archiviazione di Azure.

2. Creare un servizio di Batch sarà:

    a. Distribuire un pool di macchine virtuali di Azure identico per eseguire l'applicazione, analoghi ai nodi in un cluster HPC di calcolo. Lo sviluppatore specifica le dimensioni, il sistema operativo in esecuzione e altre proprietà, ad esempio se il pool può scalabilità automatica. Esegue un'attività, viene automaticamente assegnato una macchina virtuale del pool.

    b. Creare un processo per eseguire l'applicazione. Lo sviluppatore può specificare una pianificazione e la priorità per il processo o è possibile eseguire il processo su richiesta.

    c. Il processo in attività di partizione. Ogni attività è essenzialmente una riga di comando viene eseguito su una delle macchine virtuali nel pool di elaborare le informazioni da uno dei file di dati caricati in memoria.

3. Eseguire il servizio e monitorare i risultati.


## Passaggi successivi

* Vedere[risorse tecniche per Batch e HPC](big-compute-resources.md)per trovare indicazioni tecniche per la soluzione di cui si ha bisogno.

* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](http://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=Oct15_HO3-->