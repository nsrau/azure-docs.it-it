<properties
	pageTitle="Connettere Excel a Hadoop mediante Power Query | Microsoft Azure"
	description="Informazioni su come sfruttare i componenti di business intelligence e usare Power Query per Excel per accedere ai dati archiviati in Hadoop in HDInsight."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="jgao"/>


#Connettere Excel a Hadoop mediante Power Query

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di business intelligence (BI) Microsoft con i cluster Hadoop in Azure HDInsight. Un esempio importante di questa integrazione è la possibilità di connettere Excel all'account di archiviazione di Azure che contiene i dati associati al cluster Hadoop usando il componente aggiuntivo Microsoft Power Query per Excel. Questo articolo illustra come configurare e usare Power Query per eseguire una query sui dati associati a un cluster Hadoop gestito con HDInsight.

> [AZURE.NOTE]Mentre i passaggi descritti in questo articolo possono essere utilizzati con un cluster HDInsight basato su Windows o Linux, Windows è necessario per la workstation client.

## Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Un cluster HDInsight**. Per configurarne uno, vedere [Introduzione ad Azure HDInsight][hdinsight-get-started].
- **Una workstation** che esegue il sistema operativo Windows 7, Windows Server 2008 R2 o una versione successiva.
- **Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus**.


## <a id="InstallPowerQuery"></a>Installare Microsoft Power Query per Excel

Power Query può essere usato per importare dati da diverse origini in Microsoft Excel, dove può integrarsi con strumenti BI quali PowerPivot e Power View. In particolare, Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight.

Scaricare Microsoft Power Query per Excel dall'[Area download Microsoft][powerquery-download] e installarlo.

## <a id="ImportData"></a>Importare dati di HDInsight in Excel

Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti BI quali PowerPivot e Power Map possono essere usati per esaminare, analizzare e presentare i dati.

**Per importare dati da un cluster HDInsight**

1. Aprire Excel.

2. Creare una nuova cartella di lavoro vuota.

3. Dal menu **Power Query** scegliere **Da Azure**, quindi fare clic su **Da Microsoft Azure HDInsight**.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	**Nota:** se il menu **Power Query** non è visibile, passare a **File** > **Opzioni** > **Componenti aggiuntivi** e selezionare **Componenti aggiuntivi COM** nella casella di riepilogo **Gestisci** nella parte inferiore della pagina. Fare clic sul pulsante **Vai** e verificare che la casella per il componente aggiuntivo Power Query per Excel sia selezionata.

	**Nota:** Power Query consente inoltre di importare dati da HDFS facendo clic su **Da altre origini**.

3. Per **Nome account** immettere il nome dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**.

4. Per **Chiave account** immettere la chiave dell'account di archiviazione BLOB, quindi fare clic su **Salva**. È necessario eseguire questa operazione solo la prima volta che si accede all'archivio.

5. Nel riquadro **Strumento di navigazione** a sinistra dell'editor di query fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

6. Individuare **HiveSampleData.txt** nella colonna **Name** (il percorso della cartella è **../hive/warehouse/hivesampletable/**) e quindi fare clic su **Binary** a sinistra di HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se necessario, è possibile ridenominare le colonne. Quando lo si desidera, fare clic su **Apply & Close**.

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight nel database SQL di Azure. È inoltre possibile caricare dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-ODBC]
* [Caricare dati in HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

<!---HONumber=Oct15_HO3-->