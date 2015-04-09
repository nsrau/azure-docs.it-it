<properties 
	pageTitle="Introduzione a Data factory di Azure" 
	description="Informazioni su come usare il servizio Data factory di Azure per comporre l'elaborazione dei dati, l'archiviazione dei dati e servizi di spostamento dei dati al fine di creare pipeline che producono informazioni attendibili." 
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
	ms.topic="article" 
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Introduzione al servizio Data factory di Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

**Data factory di Azure** è un servizio completamente gestito per la composizione di servizi di archiviazione, elaborazione e spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili e affidabili. Il servizio Data factory consente di: 

- Creare flussi di lavoro guidati dai dati (pipeline) per riunire, aggregare e trasformare dati provenienti da archivi dati Internet, locali e basati sul cloud. 
- Trasformare in informazioni attendibili dati semistrutturati, non strutturati e strutturati provenienti da origini dati diversificate.
- Produrre dati facilmente utilizzabili mediante strumenti di business intelligence (BI) e di analisi e altre applicazioni. 
- Configurare un'elaborazione dati complessa attraverso semplici script JSON.
- Monitorare e gestire pipeline in modo immediato con una ricca esperienza visiva, offerta dal portale di anteprima di Azure.  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## Informazioni generali
In passato i progetti di integrazione dei dati si basavano sulla creazione di processi ETL (Extract-Transform-Load) che consentivano di estrarre i dati da diverse origini dati in un'organizzazione, di trasformarli in modo che fossero conformi allo schema di destinazione di un data warehouse aziendale e di caricarli in un data warehouse aziendale. L'EDW a cui veniva eseguito l'accesso rappresentava quindi l'unica fonte di veridicità per le soluzioni di analisi BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

L'attuale scenario di gestione dei dati per le aziende continua a crescere in misura esponenziale in termini di volume, varietà e complessità. Si tratta di uno scenario diversificato, con dati locali e generati su cloud di diversi formati e velocità.  L'elaborazione dei dati deve essere eseguita tra aree geografiche diverse e include una combinazione di software open source, soluzioni commerciali e servizi di elaborazione personalizzati costosi e difficili da integrare e gestire.  La flessibilità necessaria per adattarsi al mutevole scenario dei Big Data rappresenta un'opportunità per unire il tradizionale EDW con le capacità richieste in un moderno sistema di produzione delle informazioni.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

Il servizio **Data factory di Azure** è la piattaforma di composizione che integra i data warehouse aziendali tradizionali nello scenario dei dati in continua evoluzione per consentire alle aziende di sfruttare tutte le informazioni disponibili per un processo decisionale basato sui dati. Il servizio permette inoltre alle aziende di sfruttare questa diversità grazie a una piattaforma in grado di riunire servizi di elaborazione, archiviazione e spostamento dei dati in pipeline di produzione delle informazioni e di gestire asset di dati attendibili.

Il servizio Data factory di Azure consente di:

- **Usare agevolmente diversi sistemi di archiviazione ed elaborazione dei dati.** 
Il servizio Data factory consente di creare pipeline di produzione delle informazioni per spostare ed elaborare origini dati locali (come SQL Server) e basate sul cloud, come il database SQL di Azure e tabelle e BLOB di Azure. 
- **Trasformare dati in informazioni attendibili.** 
Il servizio Data factory supporta l'elaborazione Hive, Pig e C#, oltre alle principali funzionalità di elaborazione, come la gestione automatica dei cluster Hadoop (HDInsight), la ripetizione di tentativi in caso di errori temporanei, i criteri di timeout configurabili e gli avvisi.  
- **Monitorare le pipeline di dati da un'unica posizione.** 
Il servizio Data factory offre una visione completa dei servizi di archiviazione, elaborazione e spostamento dei dati  e consente di valutare rapidamente l'integrità della pipeline di dati end-to-end, individuare i problemi e adottare le eventuali misure correttive necessarie. È anche possibile tenere visivamente traccia della derivazione dei dati e delle relazioni tra i dati delle diverse origini, nonché visualizzare una presentazione cronologica completa di esecuzione dei processi, integrità del sistema e dipendenze da un unico dashboard di monitoraggio.
- **Ottenere informazioni accurate complete dai dati trasformati.**
Il servizio Data factory permette di creare pipeline di dati in grado di produrre dati attendibili, che possono quindi essere usati da strumenti di business intelligence e di analisi e da altre applicazioni.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modello applicativo
Il diagramma seguente mostra il modello applicativo supportato dal servizio Data factory di Azure.

![Application Model][image-data-factory-application-model]

Una data factory di Azure comprende tre fasi di produzione delle informazioni:

- **Connect & Collect**. In questa fase, i dati di più origini vengono importati negli hub di dati. Una pipeline in una data factory può comprendere una o più attività. Usare una o più **attività di copia** in una pipeline di dati per raccogliere dati da archivi dati di origine a un archivio dati di destinazione con un hub di dati per l'elaborazione aggiuntiva. Un cluster HDInsight (calcolo) e l'archiviazione BLOB di Azure associata (archiviazione) insieme costituiscono un hub di dati, ovvero un hub di dati HDInsight. Per usare un hub di dati HDInsight, copiare tutti i dati di origine in un archivio BLOB di Azure associato a HDInsight, in modo che i dati possano essere elaborati dal cluster HDInsight. Una pipeline viene eseguita su una risorsa di calcolo in un hub di dati, ad esempio un cluster HDInsight.      
- **Transform & Enrich**. In questa fase, i dati raccolti vengono elaborati. Ad esempio, un'**attività di HDInsight** in una pipeline può elaborare i dati archiviati nell'archivio BLOB di Azure associato eseguendo trasformazioni con script Hive/Pig per produrre informazioni attendibili. Le pipeline possono essere concatenate (come mostrato nel diagramma) in modo che i set di dati di output di una pipeline possano diventare i set di dati di input per un'altra pipeline nello stesso hub di dati o in uno diverso.  
- **Publish**. In questa fase, i dati sono pubblicati in modo da poter essere usati dagli strumenti BI, dagli strumenti di analisi e da altre applicazioni. Ad esempio, un'attività di copia nella pipeline può copiare i dati di output dall'elaborazione eseguita nella fase Transform & Enrich in un archivio dati (ad esempio, SQL Server locale) sulla base del quale è possibile creare soluzioni di business intelligence.   

<!--

Le data factory consentono agli sviluppatori di creare pipeline, ovvero gruppi di attività di spostamento e/o elaborazione dei dati che accettano uno o più set di dati di input e producono uno o più set di dati di output. Le pipeline possono essere eseguite una sola volta o in base a un intervallo flessibile di pianificazioni (oraria, giornaliera, settimanale e così via). Un set di dati è una visualizzazione di dati denominata. I dati descritti possono variare da semplici byte a dati semistrutturati come i file CSV, fino alle tabelle o ai modelli.

Le pipeline che comprendono attività di spostamento dei dati (come l'attività di copia) vengono spesso usate per importare/esportare dati da tutte le origini dati (database, file, servizi SaaS e così via) usate dall'organizzazione in un hub di dati.
 
Quando i dati si trovano in un **hub**, le **pipeline** ospitate dai servizi di calcolo dell'hub vengono usate per trasformare i dati in un formato adatto all'uso (da parte degli strumenti BI, delle applicazioni, dei clienti e così via).  
  
Infine, le **pipeline** possono essere concatenate (come mostrato nel diagramma) in modo che i **set di dati** di output di una pipeline corrispondano all'input di un'altra.  Ciò consente di includere i flussi di dati complessi nelle **pipeline** eseguite in un hub di dati o su più hub.  Usando le **pipeline** in questo modo, le organizzazioni dispongono di blocchi predefiniti per comporre servizi locali, cloud e SaaS altamente efficienti mediante una singola istanza di Data factory facile da gestire.
--> 

## Esperienza di creazione

È possibile creare data factory con uno degli strumenti seguenti:

- **Portale di anteprima di Azure**. I pannelli di Data factory nel portale di anteprima di Azure offrono una ricca interfaccia per creare data factory e servizi collegati. L'**editor di Data factory**, anch'esso parte del portale, permette di creare facilmente servizi collegati, tabelle, set di dati e pipeline specificando definizioni JSON per questi elementi. Vedere la pagina relativa all'[editor di Data factory][data-factory-editor] per una panoramica dell'editor e [Introduzione a Data factory di Azure][datafactory-getstarted] per un esempio dell'utilizzo del portale o dell'editor per creare e distribuire una data factory.   
- **Azure PowerShell**. Gli utenti di PowerShell potrebbero voler usare PowerShell invece dell'interfaccia utente del portale. In tal caso, i cmdlet di Data factory di Azure disponibili in Azure PowerShell consentono di creare e distribuire data factory. Vedere [Monitorare e gestire Data factory di Azure con Azure PowerShell][create-data-factory-using-powershell] per un esempio più semplice e [Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] per un esempio avanzato dell'utilizzo dei cmdlet di PowerShell per creare e distribuire una data factory. Per la documentazione completa dei cmdlet di Data factory, vedere le [informazioni di riferimento sui cmdlet di Data factory][adf-powershell-reference] su MSDN Library.  
- **Libreria di classi .NET**. È possibile creare data factory a livello di codice usando .NET SDK per Data factory. Per la procedura dettagliata per la creazione di una data factory con .NET SDK, vedere la pagina relativa a [creazione, monitoraggio e gestione delle data factory con .NET SDK][create-factory-using-dotnet-sdk]. Per la documentazione completa di .NET SDK per Data factory, vedere le [informazioni di riferimento sulla libreria di classi per Data factory][msdn-class-library-reference].  
- **API REST**. Per creare e distribuire data factory, è anche possibile usare l'API REST esposta dal servizio Data factory di Azure. Per la documentazione completa, vedere le [informazioni di riferimento sull'API REST di Data factory][msdn-rest-api-reference]. 


##Terminologia
Questa sezione descrive la terminologia relativa a Data factory di Azure.

### Data factory
Una **data factory di Azure** comprende una o più pipeline che elaborano dati in archivi dati collegati (Archiviazione di Azure, database SQL di Azure, SQL Server locale e così via) usando servizi di calcolo collegati come Azure HDInsight. Una data factory di Azure non contiene dati al suo stesso interno, ma i dati sono invece archiviati negli archivi dati citati sopra.  

### Servizio collegato
Un **servizio collegato** è un servizio collegato a una data factory di Azure. Un servizio collegato può essere:

- Un servizio di **archiviazione dei dati** come Archiviazione di Azure, il database SQL di Azure o il database di SQL Server locale. Un archivio dati è un contenitore di set di dati di input/output.    
- Un servizio di **calcolo** come Azure HDInsight e Azure Machine Learning. Un servizio di calcolo elabora i dati di input e produce i dati di output.  

### Set di dati
Un **set di dati** è una visualizzazione di dati denominata. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle tabelle relazionali o persino ai modelli. Una **tabella** di Data factory è un set di dati che contiene uno schema ed è di forma rettangolare. Dopo aver creato un servizio collegato in un archivio dati che fa riferimento a un archivio dati, è necessario definire i set di dati che rappresentano i dati di input/output aggiunti all'archivio dati. 


###Pipeline
Una **pipeline** in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Ad esempio, un'**attività di copia** copia i dati da un archivio di origine a un archivio di destinazione, mentre un'**attività di HDInsight** usa un cluster Azure HDInsight per elaborare i dati mediante query Hive o script Pig. Una data factory può comprendere una o più pipeline. 

I passaggi tipici per la creazione di un'istanza di Data factory di Azure sono:

1. Creare una **data factory**.
2. Creare un **servizio collegato** per ogni archivio dati o servizio di calcolo.
3. Creare **set di dati** di input e di output.
4. Creare una **pipeline**. 

###Attività
Un passaggio di elaborazione dati in una pipeline che acquisisce uno o più set di dati di input e genera uno o più set di dati di output.  Le attività vengono eseguite in un ambiente di esecuzione (ad esempio, un cluster Azure HDInsight) e leggono/scrivono i dati in un archivio dati associato/collegato alla data factory di Azure. 

Il servizio Data factory di Azure supporta le attività seguenti in una pipeline: 

- Un'**attività di copia** copia i dati da un archivio dati a un altro archivio dati. Per informazioni dettagliate sugli archivi dati supportati dall'attività di copia, vedere l'articolo relativo alla [copia di dati con Data factory di Azure][copy-data-with-adf]. 
- L'**attività HDInsight** elabora i dati tramite l'esecuzione di script Hive/Pig o di programmi MapReduce in un cluster HDInsight. Per informazioni dettagliate, vedere le pagine relative all'[uso di Pig e Hive con Data factory][use-pig-hive] e a come [richiamare programmi MapReduce da Data factory][run-map-reduce]. 
- L'**attività di punteggio batch di Azure Machine Learning** richiama l'API di punteggio batch di Azure Machine Learning. Per informazioni dettagliate, vedere la pagina relativa alla [creazione di pipeline predittive usando Data factory di Azure e Azure Machine Learning][azure-ml-adf]. 
- L'**attività stored procedure** richiama una stored procedure in un database SQL di Azure. Per informazioni dettagliate, vedere la pagina relativa all'[attività stored procedure][msdn-stored-procedure-activity] su MSDN Library.   

###Sezione
Una tabella in una data factory di Azure è costituita da sezioni. La larghezza di una sezione viene determinata dalla pianificazione, oraria o giornaliera. Quando la pianificazione è oraria, viene prodotta una sezione ogni ora con l'ora di inizio e di fine di una pipeline. Ad esempio, se le data e le ore di inizio e di fine della pipeline sono rispettivamente 03/03/2015 alle 6.00 e 03/03/2015 alle 9.00 dello stesso giorno, vengono prodotte tre sezioni, una per ogni intervallo di un'ora: 6.00-7.00, 7.00-8.00 e 8.00-9.00.    

Le sezioni consentono di usare un subset di tutti i dati per un intervallo di tempo specifico, ad esempio la sezione prodotta per la durata (oraria) dalle 13.00 alle 14.00. 

### Esecuzione di attività per una sezione
Un'**esecuzione** o un'esecuzione di attività è un'unità di elaborazione per una sezione. A una sezione possono essere associate una o più esecuzioni se vengono eseguiti più tentativi o se si ripete l'esecuzione della sezione in caso di errori. Una sezione viene identificata in base all'ora di inizio.

###Gateway di gestione dati
Il **Gateway di gestione dati** Microsoft è un software che connette origini dati locali a servizi cloud ai fini del consumo. Prima di aggiungere origini dati locali come servizi collegati, è necessario che nell'ambiente aziendale sia installato almeno un gateway registrato con il portale di Data factory di Azure.
 
###Hub di dati
Un **hub di dati** è un contenitore logico per servizi di archiviazione e di calcolo dei dati. Ad esempio, un cluster Hadoop con HDFS come archiviazione e Hive/Pig come calcolo (elaborazione) è un hub di dati. Analogamente, un data warehouse aziendale (EDW) può essere modellato come hub di dati (database come archiviazione, stored procedure e/o strumento ETL come servizi di calcolo).  Le pipeline usano gli archivi dati e sono eseguite su risorse di calcolo in un hub di dati. Al momento è supportato solo l'hub HDInsight.

L'hub di dati consente di dividere un'istanza di Data factory in gruppi logici o di dominio specifici, ad esempio "West US Azure Hub" che gestisce tutti i servizi collegati (archivi dati e calcolo) e le pipeline concentrati nel data center degli Stati Uniti occidentali oppure "Sales EDW Hub" che gestisce tutti i servizi collegati e le pipeline associati al popolamento e all'elaborazione dei dati per l'EDW di vendita.

Una caratteristica importante dell'hub riguarda il fatto che una pipeline viene eseguita in un singolo hub. Ciò significa che, quando si definisce una pipeline, tutti i servizi collegati a cui fanno riferimento le tabelle o le attività nella pipeline devono avere lo stesso nome hub della pipeline. Se la proprietà HubName non è specificata per un servizio collegato, questo viene inserito nell'hub predefinito.

##Passaggi successivi

1. [Introduzione a Data factory di Azure][datafactory-getstarted]. Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
2. [Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial]. Questo articolo fornisce una **procedura dettagliata end-to-end** che mostra come implementare uno **scenario quasi reale** usando Data factory di Azure per trasformare i dati da file di log in informazioni accurate.
3. [Data factory - Domande frequenti][adf-faq]. Questo articolo contiene un elenco di domande frequenti e le rispettive risposte. 
3. [Scenari comuni di utilizzo di Data factory di Azure][adf-common-scenarios]. Questo articolo descrive alcuni scenari comuni di utilizzo del servizio Data factory di Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->