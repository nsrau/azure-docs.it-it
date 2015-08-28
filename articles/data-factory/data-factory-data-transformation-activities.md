<properties 
	pageTitle="Attività di trasformazione dei dati | Microsoft Azure" 
	description="Informazioni su come utilizzare il servizio Data Factory di Azure per trasformare e analizzare i dati." 
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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Trasformazione e analisi tramite Data Factory di Azure
Le attività di trasformazione in Azure Data Factory trasformano ed elaborano i dati non elaborati in stime e approfondimenti. L'attività di trasformazione viene eseguita in un ambiente di elaborazione, ad esempio cluster HDInsight di Azure o un Batch di Azure. Data Factory di Azure supporta le seguenti attività di trasformazione che possono essere aggiunte a[pipeline](data-factory-create-pipelines.md)singolarmente o con un'altra attività concatenata.


Attività di trasformazione | Ambiente di calcolo 
----------------------- | --------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](https://msdn.microsoft.com/library/mt185698.aspx) | HDInsight [Hadoop]
[Machine Learning Batch Scoring](data-factory-create-predictive-pipelines.md) | Macchina virtuale di Azure 
[Stored procedure](data-factory-stored-proc-activity.md) | SQL di Azure | 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch di Azure    

È necessario creare un servizio collegato per l'ambiente di calcolo e quindi utilizzare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di ambienti di calcolo supportati da Data Factory.

1. **On Demand**: In questo caso, l'ambiente informatico è completamente gestito da Data Factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. Gli utenti possono configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico. 
2. **Bring Your Own**: In questo caso, è possibile registrare il proprio ambiente di elaborazione (ad esempio cluster HDInsight) come servizio collegato in Data Factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data Factory viene utilizzato per eseguire le attività. 

Vedere l’articolo[Servizi collegati di calcolo](data-factory-compute-linked-services.md)per informazioni sui servizi collegati di calcolo supportati da Data Factory.

<!----HONumber=August15_HO7-->