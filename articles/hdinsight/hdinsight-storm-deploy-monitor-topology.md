<properties
   pageTitle="Distribuzione e gestione di topologie Apache Storm in HDInsight | Microsoft Azure"
   description="Informazioni su come distribuire, monitorare e gestire le topologie Apache Storm mediante Storm Dashboard in HDInsight. Utilizzare gli strumenti Hadoop per Visual Studio"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Distribuire e gestire topologie Apache Storm in HDInsight

Storm Dashboard consente di distribuire e gestire facilmente topologie Apache Storm nel cluster mediante il Web browser in uso. È possibile usare il dashboard anche per monitorare e gestire topologie in esecuzione. Se si usa Visual Studio, HDInsight Tools per Visual Studio fornisce funzionalità simili in Visual Studio.

Storm Dashboard e le funzionalità Storm di HDInsight Tools si basano sull'API REST Storm, che consente di creare soluzioni di monitoraggio e gestione.

##Prerequisiti

* **Apache Storm i HDInsight**: per i passaggi relativi alla creazione di un cluster, vedere <a href="../hdinsight-storm-getting-started/" target="_blank">Introduzione a Storm con HDInsight</a>.

* Per **Storm Dashboard**: un Web browser di ultima generazione che supporta HTML5.

* Per **Visual Studio**: Azure SDK 2.5.1 o versione successiva e HDInsight Tools per Visual Studio. Per installare e configurare gli strumenti HDInsight per Visual Studio, vedere <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introduzione all'uso di HDInsight Tools per Visual Studio</a>.

	Una delle seguenti versioni di Visual Studio:

	* Visual Studio 2012 con <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>

	* Visual Studio 2013 con <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Update 4</a> o <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE]Attualmente HDInsight Tools per Visual Studio supporta Storm solo su cluster HDInsight versione 3.2.

##Storm Dashboard

Dashboard di Storm è una pagina Web disponibile nel cluster Storm. L'URL è **https://&lt;clustername>.azurehdinsight.net/**, dove **NomeCluster** è il nome del cluster Storm in HDInsight.

Nella parte superiore di Storm Dashboard selezionare **Submit Topology**. Seguire le istruzioni visualizzate nella pagina per eseguire una topologia di esempio o per caricare ed eseguire una topologia creata.

![pagina Submit Topology][storm-dashboard-submit]

###Interfaccia utente di Storm

In Storm Dashboard selezionare il collegamento **Storm UI**. Verranno visualizzate informazioni sul cluster, nonché l'elenco delle topologie in esecuzione.

![interfaccia utente Storm][storm-dashboard-ui]

> [AZURE.NOTE]Con alcune versioni di Internet Explorer, potrebbe risultare che l'interfaccia utente di Storm non viene aggiornata dopo il primo utilizzo. Ad esempio, potrebbero non essere visibili nuove topologie inviate oppure potrebbe risultare ancora attiva la visualizzazione di una topologia precedentemente disattivata. Microsoft è al corrente di questo problema e sta lavorando a una soluzione.

####Pagina principale

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le informazioni seguenti.

* **Cluster summary**: informazioni di base sul cluster Storm.

* **Topology summary**: elenco delle topologie in esecuzione. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su topologie specifiche.

* **Supervisor summary**: informazioni su Storm Supervisor.

* **Nimbus configuration**: configurazione Nimbus per il cluster.

####Topology summary

Se si seleziona un collegamento nella sezione **Topology summary**, verranno visualizzate le informazioni seguenti sulla topologia.

* **Topology summary**: informazioni di base sulla topologia.

* **Topology actions**: azioni di gestione che è possibile eseguire per la topologia.

	* **Activate**: riprende l'elaborazione di una topologia disattivata.

	* **Deactivate**: sospende una topologia in esecuzione.

	* **Rebalance**: regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster.

		Per altre informazioni, vedere l'articolo relativo al <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">parallelismo di una topologia Storm</a>.

	* **Kill**: termina una topologia Storm dopo il timeout specificato.

* **Topology stats**: statistiche sulla topologia. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per le rimanenti voci della pagina.

* **Spouts**: spout usati dalla topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su spout specifici.

* **Bolts**: bolt usati nella topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su bolt specifici.

* **Topology configuration**: configurazione della topologia selezionata.

####Riepilogo di spout e bolt

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le informazioni seguenti:

* **Component summary**: informazioni di base sullo spout o sul bolt.

* **Spout/Bolt stats**: statistiche relative allo spout o al bolt. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per le rimanenti voci della pagina.

* **Input stats** (solo bolt): informazioni sui flussi di input usati dal bolt.

* **Output stats**: informazioni sui flussi generati dallo spout o dal bolt.

* **Executors**: informazioni sulle istanze dello spout o del bolt. Selezionare la voce **Port** relativa a un esecutore specifico per visualizzare il log delle informazioni di diagnostica generate per questa istanza.

* **Errors**: informazioni su eventuali errori dello spout o del bolt.

##HDInsight Tools per Visual Studio

HDInsight Tools consente di inviare topologie C# o ibride al cluster Storm. Nei seguenti passaggi viene usata un'applicazione di esempio. Per informazioni sulla creazione delle proprie tipologie, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Eseguire i passaggi seguenti per distribuire una topologia di esempio nel cluster Storm in HDInsight e quindi visualizzare e gestire tale topologia.

1. Se la versione più recente di HDInsight Tools per Visual Studio non è ancora installata, vedere <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introduzione all'uso di HDInsight Tools per Visual Studio</a>.

2. Aprire Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** espandere **Installato** > **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Storm Sample**. Nella parte inferiore della finestra di dialogo digitare un nome per l'applicazione.

	![immagine](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Submit to Storm on HDInsight**.

	> [AZURE.NOTE]Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Selezionare il cluster Storm in HDInsight dall'elenco a discesa **Storm Cluster** e quindi selezionare **Submit**. È possibile verificare se l'invio è riuscito o meno usando la finestra **Output**.

3. Dopo che la topologia è stata inviata correttamente, verrà visualizzato l'elenco **Storm Topologies** relativo al cluster. Per visualizzare informazioni sulla topologia in esecuzione, selezionarla dall'elenco.

	![monitor Visual Studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

	> [AZURE.NOTE]È possibile visualizzare **Storm Topologies** anche da **Esplora server**, espandendo **Azure** > **HDInsight** e quindi facendo clic su un cluster Storm in HDInsight e selezionando **View Storm Topologies**.

	Usare i collegamenti per gli spout o i bolt per visualizzare informazioni su questi componenti. Verrà aperta una nuova finestra per ogni elemento selezionato.

4. Nella visualizzazione **Topology Summary** selezionare **Kill** per arrestare la topologia.

	> [AZURE.NOTE]Le topologie Storm continuano l'esecuzione fino a quando non vengono arrestate o fino a quando il cluster non viene eliminato.

##API REST

L'interfaccia utente di Storm si basa sull'API REST. È pertanto possibile eseguire funzionalità di gestione e monitoraggio simili usando l'API REST. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

Per altre informazioni, vedere l'articolo relativo all'<a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">API REST dell'interfaccia utente di Storm</a>. Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

###URI di base

L'URI di base per l'API REST nei cluster HDInsight è **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, dove **NomeCluster** è il nome del cluster Storm in HDInsight in uso.

###Autenticazione

Le richieste all'API REST devono usare l'**autenticazione di base** con il nome e la password amministratore del cluster HDInsight.

> [AZURE.NOTE]Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare **sempre** HTTPS per proteggere le comunicazioni con il cluster.

###Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster o nei computer che si trovano nella stessa rete virtuale di Azure del cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server Zookeeper non sarà accessibile da Internet.

##Passaggi successivi

A questo punto, dopo aver appreso come distribuire e monitorare le topologie usando Storm Dashboard, è possibile passare agli argomenti seguenti:

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Sviluppare topologie basate su Java per Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md)

Per un elenco di altre topologie di esempio, vedere [Esempi di topologie Storm per Apache Storm in HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png

<!---HONumber=Sept15_HO4-->