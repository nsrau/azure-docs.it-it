<properties 
	pageTitle="Factory di dati di Azure - terminologia" 
	description="Questo articolo vengono introdotti la terminologia utilizzata nella creazione di factory di dati mediante il servizio di Factory di dati di Azure" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Factory di dati di Azure - terminologia

## Data factory
Un **factory di dati di Azure** dispone di una o più pipeline che elaborano i dati in archivi dati collegati (Database SQL Azure, di archiviazione di Azure locale SQL Server e così via) tramite i servizi di calcolo collegato, ad esempio Azure HDInsight. Una data factory di Azure non contiene dati al suo stesso interno, ma i dati sono invece archiviati negli archivi dati citati sopra.

## Servizio collegato
Oggetto **collegati servizio** è un servizio che è collegato a una factory di dati di Azure. Un servizio collegato può essere:

- Oggetto **archiviazione dei dati** servizio, ad esempio l'archiviazione di Azure, Database SQL di Azure o SQL Server locale. Un archivio dati è un contenitore di set di dati di input/output.    
- Oggetto **calcolo** servizio HDInsight di Azure e Azure Machine Learning Batch di Azure. Un servizio di calcolo elabora i dati di input e produce i dati di output.  

## Set di dati
Oggetto **set di dati** è una visualizzazione denominata dei dati. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle tabelle relazionali o persino ai modelli. Una Factory di dati **tabella** è un set di dati che dispone di uno schema ed è rettangolare. Dopo aver creato un servizio collegato in un archivio dati che fa riferimento a un archivio dati, è necessario definire i set di dati che rappresentano i dati di input/output aggiunti all'archivio dati.


##Pipeline
Oggetto **pipeline** in un Azure factory dati elabora i dati in servizi di archiviazione collegati tramite i servizi di calcolo collegato. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Ad esempio, un **attività Copia** copia dati da un archivio di origine a un archivio di destinazione e **attività HDInsight** utilizzo di un cluster HDInsight di Azure per elaborare i dati tramite query Hive o script Pig. Una data factory può comprendere una o più pipeline.

I passaggi tipici per la creazione di un'istanza di Data factory di Azure sono:

1. Creare un **factory dati**.
2. Creare un **collegati servizio** per ogni archivio o servizio di calcolo.
3. Creazione di input e output **set di dati**.
4. Creare un **pipeline**. 

##Attività
Un passaggio di elaborazione dati in una pipeline che acquisisce uno o più set di dati di input e genera uno o più set di dati di output. Le attività vengono eseguite in un ambiente di esecuzione (ad esempio: cluster HDInsight di Azure) e leggere e scrivere dati in un archivio dati associati o collegati con la factory di dati di Azure.

Il servizio Data factory di Azure supporta le attività seguenti in una pipeline:

- **Attività Copia** copia i dati da un archivio dati in un altro archivio dati. Vedere [copiare i dati con Azure dati Factory][copy-data-with-adf] per informazioni dettagliate su quali dati vengono archiviati l'attività Copia supporta. 
- **Attività HDInsight** elabora i dati tramite l'esecuzione di programmi MapReduce o Hive/Pig script su un cluster HDInsight. Vedere [utilizzare Pig e Hive con dati Factory][use-pig-hive] e [richiamare i programmi MapReduce dalla Factory dati][run-map-reduce] per informazioni dettagliate. 
- **Azure Machine Learning Batch punteggio attività** richiama il batch di apprendimento automatico Azure punteggio API. Vedere [creare predittiva pipeline utilizzando la Factory di dati di Azure e Azure Machine Learning][azure-ml-adf] per informazioni dettagliate. 
- **Stored Procedure attività** richiama una stored procedure in un Database di SQL Azure. Vedere il [Stored Procedure attività][msdn-stored-procedure-activity] in MSDN Library per ulteriori informazioni.   

##Sezione
Una tabella in una data factory di Azure è costituita da sezioni. La larghezza di una sezione viene determinata dalla pianificazione, oraria o giornaliera. Quando la pianificazione è oraria, viene prodotta una sezione ogni ora con l'ora di inizio e di fine di una pipeline. Ad esempio, se la pipeline data-ora di inizio è 03/03/2015 06:00:00 (6 AM) e data-ora di fine è 03-03/2015 09:00:00 (9 AM nello stesso giorno), tre dati vengono prodotte le sezioni, una sezione per ogni intervallo di 1 ora: 6-7 AM, 7-8 del Mattino e 8-9: 00.

Intervalli di offrono la possibilità di utilizzare un subset di dati complessivi per un intervallo di tempo specifico (ad esempio: la sezione prodotti per la durata (ore): 1:00 PM alle 2:00).

## Esecuzione di attività per una sezione
Il **eseguire** o un'attività in esecuzione è un'unità di elaborazione per una sezione. A una sezione possono essere associate una o più esecuzioni se vengono eseguiti più tentativi o se si ripete l'esecuzione della sezione in caso di errori. Una sezione viene identificata in base all'ora di inizio.

##Gateway di gestione dati
Microsoft **Gateway di gestione dati** è un software che si connette origini dati locali al cloud services per l'utilizzo. Prima di aggiungere origini dati locali come servizi collegati, è necessario che nell'ambiente aziendale sia installato almeno un gateway registrato con il portale di Data factory di Azure.
 
##Hub di dati
Oggetto **Hub dati** è un contenitore logico per i servizi di calcolo e archiviazione dati. Ad esempio, un cluster Hadoop con HDFS come archiviazione e Hive/Pig come calcolo (elaborazione) è un hub di dati. Analogamente, un data warehouse aziendale (EDW) può essere modellato come hub di dati (database come archiviazione, stored procedure e/o strumento ETL come servizi di calcolo). Le pipeline usano gli archivi dati e sono eseguite su risorse di calcolo in un hub di dati. Al momento è supportato solo l'hub HDInsight.

L'hub di dati consente di dividere un'istanza di Data factory in gruppi logici o di dominio specifici, ad esempio “West US Azure Hub” che gestisce tutti i servizi collegati (archivi dati e calcolo) e le pipeline concentrati nel data center degli Stati Uniti occidentali oppure “Sales EDW Hub” che gestisce tutti i servizi collegati e le pipeline associati al popolamento e all'elaborazione dei dati per l'EDW di vendita.

Una caratteristica importante dell'hub riguarda il fatto che una pipeline viene eseguita in un singolo hub. Ciò significa che, quando si definisce una pipeline, tutti i servizi collegati a cui fanno riferimento le tabelle o le attività nella pipeline devono avere lo stesso nome hub della pipeline. Se la proprietà HubName non è specificata per un servizio collegato, questo viene inserito nell'hub predefinito.

## Vedere anche

1. [Introduzione ai dati di Azure Factory][adf-intro]. Questo articolo fornisce una panoramica del servizio Data factory di Azure, del valore offerto dal servizio e degli scenari supportati.
2. [Introduzione a Data Factory][datafactory-getstarted]. Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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