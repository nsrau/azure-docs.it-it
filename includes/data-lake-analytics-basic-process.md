**Processo di Data Lake Analytics di base:**

![Diagramma di flusso del processo di Azure Data Lake Analytics](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)

1. Creare un account di Analisi Data Lake.
2. Preparare i dati di origine. I processi di Data Lake Analytics possono leggere dati da account di Azure Data Lake Store o da account di archiviazione BLOB di Azure.   
3. Sviluppare uno script U-SQL.
4. Inviare un processo (script U-SQL) all'account di Analisi Data Lake. Il processo legge i dati di origine, elabora i dati come indicato nello script U-SQL e quindi salva l'output in un account di Data Lake Store o in un account di archiviazione BLOB.



<!--HONumber=Jan17_HO3-->


