Azure Data Factory supporta le seguenti attività di trasformazione che possono essere aggiunte alle pipeline singolarmente o con un'altra attività concatenata.

Attività di trasformazione dei dati | Ambiente di calcolo 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop] 
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop] 
[Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] 
[Attività di Machine Learning: Esecuzione batch e Aggiorna risorsa](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Macchina virtuale di Azure 
[Stored procedure](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL Data Warehouse o SQL Server |
[Attività U-SQL di Data Lake Analytics](../articles/data-factory/data-factory-usql-activity.md) | Azure Data Lake Analytics. 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch di Azure
   
> [AZURE.NOTE] 
È possibile usare l'attività MapReduce per eseguire i programmi Spark nel cluster HDInsight Spark. Per informazioni dettagliate, vedere [Chiamare i programmi Spark da Data Factory](../articles/data-factory/data-factory-spark.md). 
È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Esempio relativo all'esecuzione di script R con Azure Data Factory).

<!---HONumber=AcomDC_0928_2016-->
