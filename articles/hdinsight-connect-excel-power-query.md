<properties linkid="manage-services-hdinsight-connect-excel-with-power-query" urlDisplayName="HDInsight and Excel" pageTitle="Connect Excel to HDInsight with Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Azure HDInsight with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Connessione di Excel a HDInsight mediante Power Query
=====================================================

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di Microsoft Business Intelligence (BI) con i cluster Hadoop HDInsight. Un esempio importante di questa integrazione è la possibilità di connettere Excel all'account di archiviazione di Azure contenente i dati associati al cluster HDInsight mediante Microsoft Power Query per Excel. In questo articolo viene illustrato come configurare e utilizzare Power Query da Excel per eseguire una query dei dati associati a un cluster HDInsight.

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo, è necessario disporre di quanto segue:

-   Un cluster HDInsight. Per configurarne uno, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/documentation/articles/hdinsight-get-started/).
-   Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

Contenuto dell'articolo
-----------------------

-   [Installazione di Microsoft Power Query per Excel](#InstallPowerQuery)
-   [Importazione di dati in Excel](#ImportData)
-   [Passaggi successivi](#NextSteps)

Installazione di Microsoft Power Query per Excel
------------------------------------------------

Power Query può essere utilizzato per importare dati da diverse origini in Microsoft Excel, dove può integrarsi con strumenti di business intelligence (BI), ad esempio PowerPivot e Power View. In particolare, Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight.

Scaricare Microsoft Power Query per Excel dall'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkID=286689) e installarlo.

Importazione di dati di HDInsight in Excel
------------------------------------------

Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti di business intelligence, ad esempio PowerPivot e Power Map, possono essere utilizzati per esaminare, analizzare e presentare i dati.

**Per importare dati da un cluster HDInsight**

1.  Aprire Excel.

2.  Creare una nuova cartella di lavoro vuota.

3.  Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

    ![HDI.PowerQuery.SelectHdiSource](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png)

    Nota: Se il menu **Power Query** non è visibile, andare a **File** \> **Opzioni** \> **Componenti aggiuntivi** e selezionare **Componenti aggiuntivi COM** nella casella di riepilogo **Gestisci** nella parte inferiore della pagina. Selezionare il pulsante **Vai** e verificare che la casella per il componente aggiuntivo Microsoft Office Power Query per Excel sia stata selezionata.

4.  Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**.

5.  Immettere la **Chiave account** per l'account di archiviazione BLOB e quindi fare clic su **Salva**. È necessario eseguire questa operazione solo la prima volta che si accede all'archivio.

6.  Nel riquadro **Strumento di navigazione** a sinistra dell'**Editor query** fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

7.  Individuare **HiveSampleData.txt** nella colonna **Name** (il percorso della cartella è **../hive/warehouse/hivesampletable/**) e quindi fare clic su **Binary** a sinistra di HiveSampleData.txt.

    ![HDI.PowerQuery.ImportData](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png)

8.  Se necessario, è possibile ridenominare le colonne. Quando lo si desidera, fare clic su **Apply & Close**.

    ![HDI.PowerQuery.ImportedTable](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG)

Passaggi successivi
-------------------

In questo articolo è stato illustrato come utilizzare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight in SQL Azure. È inoltre possibile caricare dati in HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](/en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver/)
-   [Caricamento di dati in HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)

