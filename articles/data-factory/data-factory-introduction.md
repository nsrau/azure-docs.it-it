<properties 
	pageTitle="Introduzione a Data Factory, un servizio di integrazione dei dati | Microsoft Azure" 
	description="Informazioni su Azure Data Factory: è un servizio di integrazione dei dati cloud che consente di orchestrare e automatizzare lo spostamento e la trasformazione dei dati." 
	keywords="integrazione dei dati, integrazione dei dati cloud, che cos'è azure data factory"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="09/08/2016" 
	ms.author="spelluru"/>  

# Introduzione al servizio Azure Data Factory, un servizio di integrazione dati nel cloud

## Che cos'è Azure Data Factory? 
Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Data factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso.

Data factory usa origini dati nel cloud e locali, oltre ai servizi SaaS, per inserire, preparare, trasformare, analizzare e pubblicare i dati. È possibile usare Data factory per la composizione di servizi in pipeline di flussi di dati gestite per trasformare i dati mediante servizi quali [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) e [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) per le esigenze di calcolo relative ai Big Data e tramite [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) per rendere operative le soluzioni di analisi, in modo da superare le visualizzazioni di monitoraggio tabulari e avvalersi delle visualizzazioni avanzate offerte da Data factory per visualizzare rapidamente le derivazioni e le dipendenze tra le pipeline di dati. Data Factory permette di monitorare tutte le pipeline dei flussi di dati da un'unica visualizzazione unificata per individuare rapidamente i problemi e configurare avvisi di monitoraggio.

![Diagramma: Panoramica di Data Factory, un servizio di integrazione dei dati](./media/data-factory-introduction/what-is-azure-data-factory.png)  

**Figura 1.** Data Factory permette di raccogliere i dati da molte origini dati locali diverse, quindi di inserirli, prepararli, trasformarli e analizzarli e infine pubblicare dati pronti per l'utilizzo.

È possibile usare Data factory ogni volta che è necessario raccogliere dati con forme e dimensioni diverse, trasformarli e pubblicarli per estrarre informazioni dettagliate, il tutto con tempi affidabili. Data Factory viene utilizzata per creare pipeline di flusso di dati a disponibilità elevata per molti scenari in diversi settori per le esigenze di analisi pipeline. I rivenditori in linea la utilizzano per generare [consigli sui prodotti](data-factory-product-reco-usecase.md) personalizzati in base al comportamento di esplorazione del cliente. I game studios la utilizzano per comprendere l’[efficacia delle loro campagne di commercializzazione](data-factory-customer-profiling-usecase.md). Ottenere informazioni direttamente dai nostri clienti sulle modalità e sui motivi di utilizzo di Data Factory esaminando[Casi di studio sui clienti](data-factory-customer-case-studies.md).

> [AZURE.VIDEO azure-data-factory-overview]

## Concetti principali

Le entità principali di Data factory interagiscono tra loro per definire i dati di input e di output, gli eventi di elaborazione e la pianificazione e le risorse necessarie per eseguire il flusso di dati desiderato.

![Diagramma: Data Factory, servizio di integrazione dei dati cloud - Concetti chiave](./media/data-factory-introduction/data-integration-service-key-concepts.png)  

**Figura 2.** Relazioni tra set di dati, attività, pipeline e servizio collegato

### Pipeline
Le [pipeline](data-factory-create-pipelines.md) sono un raggruppamento logico di attività. Vengono usate per raggruppare in un'unità attività che insieme eseguono un'operazione. Ad esempio, per pulire i dati dei file di log potrebbe essere necessaria una sequenza di più attività di trasformazione. Questa sequenza potrebbe prevedere una pianificazione complessa e dipendenze che devono essere orchestrate e automatizzate. Tutte queste attività possono essere raggruppate in una singola pipeline denominata "CleanLogFiles", che può essere quindi distribuita, pianificata o eliminata come una singola unità invece di gestire in modo indipendente ogni attività specifica.

### Attività
Le attività definiscono le azioni da eseguire sui dati. Ogni attività accetta zero o più [set di dati](data-factory-create-datasets.md) come input e genera uno o più set di dati come output. Un'attività è un'unità di orchestrazione in Data factory di Azure. Ad esempio, è possibile usare un'[attività di copia](data-factory-data-movement-activities.md) per orchestrare la copia dei dati da un set di dati a un altro. Allo stesso modo, è possibile usare un'[attività Hive](data-factory-data-transformation-activities.md) che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data factory di Azure offre un'ampia gamma di attività di trasformazione, analisi e spostamento dei dati.

### Set di dati
I [set di dati](data-factory-create-datasets.md) sono riferimenti denominati o puntatori ai dati da usare come input o output di un'attività. I set di dati identificano le strutture di dati all'interno di archivi dati diversi, tra cui tabelle, file, cartelle e documenti.

### Servizio collegato
I servizi collegati definiscono le informazioni necessarie affinché il servizio Data factory si connetta a risorse esterne. In Data factory i servizi collegati vengono usati per i due scopi seguenti:

- Per rappresentare un archivio dati, inclusi, a titolo esemplificativo, un'istanza di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Come indicato in precedenza, i set di dati rappresentano le strutture dei dati all'interno degli archivi dati connessi a Data Factory tramite un servizio collegato.
- Per rappresentare una risorsa di calcolo che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività "HDInsightHive" viene eseguita in un cluster HDInsight Hadoop.

Dopo aver acquisito familiarità con i quattro semplici concetti relativi a set di dati, attività, pipeline e servizi collegati, si è pronti per iniziare. È possibile [compilare la prima pipeline](data-factory-build-your-first-pipeline.md) o distribuire un esempio pronto all'uso seguendo le istruzioni riportate nell'articolo [Azure Data Factory: esempi](data-factory-samples.md).

## Aree supportate
È attualmente possibile creare data factory nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi basati sui dati per orchestrare lo spostamento di dati tra [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores) e l'elaborazione di dati usando i [servizi di calcolo](data-factory-compute-linked-services.md) in altre aree o in un ambiente locale. Consente anche di [monitorare e gestire i flussi di lavoro](data-factory-monitor-manage-pipelines.md) usando meccanismi a livello di codice e di interfaccia utente.

Anche se Azure Data Factory è disponibile solo nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**, il servizio che consente lo spostamento dei dati in Data Factory è disponibile [a livello globale](data-factory-data-movement-activities.md#global) in alcune aree. Nel caso in cui un archivio dati si trovi dietro un firewall, i dati verranno spostati da un [Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, siano in esecuzione nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Europa settentrionale e usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di pochi secondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

In futuro si prevede che Azure Data Factory sia disponibile in ogni area geografica supportata da Azure.
  
## Passaggi successivi
Per informazioni su come creare data factory con pipeline di dati, attenersi alle istruzioni dettagliate disponibili nelle esercitazioni seguenti.

Esercitazione | Descrizione
-------- | -----------
[Creare la prima data factory (panoramica)](data-factory-build-your-first-pipeline.md) | Questa esercitazione mostra come compilare la prima istanza di Azure Data Factory con una pipeline di dati che **elabora i dati** eseguendo uno script Hive in un cluster Azure HDInsight (Hadoop). |
[Copy data from Blob Storage to SQL Database using Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Copiare dati dall'archivio BLOB al database SQL usando Data Factory) | In questa esercitazione viene creata una data factory con una pipeline per **spostare i dati** dall'archivio BLOB al database SQL.
[Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) | In questa esercitazione viene creata una data factory con una pipeline che **sposta i dati** da un database di SQL Server **locale** a un BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il Gateway di gestione dati nel computer. 

<!---HONumber=AcomDC_0914_2016-->