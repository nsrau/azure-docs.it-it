<properties 
	pageTitle="Introduzione all'uso di Hadoop con Hive in HDInsight | Azure" 
	description="Introduzione all'uso di Hadoop in HDInsight: una soluzione Big Data nel cloud. Informazioni sul provisioning di cluster, sull'esecuzione di query sui dati con Hive e sull'output dei dati in Excel per l'analisi." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="nitinme"/>


# Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

Per consentire di iniziare rapidamente a usare HDInsight, questa esercitazione mostra come eseguire una query Hive per estrarre informazioni significative da dati non strutturati in un cluster Hadoop. I risultati verranno quindi analizzati in Microsoft Excel.


> [AZURE.NOTE] Se non si ha esperienza di Hadoop e dell'uso dei Big Data, sono disponibili altre informazioni su [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] e [Hive][apache-hive]. Per informazioni sull'abilitazione di Hadoop in Azure tramite HDInsight, vedere [Introduzione a Hadoop in HDInsight][hadoop-hdinsight-intro].

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft offre anche HDInsight Emulator per Azure, noto in precedenza come *Microsoft HDInsight Developer Preview*. L'emulatore è stato progettato per scenari relativi allo sviluppo e supporta solo distribuzioni a nodo singolo. Per informazioni sull'uso di HDInsight Emulator, vedere [Introduzione all'uso di HDInsight Emulator][hdinsight-emulator].

> [AZURE.NOTE] Per istruzioni su come effettuare il provisioning di un cluster HBase, vedere [Effettuare il provisioning di cluster HBase in HDInsight][hdinsight-hbase-custom-provision]. Vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Differenze tra Hadoop e HBase</a> per informazioni utili per la scelta del cluster appropriato.   

## Quali risultati si ottengono con questa esercitazione? ##

Si supponga di avere un set di dati non strutturati di grandi dimensioni e di volere eseguire query sul set di dati per estrarre informazioni significative. Questo è l'obiettivo dell'esercitazione. Il risultato sarà ottenuto nel modo seguente:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

È anche possibile guardare un video dimostrativo relativo a questa esercitazione:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:


- Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
- Un computer con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento:** 30 minuti

##Contenuto dell'esercitazione:

* [Creare un account di archiviazione di Azure](#storage)
* [Effettuare il provisioning di un cluster HDInsight](#provision)
* [Eseguire gli esempi dal portale](#sample)
* [Eseguire un processo Hive](#hivequery)
* [Passaggi successivi](#nextsteps)

##<a name="storage"></a>Creare un account di archiviazione di Azure

HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Azure Storage - Blob*. WASB è l'implementazione Microsoft di HDFS nell'archiviazione BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

Quando si esegue il provisioning di un cluster HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è designato come file system predefinito, come in HDFS. Per impostazione predefinita, il provisioning del cluster HDInsight è eseguito nello stesso data center che include l'account di archiviazione specificato.

Oltre a questo account di archiviazione, è possibile aggiungere altri account di archiviazione durante la configurazione personalizzata di un cluster HDInsight. L'account di archiviazione aggiuntivo può appartenere alla stessa sottoscrizione di Azure o a sottoscrizioni di Azure diverse. Per le istruzioni, vedere [Effettuare il provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision]. 

Per semplificare questa esercitazione, verranno usati solo il contenitore BLOB predefinito e l'account di archiviazione predefinito. In pratica, i file di dati sono in genere archiviati in un account di archiviazione designato.

**Per creare un account di archiviazione di Azure**

1. Accedere al [portale di gestione di Azure][azure-management-portal].
2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **ARCHIVIAZIONE** e infine **CREAZIONE RAPIDA**.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. Immettere i valori desiderati per **URL**, **PERCORSO** e **REPLICA**, quindi fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.

	>[AZURE.NOTE]  L'opzione Creazione rapida per il provisioning di un cluster HDInsight, come quello usato in questa esercitazione, non richiede un percorso durante il provisioning del cluster. Il cluster è invece posizionato per impostazione predefinita nello stesso data center usato per l'account di archiviazione. Assicurarsi quindi di creare l'account di archiviazione nei percorsi supportati per il cluster, ovvero nei percorsi seguenti:  **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali**.

4. Attendere che il valore dello **STATO** per il nuovo account di archiviazione venga modificato in **Online**.
5. Selezionare il nuovo account di archiviazione dall'elenco, quindi fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
7. Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi.  Sarà necessario usarli più avanti nell'esercitazione.


Per altre informazioni, vedere [Come creare un account di archiviazione][azure-create-storageaccount] e [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Effettuare il provisioning di un cluster HDInsight

Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. In questa sezione si esegue il provisioning di un cluster HDInsight versione 3.1, basato su Hadoop versione 2.4. È anche possibile creare cluster Hadoop per altre versioni usando il portale di Azure, i cmdlet di HDInsight PowerShell oppure usando .NET SDK per HDInsight. Per le istruzioni, vedere [Effettuare il provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision]. Per informazioni sulle diverse versioni di HDInsight e i relativi contratti di servizio, vedere la pagina relativa al [controllo delle versioni del componente HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Per eseguire il provisioning di un cluster HDInsight** 

1. Accedere al [portale di gestione di Azure][azure-management-portal]. 

2. Fare clic su **HDInsight** a sinistra per elencare lo stato dei cluster disponibili nell'account. Nella schermata seguente non è disponibile alcun cluster HDInsight.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. Fare clic su **NUOVO** nell'angolo inferiore sinistro, quindi su **Servizi dati**, **HDInsight** e infine su **Hadoop**.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. Immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Nome del cluster</td><td>Nome del cluster</td></tr>
	<tr><td>Dimensione del cluster</td><td>Numero di nodi di dati che si vuole distribuire. Il valore predefinito è 4. Tuttavia, nell'elenco a discesa è disponibile anche l'opzione relativa ai nodi di dati 1 o 2. Per specificare un numero qualsiasi di nodi del cluster, usare l'opzione <strong>Creazione personalizzata</strong> . Sono disponibili i dettagli sui prezzi relativi alle tariffe di fatturazione per le diverse dimensioni di cluster. Fare clic sul simbolo <strong>?</strong> immediatamente sopra la casella a discesa, quindi selezionare il collegamento disponibile nel popup.</td></tr>
	<tr><td>Password</td><td>Password dell'account <i>admin</i> . Il nome "admin" per l'utente cluster viene specificato quando non si usa l'opzione <strong>Creazione personalizzata</strong> . Si noti che NON si tratta dell'account Administrator di Windows per le VM in cui è eseguito il provisioning dei cluster. È possibile modificare il nome dell'account tramite la procedura guidata <strong>Creazione personalizzata</strong> .</td></tr>
	<tr><td>Account di archiviazione</td><td>Selezionare l'account di archiviazione creato dalla casella a discesa. <br/>

	Dopo la selezione, non sarà possibile modificare l'account di archiviazione. In caso di rimozione dell'account di archiviazione, il cluster non sarà più disponibile per l'uso.

	Il cluster HDInsight è posizionato nello stesso data center dell'account di archiviazione. 
	</td></tr>
	</table>

	Annotare il nome del cluster.  Sarà necessario usarlo più avanti nell'esercitazione.

	
5. Fare clic su **Creazione del cluster HDInsight**. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

	>[AZURE.NOTE] La procedura precedente permette di creare cluster con HDInsight versione 3.1. Per creare altre versioni di cluster, usare il metodo di creazione personalizzata dal portale di gestione oppure Azure PowerShell. Per informazioni sulle differenze tra ogni versione di cluster, vedere [Novità delle versioni cluster incluse in HDInsight][hdinsight-versions]. Per informazioni sull'uso dell'opzione **CREAZIONE PERSONALIZZATA**, vedere [Effettuare il provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision].


##<a name="sample"></a>Eseguire gli esempi dal portale

Un cluster HDInsight il cui provisioning è stato eseguito correttamente fornisce una Console query per l'esecuzione di esempi direttamente dal portale. Gli esempi possono essere usati per apprendere come lavorare con HDInsight seguendo le procedure dettagliare di alcuni scenari di base. Gli esempi forniscono tutti i componenti necessari, come i dati per l'analisi e le query da eseguire sui dati. 

**Per eseguire l'esempio**, dal portale di gestione di Azure fare clic sul nome del cluster sul quale eseguire l'esempio, quindi fare clic su **Console query** nella parte inferiore della pagina. Nella pagina Web che viene aperta, fare clic sulla scheda **Guida introduttiva**, quindi, sotto la categoria **Esempi**, fare clic sull'esempio da eseguire. Seguire le istruzioni nella pagina Web per completare l'esempio. Per saperne di più sulla funzione di ognuno degli esempi, fare clic sui collegamenti sottostanti.

Esempio | Funzione
------ | ---------------
[Analisi di dati del sensore][hdinsight-sensor-data-sample] | Uso di HDInsight per l'elaborazione dei dati cronologici prodotti dai sistemi HVAC (Heating, Ventilating and Air Conditioning, ossia riscaldamento, ventilazione e condizionamento dell'aria) per identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata.
[Analisi dei log dei siti Web][hdinsight-weblogs-sample] | Uso di HDInsight per analizzare i log dei siti Web e ottenere informazioni dettagliate sulla frequenza delle visite di siti Web esterni nell'arco di un giorno nonché per visualizzare un riepilogo degli errori relativi ai siti Web incontrati dagli utenti


##<a name="hivequery"></a>Eseguire una query HIVE dal portale
Dopo il provisioning di un cluster HDInsight, verrà eseguito un processo Hive per eseguire query in una tabella Hive di esempio *hivesampletable* inclusa nei cluster HDInsight. La tabella contiene dati relativi a produttori di dispositivi mobili, piattaforme e modelli. Verranno eseguite query in questa tabella per recuperare i dati per i dispositivi mobili di un produttore specifico.

> [AZURE.NOTE] HDInsight Tools per Visual Studio è disponibile in Azure SDK per .NET versione 2.5 o versione successiva.  Usando gli strumenti da Visual Studio, sarà possibile connettersi al cluster HDInsight, creare tabelle Hive ed eseguire query Hive.  Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio][1].

**Per eseguire un processo Hive dal dashboard del cluster**

1. Accedere al [portale di gestione di Azure][azure-management-portal]. 
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3. Fare clic sul nome del cluster in cui si vuole eseguire il processo Hive, quindi fare clic su **QUERY CONSOLE** nella parte inferiore della pagina. 
4. Verrà aperta una pagina Web in una scheda diversa del browser. Immettere l'account utente e la password per Hadoop.  Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il provisioning del cluster.  L'aspetto del dashboard è simile al seguente:

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	Nella parte superiore sono disponibili diverse schede.  La scheda predefinita è **Editor Hive** e le altre schede sono **Cronologia processo** e **Browser file**.  Usando il dashboard, è possibile inviare query Hive, controllare i log dei processi Hadoop ed esplorare i file WASB.

	> [AZURE.NOTE] Si noti che l'URL della pagina Web è *&lt;NomeCluster&gt;.azurehdinsight.net*. Invece di aprire il dashboard dal portale di gestione, è possibile aprirlo da un Web browser usando l'URL.

6. Nella scheda **Editor Hive** in **Nome query** immettere **HTC20**.  Il nome della query corrisponde al nome del processo.

7. Nel riquadro della query immettere la query seguente: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. Fare clic su **Invia** Per ottenere i risultati sono necessari alcuni minuti. La schermata viene aggiornata ogni 30 secondi. È anche possibile fare clic su **Aggiorna** per aggiornare la schermata.

    Al termine, la schermata avrà un aspetto analogo al seguente:

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. Fare clic sul nome della query disponibile nella schermata per visualizzate l'output. Annotare il valore per **Ora inizio processo (UTC)**. Sarà necessario più avanti. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    Questa pagina mostra anche i valori per **Output processo** e **Registro processo**. Sarà anche possibile scaricare il file di output (\_stdout) e il file di log \(_stderr).


	> [AZURE.NOTE] La tabella **Sessione processo** nella scheda **Editor Hive** elenca i processi completi o in esecuzione fintanto che la scheda rimane visualizzata. Se si esce dalla pagina, nella tabella non saranno elencati processi. La scheda **Cronologia processo** include un elenco di tutti i processi, completati o in esecuzione.
 

**Per passare al file di output**

1. Nel dashboard del cluster fare clic su **Browser file** nella parte superiore della pagina. 
2. Fare clic sul nome dell'account di archiviazione, quindi sul nome del contenitore (che corrisponde al nome del cluster) e infine su **utente**.
3. Fare clic su **admin** e quindi sul GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID. Sarà necessario nella sezione successiva.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Connettersi agli strumenti di business intelligence Microsoft 

È possibile usare il componente aggiuntivo Power Query per Microsoft Excel per importare l'output del processo da HDInsight in Excel, dove si possono usare gli strumenti di business intelligence (BI) Microsoft per un'analisi più approfondita dei risultati. 

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2010 o 2013. 

**Per scaricare Microsoft Power Query per Excel**

- Scaricare Microsoft Power Query per Microsoft Excel dall'[Area download Microsoft](http://www.microsoft.com/it-it/download/details.aspx?id=39379) e installarlo.

**Per importare dati di HDInsight**

1. Aprire Excel e creare una nuova cartella di lavoro vuota.
3. Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Si tratta dell'account di archiviazione creato in precedenza nell'esercitazione.
4. Immettere la **Chiave account** per l'account di archiviazione BLOB di Azure, quindi fare clic su **Salva**. 
5. Nel riquadro Strumento di navigazione a destra fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster. 

6. Individuare **stdout** nella colonna **Nome**. Assicurarsi che il GUID nella colonna **Percorso cartella** corrispondente sia uguale al GUID annotato in precedenza. Una corretta corrispondenza indica che i dati di output corrispondono al processo inviato. Fare clic su **Binary** a sinistra di **stdout**.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. Fare clic su **Carica e chiudi** nell'angolo superiore sinistro per importare l'output del processo Hive in Excel.


##<a name="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione all'uso di HDInsight Tools per Visual Studio][1]
- [Introduzione all'uso di HDInsight Emulator][hdinsight-emulator]
- [Usare ll'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]
- [Caricare i dati in HDInsight][hdinsight-upload-data]
- [Usare MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Usare Oozie con HDInsight][hdinsight-use-oozie]
- [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=42-->
