<properties
	pageTitle="Usare Hue con Hadoop nei cluster HDInsight Linux | Microsoft Azure"
	description="Informazioni su come installare e usare Hue con i cluster Hadoop in HDInsight Linux."
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
	ms.date="05/17/2016" 
	ms.author="nitinme"/>

# Installare e usare Hue nei cluster Hadoop di HDInsight

Informazioni su come installare Hue nei cluster HDInsight Linux e usare il tunneling per instradare le richieste a Hue.

## Informazioni su Hue

Hue è un set di applicazioni Web che consente di interagire con un cluster Hadoop. È possibile usare Hue per esplorare lo spazio di archiviazione associato a un cluster Hadoop (WASB nel caso di cluster HDInsight), eseguire processi Hive e script Pig e così via. I componenti seguenti sono supportati con l'installazione di Hue in un cluster Hadoop di HDInsight.

* Editor Hive Beeswax
* Pig
* Metastore Manager
* Oozie
* FileBrowser (che interagisce con il contenitore predefinito di WASB)
* Job Browser


## Installare Hue mediante azioni script

È possibile usare l'azione script seguente per installare Hue in un cluster HDInsight basato su Linux. https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    
Questa sezione fornisce istruzioni su come usare lo script quando si effettua il provisioning del cluster usando il portale di Azure.

> [AZURE.NOTE] Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md#portal) senza completarlo.

	> [AZURE.NOTE] Per installare Hue nei cluster HDInsight, la dimensione consigliata del nodo head è minimo A4 (8 core, 14 GB di memoria).

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni come mostrato di seguito:

	![Specificare i parametri di azione script per Hue](./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Specificare i parametri di azione script per Hue")

	* __NOME__: immettere un nome descrittivo per l'azione script.
	* __URI SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
	* __HEAD__: selezionare questa opzione.
	* __LAVORO__: lasciare vuoto questo campo.
	* __ZOOKEEPER__: lasciare vuoto questo campo.
	* __PARAMETRI__: lasciare vuoto questo campo.

3. Nella parte inferiore di **Azioni script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare il provisioning del cluster come descritto in [Effettuare il provisioning dei cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Usare Hue con i cluster HDInsight

Il tunneling SSH è il solo modo di accedere a Hue nel cluster una volta che è in esecuzione. Il tunneling tramite SSH consente al traffico di raggiungere direttamente il nodo head del cluster in cui viene eseguito Hue. Al termine del provisioning del cluster, seguire questa procedura per usare Hue in un cluster HDInsight Linux.

1. Utilizzare le informazioni contenute in [Utilizzare SSH Tunneling per accedere all'interfaccia utente Web Ambari, a ResourceManager, JobHistory, NameNode, Oozie e ad altre interfacce utente Web](hdinsight-linux-ambari-ssh-tunnel.md) per creare un tunnel SSH dal sistema client al cluster HDInsight e quindi configurare il browser Web per usare il tunnel SSH come proxy.

2. Dopo aver creato un tunnel SSH e configurato il browser per inoltrare il traffico attraverso di esso, è necessario trovare il nome host del nodo head. Usare la procedura seguente per ottenere queste informazioni da Ambari:

    1. In un browser, passare a https://CLUSTERNAME.azurehdinsight.net. Quando richiesto, utilizzare il nome utente amministratore e la password per l'autenticazione nel sito.
    
    2. Dal menu nella parte superiore della pagina selezionare __Hosts__.
    
    3. Selezionare la voce che inizia con __hn0__. Quando viene visualizzata la pagina, il nome host viene visualizzato nella parte superiore. Il formato del nome host è __hn0- CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Questo è il nome host da utilizzare quando ci si connette a Hue.

2. Dopo aver creato un tunnel SSH e configurato il browser per inoltrare il traffico attraverso di esso, utilizzare il browser per aprire il portale Hue in http://HOSTNAME:8888. Sostituire HOSTNAME con il nome ottenuto da Ambari nel passaggio precedente.

    > [AZURE.NOTE] Quando si accede per la prima volta, viene richiesto di creare un account per l'accesso al portale Hue. Le credenziali specificate saranno limitate al portale e non sono correlate alle credenziali amministratore o utente SSH specificate durante il provisioning del cluster.

	![Accesso al portale di Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Specificare le credenziali per il portale di Hue")

### Eseguire una query Hive

1. Nel portale di Hue fare clic su **Editor della query** e quindi su **Hive** per aprire l'editor Hive.

	![Usare Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Usare Hive")

2. Nella scheda **Assist** in **Database** dovrebbe essere visibile **hivesampletable**. Si tratta di una tabella di esempio inclusa in tutti i cluster Hadoop in HDInsight. Immettere una query di esempio nel riquadro destro e visualizzare l'output nella scheda **Risultati** nel riquadro sottostante, come illustrato nella schermata.

	![Eseguire query Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Eseguire query Hive")

	È anche possibile usare la scheda **Grafico** per vedere una rappresentazione visiva dei risultati.

### Esplorare l'archiviazione cluster

1. Nel portale di Hue fare clic su **Esplora file** nell'angolo superiore destro della barra dei menu.

2. Per impostazione predefinita, il browser file viene aperto in corrispondenza della directory **/user/myuser**. Fare clic sulla barra subito prima della directory user nel percorso per passare alla radice del contenitore di archiviazione di Azure associato al cluster.

	![Usare il browser file](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Usare il browser file")

3. Fare clic son il pulsante destro del mouse su un file o una cartella per visualizzare le operazioni disponibili. Usare il pulsante **Carica** nell'angolo destro per caricare i file nella directory corrente. Usare il pulsante **Nuovo** per creare nuovi file o directory.

> [AZURE.NOTE] Il browser file Hue può mostrare solo il contenuto del contenitore predefinito associato al cluster HDInsight. Eventuali account di archiviazione o contenitori aggiuntivi associati al cluster non saranno accessibili tramite il browser file. I contenitori aggiuntivi associati al cluster saranno comunque sempre accessibili per i processi Hive. Ad esempio, se si immette il comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` nell'editor Hive, è possibile vedere il contenuto anche dei contenitori aggiuntivi. In questo comando **newcontainer** non è il contenitore predefinito associato a un cluster.

## Considerazioni importanti

1. Lo script usato per installare Hue lo installa solo nel nodo head 0 del cluster.

2. Durante l'installazione vengono riavviati più servizi Hadoop (HDFS, YARN, MR2, Oozie) per l'aggiornamento della configurazione. Al termine dell'installazione di Hue tramite lo script, è possibile che l'avvio di altri servizi Hadoop richieda qualche istante. Ciò potrebbe influire inizialmente sulle prestazioni di Hue. Una volta avviati tutti i servizi, Hue sarà completamente funzionale.

3.	Hue non riconosce i processi di Tez, che attualmente corrisponde all'importazione predefinita per Hive. Se si vuole usare MapReduce come motore di esecuzione di Hive, aggiornare lo script per l'uso dei comandi seguenti:

		set hive.execution.engine=mr;

4.	Con i cluster Linux è possibile avere uno scenario in cui i servizi vengono eseguiti sul nodo head 0 mentre Gestione risorse potrebbe essere in esecuzione sul nodo head 1. Questo scenario potrebbe causare errori (illustrati di seguito) quando si usa Hue per visualizzare i dettagli dei processi IN ESECUZIONE nel cluster. I dettagli del processo possono tuttavia essere visualizzati dopo il completamento del processo.

	![Errore nel portale di Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Errore nel portale di Hue")

	Questo è causato da un problema noto. Come soluzione alternativa, modificare Ambari in modo che anche l'istanza di Gestione Risorse attiva venga eseguita sul nodo head 0.

5.	Hue riconosce WebHDFS mentre i cluster HDInsight usano Archiviazione di Azure Storage tramite `wasb://`. Lo script personalizzato usato con l'azione script installa WebWasb, un servizio compatibile con WebHDFS-per comunicare con WASB. Quindi, anche se in alcuni punti nel portale di Hue è indicato HDFS (come quando si sposta il mouse su **File Browser**), dovrà essere interpretato come WASB.


## Passaggi successivi

- [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

- [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

- [Installare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md). Usare la personalizzazione dei cluster per installare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0518_2016-->