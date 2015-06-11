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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Introduzione al servizio Data factory di Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


Il **Azure dati Factory** è un servizio completamente gestito per la composizione dei servizi di spostamento dei dati, archiviazione ed elaborazione dei dati di dati nella pipeline di produzione di dati semplice, scalabile e affidabile. Il servizio Data factory consente di:

- Creare flussi di lavoro guidati dai dati (pipeline) per riunire, aggregare e trasformare dati provenienti da archivi dati Internet, locali e basati sul cloud. 
- Trasformare in informazioni attendibili dati semistrutturati, non strutturati e strutturati provenienti da origini dati diversificate.
- Produrre dati facilmente utilizzabili mediante strumenti di business intelligence (BI) e di analisi e altre applicazioni. 
- Configurare un'elaborazione dati complessa attraverso semplici script JSON.
- Monitorare e gestire pipeline in modo immediato con una ricca esperienza visiva, offerta dal portale di anteprima di Azure.  

Nel video seguente fornisce una rapida panoramica del servizio Factory di dati di Azure.


- [Video: Introduzione a Factory di dati di Azure](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Panoramica
In passato i progetti di integrazione dei dati si basavano sulla creazione di processi ETL (Extract-Transform-Load) che consentivano di estrarre i dati da diverse origini dati in un'organizzazione, di trasformarli in modo che fossero conformi allo schema di destinazione di un data warehouse aziendale e di caricarli in un data warehouse aziendale. L'EDW a cui veniva eseguito l'accesso rappresentava quindi l'unica fonte di veridicità per le soluzioni di analisi BI.

![ETL tradizionale][image-data-factory-introduction-traditional-ETL]

L'attuale scenario di gestione dei dati per le aziende continua a crescere in misura esponenziale in termini di volume, varietà e complessità. Si tratta di uno scenario diversificato, con dati locali e generati su cloud di diversi formati e velocità. L'elaborazione dei dati deve essere eseguita tra aree geografiche diverse e include una combinazione di software open source, soluzioni commerciali e servizi di elaborazione personalizzati costosi e difficili da integrare e gestire. La flessibilità necessaria per adattarsi al mutevole scenario dei Big Data rappresenta un'opportunità per unire il tradizionale EDW con le capacità richieste in un moderno sistema di produzione delle informazioni.

![Elaborazione Diverse odierna orizzontale][image-data-factory-introduction-todays-diverse-processing-landspace]

Il **Azure dati Factory** servizio è la piattaforma di composizione per il lavoro tra EDWs tradizionali e il mutevole panorama di dati per consentire alle aziende di sfruttare tutti i dati disponibili per le decisioni basate sui dati. Il servizio permette inoltre alle aziende di sfruttare questa diversità grazie a una piattaforma in grado di riunire servizi di elaborazione, archiviazione e spostamento dei dati in pipeline di produzione delle informazioni e di gestire asset di dati attendibili.

Il servizio Data factory di Azure consente di:

- **Utilizzare facilmente con i sistemi di elaborazione e archiviazione di dati diversi.** Il servizio Data factory consente di creare pipeline di produzione delle informazioni per spostare ed elaborare origini dati locali (come SQL Server) e basate sul cloud, come il database SQL di Azure e tabelle e BLOB di Azure. 
- **Trasformare i dati in informazioni attendibili.** Il servizio Data factory supporta l'elaborazione Hive, Pig e C#, oltre alle principali funzionalità di elaborazione, come la gestione automatica dei cluster Hadoop (HDInsight), la ripetizione di tentativi in caso di errori temporanei, i criteri di timeout configurabili e gli avvisi.  
- **Monitorare la pipeline di dati in un'unica posizione.** Il servizio Data factory offre una visione completa dei servizi di archiviazione, elaborazione e spostamento dei dati e consente di valutare rapidamente l'integrità della pipeline di dati end-to-end, individuare i problemi e adottare le eventuali misure correttive necessarie. È anche possibile tenere visivamente traccia della derivazione dei dati e delle relazioni tra i dati delle diverse origini, nonché visualizzare una presentazione cronologica completa di esecuzione dei processi, integrità del sistema e dipendenze da un unico dashboard di monitoraggio.
- **Ottenere informazioni avanzate da dati trasformati** servizio della Factory di dati consente di creare la pipeline di dati che generano dati attendibili, che possono essere utilizzati da strumenti di business intelligence e di analisi e altre applicazioni.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modello applicativo
Il diagramma seguente mostra il modello applicativo supportato dal servizio Data factory di Azure.

![Modello applicativo][image-data-factory-application-model]

Una data factory di Azure comprende tre fasi di produzione delle informazioni:

- **Connetti & raccogliere**. In questa fase, i dati di più origini vengono importati negli hub di dati. Una pipeline in una data factory può comprendere una o più attività. È possibile utilizzare uno o più **Copia** attività in una pipeline di dati per raccogliere dati dall'origine archivi dati in un archivio dati di destinazione con un hub di dati per un'ulteriore elaborazione. Un cluster HDInsight (calcolo) e l'archiviazione BLOB di Azure associata (archiviazione) insieme costituiscono un hub di dati, ovvero un hub di dati HDInsight. Per usare un hub di dati HDInsight, copiare tutti i dati di origine in un archivio BLOB di Azure associato a HDInsight, in modo che i dati possano essere elaborati dal cluster HDInsight. Una pipeline viene eseguita su una risorsa di calcolo in un hub di dati, ad esempio un cluster HDInsight.      
- **Transform & arricchire**. In questa fase, i dati raccolti vengono elaborati. Ad esempio, un **attività HDInsight** in una pipeline elabora i dati archiviati nell'archivio blob di Azure associato mediante l'esecuzione di trasformazioni tramite script Hive/Pig per produrre informazioni attendibili. Le pipeline possono essere concatenate (come mostrato nel diagramma) in modo che i set di dati di output di una pipeline possano diventare i set di dati di input per un'altra pipeline nello stesso hub di dati o in uno diverso.  
- **Pubblicare**. In questa fase, i dati sono pubblicati in modo da poter essere usati dagli strumenti BI, dagli strumenti di analisi e da altre applicazioni. Ad esempio, un'attività di copia nella pipeline può copiare i dati di output dall'elaborazione eseguita nella fase Transform & Enrich in un archivio dati (ad esempio, SQL Server locale) sulla base del quale è possibile creare soluzioni di business intelligence.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Passaggi successivi
1. [Introduzione a Data Factory][datafactory-getstarted]. Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
2. [Esercitazione: spostare ed elaborare i file di log utilizzando dati Factory][adf-tutorial]. In questo articolo fornisce un **procedura dettagliata end-to-end** che viene illustrato come implementare un **scenario reale** utilizza Azure dati Factory per trasformare i dati dai file di log in approfondimenti.

## Vedere anche
- [Dati Factory - terminologia][adf-terminology]. Questo articolo vengono introdotti la terminologia utilizzata nella creazione di factory di dati mediante il servizio di Factory di dati di Azure 
- [Factory dati - domande frequenti][adf-faq]. Questo articolo contiene un elenco di domande frequenti e le rispettive risposte.
- [Scenari comuni per l'utilizzo di dati Azure][adf-common-scenarios]. Questo articolo descrive alcuni scenari comuni di utilizzo del servizio Data factory di Azure. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
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

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png

<!---HONumber=GIT-SubDir--> 