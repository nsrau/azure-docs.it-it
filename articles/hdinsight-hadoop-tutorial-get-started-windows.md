<properties 
   pageTitle="Esercitazione di Hadoop: Introduzione a Hadoop in Windows | Microsoft Azure" 
   description="Introduzione a Hadoop in HDInsight Informazioni su come eseguire il provisioning di cluster Hadoop in Windows, eseguire una query Hive sui dati e analizzare l'output in Excel." 
   keywords="hadoop tutorial,hadoop on windows,hadoop cluster,learn hadoop, hive query"
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/20/2015"
   ms.author="nitinme"/>


# Esercitazione di Hadoop: Introduzione ad Hadoop e a una query Hive in HDInsight in Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-get-started.md)
- [Linux](hdinsight-hadoop-linux-get-started.md)

Per informazioni su Hadoop in Windows e per iniziare a usare HDInsight, questa esercitazione illustra come eseguire una query Hive su dati non strutturati in un cluster Hadoop e quindi analizzare i risultati in Microsoft Excel.

## Obiettivi di questa esercitazione di Hadoop 

Si supponga di avere un set di dati non strutturati di grandi dimensioni e di volere eseguire una query Hive sul set di dati per estrarre informazioni significative. Questo è l'obiettivo dell'esercitazione. Il risultato sarà ottenuto nel modo seguente:

   ![Esercitazione di Hadoop: Creare un account, eseguire il provisioning di un cluster Hadoop, inviare una query Hive e analizzare i dati in Excel.][image-hdi-getstarted-flow]

Guardare un video dimostrativo di questa esercitazione per imparare a usare Hadoop su HDInsight:

![Video della prima esercitazione di Hadoop: Inviare una query Hive in un cluster Hadoop e analizzare i risultati in Excel.][img-hdi-getstarted-video]

**[Guardare l'esercitazione di Hadoop per HDInsight su YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft offre anche HDInsight Emulator per Azure, noto in precedenza come *Microsoft HDInsight Developer Preview*. L'emulatore è stato progettato per scenari relativi allo sviluppo e supporta solo distribuzioni a nodo singolo. Per informazioni sull'uso di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator][hdinsight-emulator].

> [AZURE.NOTE]Per istruzioni su come effettuare il provisioning di un cluster HBase, vedere [Provisioning di cluster HBase in HDInsight][hdinsight-hbase-custom-provision]. Per capire perché scegliere un database piuttosto che un altro, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Differenze tra Hadoop e HBase</a>.

## Prerequisiti

Prima di iniziare questa esercitazione per Hadoop, è necessario disporre di quanto segue:


- Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
- Un computer con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento dell'esercitazione:** 30 minuti



##<a name="storage"></a>Creare un account di archiviazione di Azure

Quando si esegue il provisioning di un cluster Hadoop in HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account viene designato come file system predefinito, come in HDFS (Hadoop Distributed File System, file system distribuito Hadoop). Per impostazione predefinita, il provisioning del cluster HDInsight viene effettuato nello stesso data center dell'account di archiviazione specificato. Per altre informazioni, vedere l'argomento relativo all'[uso dell'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

>[AZURE.NOTE]Non condividere un contenitore di archiviazione BLOB predefinito con più cluster Hadoop.

Oltre a questo account di archiviazione, è possibile aggiungere altri account di archiviazione durante la configurazione personalizzata di un cluster. L'account di archiviazione aggiuntivo può appartenere alla stessa sottoscrizione di Azure o a sottoscrizioni di Azure diverse. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision].

Questa esercitazione usa solo il BLOB e l'account di archiviazione predefiniti.

**Per creare un account di archiviazione di Azure**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **NUOVO** nell'angolo in basso a sinistra e quindi immettere i valori come illustrato nell'immagine.

	![Portale di Azure dove è possibile usare la funzione di creazione rapida per configurare un nuovo account di archiviazione.][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]Assicurarsi quindi di creare l'account di archiviazione in una località supportata dal cluster, ovvero: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali**.

Selezionare il nuovo account di archiviazione dall'elenco e fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina. Prendere nota dei valori di **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA** (funzionano entrambe le chiavi). Sarà necessario usarli più avanti nell'esercitazione. Per altre informazioni, vedere l'articolo relativo alla [creazione di un account di archiviazione][azure-create-storageaccount].
	
##<a name="provision"></a>Provisioning di un cluster Hadoop

Quando si esegue il provisioning di un cluster, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. In questa sezione si effettua il provisioning di un cluster HDInsight versione 3.1, basato su Hadoop versione 2.4. È anche possibile creare cluster Hadoop per altre versioni usando il portale di Azure, i cmdlet PowerShell HDInsight oppure HDInsight .NET SDK. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision]. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Per eseguire il provisioning di un cluster Hadoop**

1. Accedere al [portale di Azure][azure-management-portal]. 

2. Fare clic su **NUOVO** nell'angolo in basso a sinistra e quindi immettere i valori come illustrato nell'immagine.

	![Creazione di un cluster Hadoop in HDInsight.][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:

	
	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account. 
	</td></tr>
	</table>
	
	Keep a copy of the cluster name. You will need it later in the tutorial.

	
5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE]Questi passaggi fanno riferimento al provisioning di un cluster HDInsight versione 3.1. Per creare il cluster con altre versioni, usare il metodo **Creazione personalizzata** del portale o Azure PowerShell. Per informazioni sulle differenze tra ogni versione, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions]. Per informazioni su come usare l'opzione **CREAZIONE PERSONALIZZATA**, vedere [Effettuare il provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision].


##<a name="sample"></a>Eseguire dati di esempio dal portale

Un cluster HDInsight il cui provisioning è stato effettuato correttamente fornisce una console di query che include una raccolta introduttiva per l'esecuzione di esempi direttamente dal portale. Gli esempi possono essere usati per apprendere come lavorare con HDInsight seguendo le procedure dettagliare di alcuni scenari di base. Gli esempi forniscono tutti i componenti necessari, come i dati per l'analisi e le query da eseguire sui dati. Per altre informazioni sugli esempi della raccolta introduttiva, vedere [Informazioni su Hadoop in HDInsight tramite la raccolta di introduzione HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Per eseguire l'esempio**, dal portale di Azure fare clic sul nome del cluster in cui si vuole eseguire l'esempio e quindi su **Console query** nella parte inferiore della pagina. Nella pagina Web visualizzata fare clic sulla scheda **Guida introduttiva** e quindi nella categoria **Esempi** fare clic sull'esempio da eseguire. Seguire le istruzioni nella pagina Web per completare l'esempio. La tabella seguente elenca alcuni esempi e fornisce informazioni aggiuntive sulla funzione di ogni esempio.

Esempio | Funzione
------ | ---------------
[Analisi dei dati dei sensori][hdinsight-sensor-data-sample] | Informazioni su come usare HDInsight per l'elaborazione dei dati cronologici prodotti dai sistemi HVAC (Heating, Ventilating and Air Conditioning, riscaldamento, ventilazione e condizionamento dell'aria) per identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata.
[Analisi del log del sito Web][hdinsight-weblogs-sample] | Informazioni su come usare HDInsight per analizzare i file di log dei siti Web per ottenere informazioni dettagliate sulla frequenza delle visite nel sito Web nell'arco di un giorno da siti Web esterni, nonché un riepilogo degli errori nei siti Web riscontrati dagli utenti.
[Analisi delle tendenze di Twitter](hdinsight-analyze-twitter-data.md) | Informazioni su come usare HDInsight per analizzare le tendenze in Twitter.



##<a name="hivequery"></a>Eseguire una query Hive dal portale
Dopo il provisioning di un cluster HDInsight, il passaggio successivo consiste nell'esecuzione di un processo Hive per eseguire query su una tabella Hive di esempio. Verrà usata la tabella *hivesampletable*, disponibile con i cluster HDInsight. La tabella contiene dati relativi a produttori, piattaforme e modelli di dispositivi mobili. Una query Hive in questa tabella recupera i dati per i dispositivi mobili di un produttore specifico.

> [AZURE.NOTE]HDInsight Tools per Visual Studio è disponibile in Azure SDK per .NET versione 2.5 o versione successiva. Usando gli strumenti in Visual Studio, sarà possibile connettersi al cluster HDInsight, creare tabelle Hive ed eseguire query Hive. Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio][1].

**Per eseguire un processo Hive dal dashboard del cluster**

1. Accedere al [portale di Azure][azure-management-portal]. 
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster, incluso quello appena creato nella sezione precedente.
3. Fare clic sul nome del cluster che si vuole usare per eseguire il processo Hive e quindi fare clic su **CONSOLE QUERY** nella parte inferiore della pagina. 
4. Verrà aperta una pagina Web in una scheda diversa del browser. Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il provisioning del cluster. L'aspetto del dashboard è simile al seguente:

	![Scheda Editor Hive nel dashboard del cluster HDInsight.][img-hdi-dashboard]

	Nella parte superiore della pagina sono presenti diverse schede. La scheda predefinita è **Editor Hive** e le altre schede sono **Cronologia processo** e **Browser file**. Usando il dashboard, è possibile inviare query Hive, controllare i log dei processi Hadoop ed esplorare i file nell'archivio.

	> [AZURE.NOTE]Si noti che l'URL della pagina Web è *&lt;NomeCluster&gt;.azurehdinsight.net*. Invece di aprire il dashboard dal portale, è quindi possibile aprirlo da un Web browser usando l'URL.

6. Nella scheda **Editor Hive** in **Nome query** immettere **HTC20**. Il nome della query corrisponde al nome del processo. Nel riquadro della query immettere la query Hive come illustrato nell'immagine:
		
	![Query Hive immessa nel riquadro di query dell'Editor Hive.][img-hdi-dashboard-query-select]

4. Fare clic su **Submit**. Per ottenere i risultati sono necessari alcuni minuti. La schermata viene aggiornata ogni 30 secondi. È anche possibile fare clic su **Aggiorna** per aggiornare la schermata.

    ![Risultati di una query Hive nella parte inferiore del dashboard del cluster.][img-hdi-dashboard-query-select-result]

5. Quando lo stato indica che il processo è stato completato, fare clic sul nome della query nella schermata per visualizzare l'output. Annotare il valore per **Ora inizio processo (UTC)**. Sarà necessario più avanti.

    ![Ora di inizio processo nella scheda Cronologia processo del dashboard del cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    Questa pagina mostra anche i valori per **Output processo** e **Registro processo**. Sarà anche possibile scaricare il file di output (_stdout) e il file di log (_stderr).


**Per passare al file di output**

1. Nel dashboard del cluster fare clic su **Browser file**. 
2. Fare clic sul nome dell'account di archiviazione, quindi sul nome del contenitore (che corrisponde al nome del cluster) e infine su **utente**.
3. Fare clic su **amministratore** e quindi sul GUID modificato più di recente (poco dopo l'ora di avvio del processo annotata in precedenza). Copiare il GUID. Sarà necessario nella sezione successiva.


   	![GUID file di output della query Hive nella scheda Browser file.][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Connettersi agli strumenti di business intelligence Microsoft per Excel 

È possibile usare il componente aggiuntivo Power Query per Microsoft Excel per importare l'output del processo da HDInsight in Excel, dove si possono usare gli strumenti di Microsoft Business Intelligence per analizzare ulteriormente i risultati.

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2013 o 2010.

**Per scaricare Microsoft Power Query per Excel**

- Scaricare Microsoft Power Query per Microsoft Excel dall'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) e installarlo.

**Per importare dati di HDInsight**

1. Aprire Excel e creare una nuova cartella di lavoro.
3. Dal menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

	![Menu di importazione PowerQuery in Excel per Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Immettere **Nome account** per l'account di archiviazione BLOB di Azure associato al cluster e quindi fare clic su **OK**. Si tratta dell'account di archiviazione creato in precedenza nell'esercitazione.
4. Immettere **Chiave account** per l'account di archiviazione BLOB di Azure e quindi fare clic su **Salva**. 
5. Nel riquadro destro fare doppio clic sul nome del BLOB. Per impostazione predefinita, il nome del BLOB corrisponde al nome del cluster. 

6. Individuare **stdout** nella colonna **Nome**. Assicurarsi che il GUID nella colonna **Percorso cartella** corrispondente sia uguale al GUID annotato in precedenza. Una corretta corrispondenza indica che i dati di output corrispondono al processo inviato. Fare clic su **Binario** nella colonna a sinistra di **stdout**.

	![Ricerca dell'output dei dati in base al GUID nell'elenco di contenuti.][image-hdi-gettingstarted-powerquery-importdata2]

9. Fare clic su **Carica e chiudi** nell'angolo superiore sinistro per importare l'output del processo Hive in Excel.


##<a name="nextsteps"></a>Passaggi successivi
In questa esercitazione di Hadoop si è appreso come effettuare il provisioning di un cluster Hadoop in Windows in HDInsight, eseguire una query Hive sui dati e importare i risultati in Excel per elaborarli ulteriormente e visualizzarli graficamente mediante gli strumenti di business intelligence. Per altre informazioni, vedere le esercitazioni seguenti:

- [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio][1]
- [Introduzione a HDInsight Emulator][hdinsight-emulator]
- [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]
- [Amministrazione di HDInsight tramite PowerShell][hdinsight-admin-powershell]
- [Caricare dati in HDInsight][hdinsight-upload-data]
- [Usare MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Usare Oozie con HDInsight][hdinsight-use-oozie]
- [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]


[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: install-configure-powershell.md
[powershell-open]: install-configure-powershell.md#Install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=58-->