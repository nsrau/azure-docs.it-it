<properties 
	pageTitle="Usare i notebook di Zeppelin con cluster Spark in HDInsight Linux | Azure" 
	description="Istruzioni dettagliate su come usare i notebook di Zeppelin con cluster Spark in HDInsight per Linux." 
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
	ms.date="06/06/2016" 
	ms.author="nitinme"/>


# Utilizzare i notebook di Zeppelin con cluster Apache Spark in HDInsight Linux

Informazioni su come installare i notebook di Zeppelin in cluster Apache Spark e su come utilizzare i notebook di Zeppelin per eseguire i processi Spark.

> [AZURE.IMPORTANT] I notebook di Zeppelin per cluster HDInsight Spark vengono proposti per illustrare l'uso di Zeppelin in un ambiente Azure HDInsight Spark. Se si vogliono usare i notebook con HDInsight Spark, è consigliabile usare i notebook di Jupyter. Anche i notebook di Jupyter offrono varie opzioni kernel, ad esempio Scala, e le funzionalità continuano a migliorare. Per istruzioni su come usare i notebook di Jupyter con HDInsight Spark, vedere [Eseguire query Spark SQL con un notebook di Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Prerequisiti:**

* Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un client SSH. Per distribuzioni Linux e Unix o Macintosh OS X, il comando `ssh` viene offerto con il sistema operativo. Per Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Se si vuole usare un client SSH diverso da `ssh` o PuTTY, consultare la documentazione per il client su come stabilire un tunnel SSH.

* Un browser Web che può essere configurato per usare un proxy SOCKS

* __(facoltativo)__: un plug-in, ad esempio [FoxyProxy](http://getfoxyproxy.org/,) che possa applicare delle regole che instradano solo richieste specifiche attraverso il tunnel.

	> [AZURE.WARNING] Senza un plug-in come FoxyProxy, tutte le richieste effettuate tramite il browser possono essere instradate attraverso il tunnel. Ciò può comportare un caricamento più lento delle pagine web nel browser.

## Installare Zeppelin in un cluster Spark

È possibile installare Zeppelin in un cluster Spark tramite l'azione script. L'azione script usa script personalizzati per installare nel cluster i componenti che non sono disponibili per impostazione predefinita. È possibile usare lo script personalizzato per installare Zeppelin dal portale di Azure tramite HDInsight .NET SDK o Azure PowerShell. È possibile utilizzare lo script per installare Zeppelin sia nell’ambito della creazione del cluster che quando il cluster è in esecuzione. I collegamenti nelle sezioni seguenti forniscono le istruzioni.

### Uso del portale di Azure

Per istruzioni su come usare HDInsight .NET SDK per eseguire azioni di script per installare Zeppelin, leggere l'articolo [Personalizzare cluster HDInsight mediante Azione script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). È necessario apportare alcune modifiche alle istruzioni riportate in questo articolo.

* È necessario usare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster Spark su HDInsight è disponibile ai collegamenti seguenti.
	* Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* È necessario eseguire l'azione script solo sul nodo head.

* Per lo script non sono necessari parametri.

### Uso di HDInsight .NET SDK

Per istruzioni su come usare HDInsight .NET SDK per eseguire azioni di script per installare Zeppelin, leggere l'articolo [Personalizzare cluster HDInsight mediante Azione script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). È necessario apportare alcune modifiche alle istruzioni riportate in questo articolo.

* È necessario usare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster Spark su HDInsight è disponibile ai collegamenti seguenti.
	* Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* Per lo script non sono necessari parametri.

* Impostare il tipo di cluster che si sta creando per Spark.

### Uso di Azure PowerShell

Usare il frammento di codice PowerShell seguente per creare un cluster Spark in HDInsight Linux con Zeppelin installato. A seconda della versione del cluster Spark in uso, è necessario aggiornare il frammento di codice di PowerShell seguente per includere il collegamento allo script personalizzato corrispondente.

* Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Impostare il tunneling SSH per accedere a un notebook di Zeppelin

Si userà il tunnel SSH per accedere ai notebook di Zeppelin in esecuzione sul cluster Spark in HDInsight Linux. La procedura seguente illustra come creare un tunnel SSH con la riga di comando ssh (Linux) e PuTTY (Windows).

### Creare un tunnel con il comando SSH (Linux)

Usare il comando seguente per creare un tunnel SSH usando il comando `ssh`. Sostituire __NOME UTENTE__ con un utente SSH per il cluster HDInsight e sostituire __NOME CLUSTER__ con il nome del cluster HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Verrà creata una connessione che instrada il traffico alla porta locale 9876 al cluster su SSH. Le opzioni sono:

* **D 9876**: la porta locale che instraderà il traffico attraverso il tunnel.

* **C**: comprime tutti i dati, in quanto il traffico Web è costituito prevalentemente da testo.

* **2**: forza SSH a tentare solo il protocollo versione 2.

* **q**: modalità non interattiva.

* **T**: disabilita l'allocazione pseudo-tty perché si tratta semplicemente dell'inoltro di una porta.

* **n**: impedisce la lettura di STDIN perché si tratta semplicemente dell'inoltro di una porta.

* **N**: non esegue un comando remoto perché si tratta semplicemente dell'inoltro di una porta.

* **f**: effettua l'esecuzione in background.

Se il cluster è stato configurato con una chiave SSH, potrebbe essere necessario usare il parametro `-i` e specificare il percorso della chiave privata SSH.

Al termine del comando, il traffico inviato alla porta 9876 nel computer locale verrà instradato su SSL (Secure Sockets Layer) al nodo head del cluster e sembrerà provenire da tale nodo.

### Creare un tunnel usando PuTTY (Windows)

Usare la procedura seguente per creare un tunnel SSH usando PuTTY.

1. Aprire PuTTY e immettere le informazioni di connessione. Se non si ha familiarità con PuTTY, vedere l'articolo relativo all'[uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.

2. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine selezionare **Tunnels**.

3. Fornire le seguenti informazioni nel modulo **Options controlling SSH port forwarding**:

	* **Source port**: la porta del client che si desidera inoltrare. Ad esempio, **9876**.

	* **Destination**: l'indirizzo SSH del cluster HDInsight basato su Linux. Ad esempio, **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: consente il routing tramite proxy SOCKS dinamico.

	![image of tunneling options](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Fare infine clic su **Add** per aggiungere le impostazioni, quindi su **Open** per aprire una connessione SSH.

5. Quando richiesto, accedere al server. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

### Usare il tunnel dal browser

> [AZURE.NOTE] I passaggi descritti in questa sezione usano il browser FireFox, poiché è disponibile gratuitamente per i sistemi Linux, Unix, Macintosh OS X e Windows. Anche altri browser moderni come Google Chrome, Edge Microsoft o Apple Safari dovrebbero funzionare; tuttavia, il plug-in FoxyProxy usato in alcuni passaggi potrebbe non essere disponibile per tutti i browser.

1. Configurare il browser per usare **localhost:9876** come proxy **SOCKS v5**. Ecco visualizzate le impostazioni di Firefox. Se si usa una porta diversa da quella 9876, cambiare la porta con quella usata:

	![image of Firefox settings](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] Se si seleziona **Remote DNS**, le richieste DNS (Domain Name System) verranno risolte usando il cluster HDInsight. Se questa opzione è deselezionata, il DNS verrà risolto localmente.

2. Verificare che il traffico sia instradato attraverso il tunnel visitando un sito come [http://www.whatismyip.com/](http://www.whatismyip.com/) con le impostazioni proxy abilitate e disabilitate in Firefox. Mentre le impostazioni sono abilitate, l'indirizzo IP sarà per un computer nel data center di Microsoft Azure.

### Estensioni del browser

Quando si configura il browser per l'uso del tunnel, in genere non è opportuno instradare tutto il traffico attraverso il tunnel. Le estensioni del browser, ad esempio [FoxyProxy](http://getfoxyproxy.org/), supportano i criteri di ricerca per le richieste URL (solo FoxyProxy Standard o Plus), in modo che vengano inviate tramite tunnel solo le richieste relative a URL specifici.

Se è stato installato FoxyProxy Standard, seguire questa procedura per configurarlo in modo che inoltri tramite tunnel solo il traffico per HDInsight.

1. Aprire l'estensione FoxyProxy nel browser. Ad esempio, in Firefox fare clic sull'icona FoxyProxy accanto al campo dell'indirizzo.

	![foxyproxy icon](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Selezionare **Add New Proxy**, fare clic sulla scheda **General** e immettere **HDInsightProxy** come nome proxy.

	![foxyproxy general](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Fare clic sulla scheda **Proxy Details** e popolare i campi seguenti:

	* **Host or IP Address**: localhost perché viene usato un tunnel SSH nel computer locale.

	* **Port**: la porta usata per il tunnel SSH.

	* **SOCKS proxy**: selezionare questa opzione per consentire al browser di usare il tunnel come proxy.

	* **SOCKS v5**: selezionare questa opzione per impostare la versione necessaria per il proxy.

	![foxyproxy proxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Fare clic sulla scheda **URL Patterns** e selezionare **Add New Pattern**. Usare i campi seguenti per definire il criterio, quindi fare clic su **OK**:

	* **Pattern Name** - **zeppelinnotebook**: si tratta di un nome descrittivo per il modello.

	* **URL pattern** - ***hn0***: definisce un modello che corrisponde al nome di dominio interno completo dell'endpoint in cui sono ospitati i notebook di Zeppelin. Poiché i notebook di Zeppelin sono disponibili solo sul nodo head 0 del cluster e l'endpoint è in genere `http://hn0-<string>.internal.cloudapp.net`, l'uso del modello **hn0** assicura che la richiesta venga reindirizzata all'endpoint Zeppelin.

		![foxyproxy pattern](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Fare clic su **OK** per aggiungere il proxy e chiudere la finestra **Proxy Settings**.

5. Nella parte superiore della finestra di dialogo FoxyProxy impostare **Select Mode** su **Use proxies based on their pre-defined patterns and priorities**, quindi fare clic su **Close**.

	![foxyproxy select mode](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Dopo aver eseguito questa procedura, solo le richieste per gli URL contenenti la stringa __internal.cloudapp.net__ verranno instradate attraverso il tunnel SSL.

## Accedere al notebook di Zeppelin

Dopo aver configurato il tunneling SSH, è possibile usare la procedura seguente per accedere a notebook di Zeppelin nel cluster Spark seguendo i passaggi elencati.

1. Dal Web browser, aprire l'endpoint seguente:

		http://hn0-myspar:9995

	* **hn0** indica il nodo head 0.
	* **myspar** sono le prime sei lettere del nome del cluster Spark.
	* **9995** è la porta da cui il notebook di Zeppelin è accessibile.

2. Creare un nuovo notebook. Dal riquadro intestazione fare clic su **Notebook** quindi fare clic su **Creare una nuova nota**.

	![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Creare un nuovo notebook Zeppelin")

	Nella stessa pagina, sotto l'intestazione **Notebook**, verrà visualizzato un nuovo notebook con il nome che inizia con **Nota XXXXXXXXX**. Fare clic su nuovo notebook.

3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Verificare anche che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

	![Stato di notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Stato di notebook Zeppelin")

4. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato in **\\HdiSamples\\SensorSampleData\\hvac**.

	Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento di codice riportato di seguito.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Premere **MAIUSC+INVIO** oppure fare clic sul pulsante **Riproduci** in modo che il paragrafo esegua il frammento di codice. Lo stato nell'angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output viene visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

	![Crea una tabella temporanea dai dati non elaborati](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Crea una tabella temporanea dai dati non elaborati")

	È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo superiore destro fare clic sull'icona **Impostazioni** e quindi su **Mostra titolo**.

5. È ora possibile eseguire istruzioni SQL Spark su tabella **hvac**. Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID dell'edificio e la differenza tra le temperature effettive e quelle di destinazione per ogni edificio in una determinata data. Premere **MAIUSC + INVIO**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete SQL Spark. È possibile esaminare gli interpreti definiti dalla scheda **Interprete** nell'intestazione del notebook.

	Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Eseguire un'istruzione SQL Spark usando il notebook")

	 Scegliere le opzioni di visualizzazione (evidenziate nel rettangolo) per passare tra diverse rappresentazioni per lo stesso output. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave e la media di **temp\_diff** come valore.

	
6. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. Il seguente frammento di codice illustra come definire una variabile**Temp**nella query con i valori possibili che si vuole eseguire. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Incollare questo frammento di codice in un nuovo paragrafo e premere **MAIUSC+INVIO**. Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Eseguire un'istruzione SQL Spark usando il notebook")

	Per le query successive, è possibile selezionare un nuovo valore dall'elenco a tendina e quindi eseguire nuovamente la query. Fare clic su**impostazioni**per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave, la media di **temp\_diff** come valore e **targettemp** come gruppo.

7. Riavviare l'interprete Spark SQL per uscire dall'applicazione. Scegliere la scheda **Interprete** nella parte superiore e per l'interprete Spark fare clic su **Riavvia**.

	![Riavviare l'interprete Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Riavviare l'interprete Zeppelin")


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0608_2016-->