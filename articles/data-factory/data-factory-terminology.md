<properties 
	pageTitle="Data Factory di Azure - Terminologia" 
	description="Questo articolo presenta la terminologia usata nella creazione di data factory mediante il servizio Data factory di Azure." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

#Data Factory di Azure - Terminologia

## Data factory
Una **data factory di Azure** comprende una o più pipeline che elaborano dati in archivi dati collegati (Archiviazione di Azure, database SQL di Azure, SQL Server locale e così via) usando servizi di calcolo collegati come Azure HDInsight. Una data factory di Azure non contiene dati al suo stesso interno, ma i dati sono invece archiviati negli archivi dati citati sopra.

## Servizio collegato
Un **servizio collegato** è un servizio collegato a una data factory di Azure. Un servizio collegato può essere:

- Un **servizio di archiviazione dei dati** come Archiviazione di Azure, il database SQL di Azure o il database di SQL Server locale. Un archivio dati è un contenitore di set di dati di input/output.    
- Un servizio di **calcolo** come Azure HDInsight, Azure Machine Learning e Azure Batch. Un servizio di calcolo elabora i dati di input e produce i dati di output.  

## Set di dati
Un **set di dati** è una visualizzazione di dati denominata. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle tabelle relazionali o persino ai modelli. Una **tabella** di Data factory è un set di dati che contiene uno schema ed è di forma rettangolare. Dopo aver creato un servizio collegato in un archivio dati che fa riferimento a un archivio dati, è necessario definire i set di dati che rappresentano i dati di input/output aggiunti all'archivio dati.


##Pipeline
Una **pipeline** in una data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Ad esempio, un'**Attività di copia** copia i dati da un archivio di origine a un archivio di destinazione, mentre un'**Attività di HDInsight** usa un cluster Azure HDInsight per elaborare i dati mediante query Hive o script Pig. Una data factory può comprendere una o più pipeline.

I passaggi tipici per la creazione di un'istanza di Data factory di Azure sono:

1. Creare una **data factory**.
2. Creare un **servizio collegato** per ogni archivio dati o servizio di calcolo.
3. Creare **set di dati** di input e di output.
4. Creare una **pipeline**. 

##Attività
Un passaggio di elaborazione dati in una pipeline che acquisisce uno o più set di dati di input e genera uno o più set di dati di output. Le attività vengono eseguite in un ambiente di esecuzione (ad esempio, un cluster Azure HDInsight) e leggono/scrivono i dati in un archivio dati associato/collegato alla data factory di Azure.

Il servizio Data factory di Azure supporta le attività seguenti in una pipeline:

- Un'**Attività di copia** copia i dati da un archivio dati a un altro archivio dati.  
- L'**Attività di HDInsight** elabora i dati tramite l'esecuzione di script Hive/Pig o di programmi MapReduce in un cluster HDInsight. Per informazioni dettagliate, vedere [Usare Pig e Hive con Data factory][use-pig-hive] e [Richiamare i programmi MapReduce da Data factory][run-map-reduce]. 
- L'**Attività di punteggio batch di Azure Machine Learning** richiama l'API di punteggio batch di Azure Machine Learning. Per informazioni dettagliate, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning][azure-ml-adf]. 
- L'**Attività stored procedure** richiama una stored procedure in un database SQL di Azure. Per informazioni dettagliate, vedere [Attività stored procedure][msdn-stored-procedure-activity] in MSDN Library.   

##Sezione
Una tabella in una data factory di Azure è costituita da sezioni. La larghezza di una sezione viene determinata dalla pianificazione, oraria o giornaliera. Quando la pianificazione è oraria, viene prodotta una sezione ogni ora con l'ora di inizio e di fine di una pipeline. Ad esempio, se la data e le ore di inizio e di fine della pipeline sono rispettivamente 03/03/2015 alle 6.00 e 03/03/2015 alle 9.00 dello stesso giorno, vengono prodotte tre sezioni, una per ogni intervallo di un'ora: 6.00-7.00, 7.00-8.00 e 8.00-9.00.

Le sezioni consentono di usare un subset di tutti i dati per un intervallo di tempo specifico, ad esempio la sezione prodotta per la durata (oraria): dalle 13.00 alle 14.00.

## Esecuzione di attività per una sezione
Un'**esecuzione** o un'esecuzione di attività è un'unità di elaborazione per una sezione. A una sezione possono essere associate una o più esecuzioni se vengono eseguiti più tentativi o se si ripete l'esecuzione della sezione in caso di errori. Una sezione viene identificata in base all'ora di inizio.

##Gateway di gestione dati
Il **Gateway di gestione dati** Microsoft è un software che connette origini dati locali a servizi cloud ai fini del consumo. Prima di aggiungere origini dati locali come servizi collegati, è necessario che nell'ambiente aziendale sia installato almeno un gateway registrato con il portale di Data factory di Azure.
 
##Hub di dati
Un **hub di dati** è un contenitore logico per servizi di archiviazione e di calcolo dei dati. Ad esempio, un cluster Hadoop con HDFS come archiviazione e Hive/Pig come calcolo (elaborazione) è un hub di dati. Analogamente, un data warehouse aziendale (EDW) può essere modellato come hub di dati (database come archiviazione, stored procedure e/o strumento ETL come servizi di calcolo). Le pipeline usano gli archivi dati e sono eseguite su risorse di calcolo in un hub di dati. Al momento è supportato solo l'hub HDInsight.

L'hub di dati consente di dividere un'istanza di Data factory in gruppi logici o di dominio specifici, ad esempio “West US Azure Hub” che gestisce tutti i servizi collegati (archivi dati e calcolo) e le pipeline concentrati nel data center degli Stati Uniti occidentali oppure “Sales EDW Hub” che gestisce tutti i servizi collegati e le pipeline associati al popolamento e all'elaborazione dei dati per l'EDW di vendita.

Una caratteristica importante dell'hub riguarda il fatto che una pipeline viene eseguita in un singolo hub. Ciò significa che, quando si definisce una pipeline, tutti i servizi collegati a cui fanno riferimento le tabelle o le attività nella pipeline devono avere lo stesso nome hub della pipeline. Se la proprietà HubName non è specificata per un servizio collegato, questo viene inserito nell'hub predefinito.

## Vedere anche

1. [Introduzione a Data factory di Azure][adf-intro]. Questo articolo fornisce una panoramica del servizio Data factory di Azure, del valore offerto dal servizio e degli scenari supportati.
2. [Introduzione a Data factory di Azure][datafactory-getstarted]. Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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



 

<!---HONumber=August15_HO7-->