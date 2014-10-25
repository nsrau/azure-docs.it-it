<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />




# Introduzione all'utilizzo di Hadoop 2.4 in HDInsight (anteprima)

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div>


HDInsight rende [Apache Hadoop][Apache Hadoop] disponibile come servizio nel cloud, rendendo disponibile il framework software MapReduce in un ambiente di Azure più semplice, scalabile ed efficiente a livello di costi. HDInsight offre inoltre un approccio economicamente vantaggioso alla gestione e all'archiviazione di dati tramite l'archivio BLOB di Azure.

In questa esercitazione verrà eseguito il provisioning di un cluster Hadoop in HDInsight tramite il portale di gestione di Azure, verrà inviato un processo Hive per eseguire una query su una tabella Hive di esempio tramite il dashboard del cluster di HDInsight e quindi i dati di output del processo Hive verranno importati in Excel per essere esaminati.

> [WACOM.NOTE] In questa esercitazione viene illustrato l'utilizzo di cluster Hadoop 2.4 in HDInsight (anteprima). Per informazioni su altre versioni supportate, fare clic sul selettore nella parte superiore della pagina. Per informazioni sulla versione, vedere [Novità delle versioni cluster incluse in HDInsight][Novità delle versioni cluster incluse in HDInsight]

Demo in diretta di questo articolo:

<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center></center>

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft ha rilasciato anche HDInsight Emulator per Azure, noto in precedenza come *Microsoft HDInsight Developer Preview*. Questo prodotto è stato progettato per scenari relativi allo sviluppo e supporta pertanto solo distribuzioni a nodo singolo. Per informazioni sull'utilizzo di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator][Introduzione a HDInsight Emulator].

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:


- Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].
- Un computer con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.
- Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight][Note sulla versione di HDInsight].

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

* [Provisioning di un cluster HDInsight][Provisioning di un cluster HDInsight]
* [Eseguire un processo Hive][Eseguire un processo Hive]
* [Connettersi agli strumenti di business intelligence Microsoft][Connettersi agli strumenti di business intelligence Microsoft]
* [Passaggi successivi][Passaggi successivi]


## <a name="provision"></a>Provisioning di un cluster HDInsight

HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

Durante il provisioning di un cluster HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account sono designati come file system predefinito, come in HDFS. L'account di archiviazione deve trovarsi nello stesso data center che include le risorse di calcolo di HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:

- Asia sudorientale
- Europa settentrionale
- Europa occidentale
- Stati Uniti orientali
- Stati Uniti occidentali

È possibile aggiungere all'account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

Per semplificare questa esercitazione vengono usati solo il contenitore BLOB e l'account di archiviazione predefiniti e tutti i file vengono archiviati nel contenitore del file system predefinito, disponibile in */tutorials/getstarted/*. In pratica, i file di dati sono in genere archiviati in un account di archiviazione designato.


**Per creare un account di archiviazione di Azure**

1. Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2. Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3. Immettere i valori desiderati per **URL**, **LOCATION** e **REPLICATION**, quindi fare clic su **CREATE STORAGE ACCOUNT**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.
4. Attendere che il valore di **STATUS** per il nuovo account di archiviazione venga modificato in **Online**.
5. Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
6. Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della pagina.
7. Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi. Sarà necessario utilizzarli più avanti nell'esercitazione.


Per altre informazioni, vedere
[Come creare un account di archiviazione][Come creare un account di archiviazione] e [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].




















**Per eseguire il provisioning di un cluster HDInsight**

1. Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2. Fare clic su **HDINSIGHT** a sinistra per elencare lo stato dei cluster disponibili nell'account. Nella schermata seguente non è disponibile alcun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3. Custom Create **NEW** nell'angolo inferiore sinistro, quindi su **DATA SERVICES**, **HDINSIGHT** e infine su **CUSTOM CREATE**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4. Nella scheda Cluster Details immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td><strong>Cluster Name</strong></td><td>Nome del cluster.</td></tr>
	<tr><td><strong>Data Nodes</strong></td><td>Numero di nodi di dati che si desidera distribuire. Ai fini di test, creare un cluster a singolo nodo.<br/>                                                                                                                                                                                                                                               Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
	<tr><td><strong>HDInsight Version</strong></td><td>Scegliere <strong>3.1</strong> per creare un cluster Hadoop 2,4 su HDInsight.</td></tr>
	<tr><td><strong>Region</strong></td><td>Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui. 
	</td></tr>
	</table>

5. Fare clic sulla freccia destra nell'angolo inferiore destro per configurare l'utente del cluster.
6. Nella scheda Configure Cluster immettere **User Name** e **Password** per l'account utente relativo al cluster HDInsight. Oltre a tale account, è possibile creare un account utente RDP dopo il provisioning, in modo da potere accedere al cluster tramite Desktop remoto. Per istruzioni, vedere [Amministrazione di HDInsight tramite il portale di gestione][hdinsight-admin-portal]
7. Fare clic sulla freccia destra nell'angolo inferiore destro per configurare l'account di archiviazione.
8. Nella scheda Storage Account immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>STORAGE ACCOUNT</td><td>Selezionare <strong>Use Existing Storage</strong>. È inoltre possibile usare il portale di gestione per creare un nuovo account di archiviazione, nel caso in cui non ne sia già stato creato uno.</td></tr>
	<tr><td>ACCOUNT NAME</td><td>Specificare l'account di archiviazione creato nell'ultima procedura di questa esercitazione. Si noti che nell'elenco vengono visualizzati solo gli account di archiviazione disponibili nella stessa area geografica.</td></tr>
	<tr><td>DEFAULT CONTAINER</td><td>Selezionare <strong>Create default container</strong>. Quando si seleziona questa opzione, il nome del contenitore predefinito sarà uguale al nome del cluster.</td></tr>
	<tr><td>ADDITIONAL STORATGE ACCOUNT</td><td>Selezionare <strong>0</strong>. È possibile connettere il cluster a un massimo di 7 account di archiviazione aggiuntivi.</td></tr>
	</table>

9. Fare clic sull'icona di spunta nell'angolo inferiore destro per creare il cluster. Al termine del processo di provisioning, nella colonna relativa allo stato verrà visualizzato il valore **Running**.














## <a name="sample"></a>Eseguire un processo Hive

Il provisioning del cluster HDInsight è stato completato. Nel passaggio successivo verrà creato un processo Hive per eseguire una query su una tabella Hive di esempio disponibile nei cluster HDInsight. Il nome della tabella è *hivesampletable*.

**Per aprire il dashboard cluster di HDInsight**

1. Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3. Fare clic sul nome del cluster in cui eseguire il processo Hive.
4. Fare clic su **GESTIONE DEL CLUSTER** nella parte inferiore della pagina per aprire il dashboard cluster di HDInsight. Verrà aperta una pagina Web in una scheda diversa del browser.
5. Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il processo di provisioning. L'aspetto del dashboard è simile al seguente:

    ![hdi.dashboard][hdi.dashboard]

    Nella parte superiore sono disponibili diverse schede. La scheda predefinita è *Editor Hive* e le altre schede disponibili sono Processi e File. Usando il dashboard, è possibile inviare query Hive, controllare i log dei processi Hadoop ed esplorare i file WASB.

> [wacom.note] Si noti che l'URL è *\<NomeCluster\>.azurehdinsight.net*. Invece di aprire il dashboard dal portale di gestione, è possibile aprirlo da un Web browser usando l'URL.

**Per eseguire una query Hive**

1. Nel dashboard del cluster di HDInsight fare clic su **Editor Hive** nella parte superiore della pagina.
2. In **Nome query** digitare **HTC20**. Il nome della query corrisponde al nome del processo.
3. Nel riquadro della query immettere la query seguente:

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4. Fare clic su **Submit**. Per ottenere i risultati sono necessari alcuni minuti. La schermata viene aggiornata ogni 30 secondi. È anche possibile fare clic su **Aggiorna** per aggiornare la schermata.

    Al termine, la schermata avrà un aspetto analogo al seguente:

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    Annotare il valore per **Ora inizio processo (UTC)**. Sarà necessario più avanti.

    Scorrendo verso il basso, verrà visualizzata l'opzione **Registro processo**. Output processo è stdout e Registro processo è stderr.

5. Per riaprire il file di registro in futuro, fare clic su **Processi** nella parte superiore dello schermo e quindi fare clic sul titolo del processo (nome query). Ad esempio, in questo caso sarà **HTC20**.

**Per passare al file di output**

1. Nel dashboard del cluster di HDInsight fare clic su **File** nella parte superiore della pagina.
2. Fare clic su **Templeton-Job-Status**.
3. Fare clic sul numero di GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID. Sarà necessario nella sezione successiva.
4. Il file **stdout** contiene i dati necessari nella prossima sezione. Se necessario, fare clic su **stdout** per scaricare una copia del file di dati.

## <a name="powerquery"></a>Connettersi agli strumenti di business intelligence Microsoft

È possibile usare il componente aggiuntivo Power Query per Excel per esportare l'output da HDInsight a Excel, quindi usare gli strumenti di Microsoft Business Intelligence (BI) per elaborare ulteriormente o visualizzare i risultati.

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2010 o 2013.

**Per scaricare Microsoft PowerQuery per Excel**

- Scaricare Microsoft Power Query per Excel dall'[Area download Microsoft][Area download Microsoft] e installarlo.

**Per importare dati di HDInsight**

1. Aprire Excel e creare una nuova cartella di lavoro vuota.
2. Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3. Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Si tratta dell'account di archiviazione creato in precedenza nell'esercitazione.
4. Immettere la **Chiave account** per l'account di archiviazione BLOB di Azure, quindi fare clic su **Salva**.
5. Nel riquadro Strumento di navigazione a destra fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

6. Individuare **stdout** nella colonna **Nome** (il percorso è *.../Templeton-Job-Status/<GUID>*) e quindi fare clic su **Binario** a sinistra di **stdout**. Il <guid> deve corrispondere a quello specificato nell'ultima sezione.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7. Fare clic su **Applica e chiudi** nell'angolo superiore sinistro. La query eseguirà quindi l'importazione dell'output del processo Hive in Excel.


## <a name="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione all'utilizzo di HDInsight Emulator][Introduzione a HDInsight Emulator]
- [Utilizzo dell'archiviazione BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight]
- [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell]
- [Caricare i dati in HDInsight][Caricare i dati in HDInsight]
- [Utilizzo di MapReduce con HDInsight][Utilizzo di MapReduce con HDInsight]
- [Usare Hive con HDInsight][Usare Hive con HDInsight]
- [Usare Pig con HDInsight][Usare Pig con HDInsight]
- [Usare Oozie con HDInsight][Usare Oozie con HDInsight]
- [Sviluppare programmi per la creazione di flussi Hadoop in C\# per HDInsight][Sviluppare programmi per la creazione di flussi Hadoop in C\# per HDInsight]
- [Sviluppare programmi MapReduce Java per HDInsight][Sviluppare programmi MapReduce Java per HDInsight]

  [Hadoop 2.2]: ../hdinsight-get-started "Get started using Hadoop 2.2 in HDInsight"
  [Hadoop 2.4]: ../hdinsight-get-started-31 "Get started using Hadoop 2.4 in HDInsight"
  [Hadoop 1.2]: ../hdinsight-get-started-21 "Get started using Hadoop 1.2 in HDInsight"
  [Apache Hadoop]: http://hadoop.apache.org/
  [Novità delle versioni cluster incluse in HDInsight]: ../hdinsight-component-versioning/
  [Introduzione a HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Opzioni di acquisto]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Note sulla versione di HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-release-notes/
  [Provisioning di un cluster HDInsight]: #provision
  [Eseguire un processo Hive]: #sample
  [Connettersi agli strumenti di business intelligence Microsoft]: #powerquery
  [Passaggi successivi]: #nextsteps
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Area download Microsoft]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Utilizzo di MapReduce con HDInsight]: ../hdinsight-use-mapreduce
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Sviluppare programmi per la creazione di flussi Hadoop in C\# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
