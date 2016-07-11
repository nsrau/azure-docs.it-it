<properties 
	pageTitle="Trasformazione dei dati: elaborare e trasformare i dati | Microsoft Azure" 
	description="Informazioni sulla trasformazione dei dati in Azure Data Factory. Trasformare ed elaborare i dati in un cluster HDInsight di Azure o in un'istanza di Azure Batch." 
	keywords="trasformazione di dati, elaborare dati, trasformare dati, attività di trasformazione"
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Informazioni sulla trasformazione e l'analisi dei dati in Azure Data Factory
Questo articolo illustra le attività di trasformazione dei dati in Azure Data Factory (ADF) per spiegare come i dati non elaborati vengono trasformati ed elaborati in stime e informazioni dettagliate. L'attività di trasformazione viene eseguita in un ambiente di elaborazione, ad esempio cluster HDInsight di Azure o un Batch di Azure. Sono anche inclusi collegamenti ad articoli che spiegano come usare attività di trasformazione specifiche.
 
Data factory di Azure supporta le seguenti attività di trasformazione che possono essere aggiunte a[pipeline](data-factory-create-pipelines.md)singolarmente o con un'altra attività concatenata.

Attività di trasformazione dei dati | Ambiente di calcolo 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Attività di Machine Learning: Esecuzione batch e Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md) | Macchina virtuale di Azure 
[Stored procedure](data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL Data Warehouse o SQL Server |
[Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) | Azure Data Lake Analytics. 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch di Azure
   
> [AZURE.NOTE] 
È possibile usare l'attività MapReduce per eseguire i programmi Spark nel cluster HDInsight Spark. Per informazioni dettagliate, vedere [Chiamare i programmi Spark da Data Factory](data-factory-spark.md). È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Esempio relativo all'esecuzione di script R con Azure Data Factory).
 

È necessario creare un servizio collegato per l'ambiente di calcolo e quindi utilizzare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di ambienti di calcolo supportati da Data factory.

1. **On Demand**: In questo caso, l'ambiente informatico è completamente gestito da Data factory. Viene automaticamente creato dal servizio Data factory prima che un processo venga inviato per l'elaborazione dati e rimosso quando viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico.
2. **Bring Your Own**: In questo caso, è possibile registrare il proprio ambiente di elaborazione (ad esempio cluster HDInsight) come servizio collegato in Data factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data factory viene usato per eseguire le attività.

Per informazioni sui servizi collegati di calcolo supportati da Data factory, vedere l'articolo [Servizi collegati di calcolo](data-factory-compute-linked-services.md).

<!---HONumber=AcomDC_0629_2016-->