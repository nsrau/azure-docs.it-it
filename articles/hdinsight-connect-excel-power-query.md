<properties urlDisplayName="HDInsight and Excel" pageTitle="Connettere Excel a Hadoop mediante Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Connettere Excel a Hadoop mediante Power Query

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di business intelligence (BI) Microsoft con i cluster Hadoop in HDInsight. Un esempio importante di questa integrazione è la possibilità di connettere Excel all'account di archiviazione di Azure contenente i dati associati al cluster Hadoop mediante Microsoft Power Query per Excel. In questo articolo è illustrato come configurare e usare Power Query da Excel per eseguire una query dei dati associati a un cluster Hadoop gestito con HDInsight. 

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

- Un cluster HDInsight. Per configurarne uno, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started].
- Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## Contenuto dell'articolo

- [Installazione di Microsoft Power Query per Excel](#InstallPowerQuery)
- [Importazione di dati in Excel](#ImportData)
- [Passaggi successivi](#NextSteps)


## <a id="InstallPowerQuery"></a>Installazione di Microsoft Power Query per Excel

Power Query può essere usato per importare dati da diverse origini in Microsoft Excel, dove può integrarsi con strumenti di business intelligence (BI), ad esempio PowerPivot e Power View. In particolare, Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight. 

Scaricare Microsoft Power Query per Excel dall'[Area download Microsoft][powerquery-download] e installarlo.

## <a id="ImportData"></a>Importazione di dati di HDInsight in Excel

Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti di business intelligence, ad esempio PowerPivot e Power Map, possono essere usati per esaminare, analizzare e presentare i dati.

**Per importare dati da un cluster HDInsight**

1. Aprire Excel.

2. Creare una nuova cartella di lavoro vuota.

3. Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Nota: Se il menu **Power Query** non è visibile, andare a **File** > **Opzioni** > **Componenti aggiuntivi** e selezionare **Componenti aggiuntivi COM** nella casella di riepilogo **Gestisci** nella parte inferiore della pagina. Selezionare il pulsante **Vai** e verificare che la casella per il componente aggiuntivo Microsoft Office Power Query per Excel sia stata selezionata.

3. Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. 

4. Immettere la **Chiave account** per l'account di archiviazione BLOB, quindi fare clic su **Salva**. È necessario eseguire questa operazione solo la prima volta che si accede all'archivio.	

5. Nel riquadro **Strumento di spostamento** a sinistra dell'**Editor query** fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster. 

6. Trovare **HiveSampleData.txt** nella colonna **Name** (il percorso della cartella è **../hive/warehouse/hivesampletable/**), quindi fare clic su **Binary** a sinistra di HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se necessario, è possibile rinominare le colonne. Quando si è pronti, fare clic su **Applica e chiudi**.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight in SQL Azure. È inoltre possibile caricare dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-ODBC]
* [Caricare i dati in HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

<!--HONumber=35_1-->
