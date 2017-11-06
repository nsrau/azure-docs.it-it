
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory è un servizio di integrazione di dati. Consente di creare flussi di lavoro basati sui dati nel cloud. Un flusso di lavoro viene implementato come una o più *pipeline*. Le pipeline orchestrano e automatizzano lo spostamento dei dati e la trasformazione dei dati. Le pipeline possono eseguire la sequenza seguente con i dati:

1. Inserire dati da archivi dati diversi.
2. Trasformare o elaborare i dati usando servizi di calcolo, tra cui:
    - Azure HDInsight Hadoop
    - Spark
    - Azure Data Lake Analytics.
    - Azure Machine Learning
3. Pubblicare i dati di output negli archivi dati.
    - La destinazione di pubblicazione può essere un'istanza di Azure SQL Data Warehouse destinata ad applicazioni di business intelligence (BI). 

È possibile pianificare le pipeline tramite Data Factory.

