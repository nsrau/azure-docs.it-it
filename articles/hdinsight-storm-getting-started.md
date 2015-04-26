<properties 
	pageTitle="Introduzione all'uso di Apache Storm con Microsoft Azure HDInsight (Hadoop)" 
	description="Informazioni sull'uso di  Apache Storm per elaborare i dati in tempo reale con HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# Introduzione a Storm con HDInsight (Hadoop)

Apache Storm è un sistema di calcolo in tempo reale scalabile, a tolleranza di errore e distribuito per l'elaborazione di flussi di dati. Con Azure HDInsight è possibile creare un cluster Hadoop basato sul cloud che esegue l'analisi dei dati in tempo reale con Storm.

## In questa esercitazione si apprenderà come:

* [Come effettuare il provisioning di un cluster Storm in HDInsight](#provision)

* [Connettersi al cluster](#connect)

* [Eseguire una topologia Storm](#run)

* [Monitorare una topologia Storm](#monitor)

* [Arrestare una topologia Storm](#stop)

* [Passaggi successivi](#next)

## Operazioni preliminari

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure

* Microsoft Azure PowerShell

* Se non si ha familiarità con Apache Storm, leggere la [panoramica su HDInsight Storm](hdinsight-storm-overview.md) prima di iniziare.

## <a id="provision"></a>Eseguire il provisioning di un cluster Storm nel portale di Azure

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Accedere al [portale di gestione di Azure][azureportal].

2. Fare clic su **HDInsight** a sinistra e quindi su **+NUOVO** nell'angolo inferiore sinistro della pagina.

3. Fare clic sull'icona di HDInsight nella seconda colonna e quindi selezionare **Personalizzato**.

4. Nella pagina **Dettagli cluster** immettere il nome del nuovo cluster e selezionare **Storm** in **Tipo di cluster**. Fare clic sulla freccia per continuare.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. In **Nodi dati** immettere il numero di nodi da usare per il cluster e in **Area/Rete virtuale** specificare l'area geografica o la rete virtuale in cui verrà creato il cluster. Fare clic sulla freccia per continuare.

	> [AZURE.NOTE] Per ridurre al minimo il costo del cluster usato in questo articolo, ridurre il valore di **Dimensione del cluster** a 1 ed eliminare il cluster al termine della procedura.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Immettere **Nome utente** e **Password** dell'amministratore, quindi fare clic sulla freccia per continuare.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. In **Account di archiviazione** selezionare **Crea nuova archiviazione** o selezionare un account di archiviazione esistente. Selezionare o immettere il **Nome account** e il **Contenitore predefinito** da usare.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. Nella pagina **Azioni script** fare clic su **aggiungi azione script** per specificare i dettagli relativi allo script da eseguire per personalizzare un cluster, ad esempio quello da creare. È ad esempio possibile usare Azione script per personalizzare un cluster da installare. <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante Azione script](hdinsight-hadoop-customize-cluster.md). 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script.</td></tr>
	</table>

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

## Uso di Storm in HDInsight

Per la versione di anteprima di Storm in HDInsight, occorre connettersi al cluster tramite Desktop remoto per poter usare Storm. La procedura seguente consente di connettersi al cluster HDInsight e di usare il comando Storm.

###<a id="connect"></a>Connettersi al cluster

1. Abilitare la connessione al cluster HDInsight tramite Desktop remoto usando il [portale di gestione di Azure][azureportal]. Nel portale selezionare il cluster HDInsight, quindi selezionare __Abilita modalità remota__ nella parte inferiore della pagina __Configurazione__.

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Quando richiesto, immettere il nome utente e la password da usare per le sessioni remote. È anche necessario selezionare una data di scadenza per l'accesso remoto.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. Dopo aver abilitato l'accesso remoto, selezionare __Connetti__ per iniziare la connessione. Verrà scaricato un file con estensione __rdp__ da usare per avviare la sessione Desktop remoto.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] È possibile che vangano visualizzati molti messaggi per richiedere i certificati attendibili durante la connessione al computer remoto.

3. Dopo avere stabilito la connessione, usare l'icona della __riga di comando di Hadoop__ sul desktop per aprire la riga di comando di Hadoop.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Nella riga di comando di Hadoop usare il comando seguente per passare alla directory contenente il comando Storm.

	cd %storm_home%\bin

5. Per un elenco dei comandi disponibili, digitare `storm` senza alcun parametro.

Il cluster HDInsight include varie topologie di Storm di esempio. Nella procedura seguente viene usato l'esempio **WordcountTopology**. Per altre informazioni sugli esempi forniti con Storm in HDInsight, vedere [Passaggi successivi)]#next).

### <a id="run"></a>Per eseguire una topologia Storm

Per eseguire **WordCountTopology**, usare i comandi seguenti.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

In questo modo si indica a Storm che si vuole eseguire la topologia **wordcount** della classe **storm.starter.WordCountTopology** inclusa nel file **storm-starter-&lt;version>-jar-with-dependencies.jar**. Questo file si trova nella cartella contrib nella directory %storm_home%, insieme ad altri esempi di Storm.

> [AZURE.NOTE] La versione del file JAR contenente gli esempi potrebbe cambiare periodicamente. Specificare la versione del file fornita con il cluster durante l'esecuzione del comando.

Si noti che non viene restituito nulla quando si immette il comando. **Dopo l'avvio, una topologia Storm viene eseguita fino a quando non viene arrestata.** WordCountTopology genererà frasi casuali e conterà quante volte ogni parola è stata incontrata, fino a quando non verrà arrestata.

### <a id="monitor"></a>Per monitorare lo stato di una topologia Storm

L'esempio WordCountTopology non scrive l'output in una directory, ma è possibile usare le pagine Web dell'interfaccia utente di Storm per visualizzare lo stato della topologia e l'output.

1. Tramite Desktop remoto connettersi al cluster HDInsight.

2. Aprire il collegamento **Storm UI** dal desktop. Viene visualizzata la pagina Web dell'interfaccia utente di Storm.  In **Topology summary** selezionare **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Vengono visualizzate le statistiche della topologia, inclusi i componenti che la costituiscono, gli **Spout** e i **Bolt**.

3. Selezionare il collegamento **spout** nella pagina, quindi selezionare il numero di porta in **Port** per una delle voci dell'elenco **Executors (All time)** con un valore superiore a 0 nelle colonne **Emitted** e **Transferred**.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] A seconda del numero di nodi del cluster e della topologia che si sta eseguendo, potrebbero essere necessari diversi minuti prima che la topologia inizi l'elaborazione. Aggiornare la pagina periodicamente fino a quando i valori di **Emitted** e **Transferred** non iniziano ad aumentare.

4. Verrà visualizzata una pagina con informazioni simili alle seguenti.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	Da questo frammento di codice è possibile vedere che lo Spout ha generato la frase 'snow white and the seven dwarfs' suddivisa in singole parole. Viene anche tenuto il conteggio delle volte in cui ogni parola è stata elaborata dalla topologia dal momento dell'avvio. La parola 'dwarfs' ad esempio è stata emessa 8360 volte dallo Spout al momento della visualizzazione dell'output.

### <a id="stop"></a>Per arrestare una topologia Storm

**WordCountTopology** continuerà ad essere eseguita fino a quando non viene arrestata. Per arrestarla, usare il comando seguente:

	storm kill wordcount

Se immediatamente dopo questo comando viene visualizzata la pagina Web dell'interfaccia utente di Storm, si noterà che lo stato di **wordcount** in **Topology summary** è passato a KILLED. Dopo alcuni secondi, la topologia non sarà più elencata nella sezione **Topology summary**.

## <a id="next"></a>Passaggi successivi

* **File di esempio**: nella directory **%storm_home%\contrib** del cluster Storm sono disponibili diversi esempi. Ogni esempio deve contenere quanto segue:

	* Il codice sorgente, ad esempio storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* I documenti Java, ad esempio storm-starter-0.9.1.2.1.5.0-2057-javadocs.jar

	* L'esempio, ad esempio storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Usare il comando 'jar' per estrarre il codice sorgente o i documenti Java, ad esempio 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] I documenti Java sono costituiti da pagine Web. Dopo l'estrazione dei file, usare un browser per visualizzare il file **index.html**.

* [Analisi dei dati dei sensori con Storm e HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm in HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=42-->
