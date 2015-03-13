<properties 
   pageTitle="Distribuzione e gestione di topologie Storm in HDInsight | Azure" 
   description="Informazioni su come distribuire, monitorare e gestire le topologie Storm usando Storm Dashboard, incluso in Apache Storm in HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags 
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Distribuzione e gestione di topologie Apache Storm in HDInsight

Storm Dashboard consente di distribuire e gestire facilmente topologie Apache Storm nel cluster mediante il Web browser in uso. È possibile usare il dashboard anche per monitorare e gestire topologie in esecuzione. Se si usa Visual Studio, HDInsight Tools per Visual Studio fornisce funzionalità simili in Visual Studio.

Sia Storm Dashboard che le funzionalità Storm di HDInsight Tools si basano sull'API REST Storm, che consente di creare soluzioni di monitoraggio e gestione.

## Prerequisiti

* **Apache Storm in HDInsight**: vedere <a href="../hdinsight-storm-getting-started/" target="_blank">Introduzione ad Apache Storm in HDInsight</a> per i passaggi relativi alla creazione di un cluster.

* Per **Storm Dashboard**: un Web browser di ultima generazione che supporta HTML5.

* Per **Visual Studio**: Azure SDK 2.5.1 o versione successiva e HDInsight Tools per Visual Studio. Vedere <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Introduzione all'uso di HDInsight Tools per Visual Studio</a> per installare e configurare HDInsight Tools per Visual Studio.

	Una delle seguenti versioni di Visual Studio:

	* Visual Studio 2012 con <a href="http://www.microsoft.com/it-it/download/details.aspx?id=39305" target="_blank">Update 4</a>

	* Visual Studio 2013 con <a href="http://www.microsoft.com/it-it/download/details.aspx?id=44921" target="_blank">Update 4</a> o <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/it-it/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Attualmente HDInsight Tools per Visual Studio supporta Storm solo su cluster HDInsight versione 3.2.

## Storm Dashboard

Storm Dashboard è disponibile nel cluster Storm. L'URL è **https://&lt;NomeCluster>.azurehdinsight.net/**, dove **NomeCluster** è il nome del cluster Storm in HDInsight. È possibile anche accedere al dashboard usando il collegamento **Storm Dashboard** dal dashboard del cluster nel portale di Azure.

![the portal with storm dashboard highlighted][hdinsight-dashboard]

Nella parte superiore di Storm Dashboard selezionare **Submit Topology**. Seguire le istruzioni visualizzate nella pagina per eseguire una topologia di esempio o per caricare ed eseguire una topologia creata.

![the submit topology page][storm-dashboard-submit]

### Interfaccia utente di Storm

In Storm Dashboard selezionare il collegamento **Storm UI**. Verranno visualizzate informazioni sul cluster, nonché l'elenco delle topologie in esecuzione.

![the storm ui][storm-dashboard-ui] 

#### Pagina principale

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le seguenti informazioni.

* **Cluster Summary**: informazioni di base sul cluster Storm.

* **Topology summary**: elenco delle topologie in esecuzione. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su topologie specifiche.

* **Supervisor summary**: informazioni su Storm Supervisor.

* **Nimbus Configuration**: configurazione Nimbus per il cluster.

#### Topology summary

Se si seleziona un collegamento nella sezione **Topology summary**, verranno visualizzate le seguenti informazioni sulla topologia.

* **Topology summary**: informazioni di base sulla topologia.

* **Topology actions**: azioni di gestione che è possibile eseguire per la topologia.

	* **Activate**: riprende l'elaborazione di una topologia disattivata.
	
	* **Deactivate**: sospende una topologia in esecuzione.
	
	* **Rebalance**: regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster.
	
		Per altre informazioni, vedere l'articolo relativo al  <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">parallelismo di una topologia Storm</a>
	
	* **Kill**: termina una topologia di Storm dopo il timeout specificato.

* **Topology stats**: statistiche sulla topologia. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per cui le voci devono rimanere nella pagina.

* **Spouts**: spout usati dalla topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su spout specifici.

* **Bolts**: bolt usati nella topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su bolt specifici.

* **Topology configuration**: configurazione della topologia selezionata.

#### Riepilogo di spout e bolt 

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le seguenti informazioni.

* **Component summary**: informazioni di base sullo spout o sul bolt.

* **Spout/Bolt stats**: statistiche relative allo spout o al bolt. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per cui le voci devono rimanere nella pagina.

* **Input stats** (solo bolt): informazioni sui flussi di input usati dal bolt.

* **Output stats**: informazioni sui flussi generati dallo spout o dal bolt.

* **Executors**: informazioni sulle istanze dello spout o del bolt. Selezionare la voce **Port** relativa a un esecutore specifico per visualizzare il log delle informazioni di diagnostica generate per questa istanza.

* **Errors**: informazioni su eventuali errori dello spout o del bolt.

## HDInsight Tools per Visual Studio

HDInsight Tools consente di inviare topologie C# o ibride al cluster Storm. Nei seguenti passaggi viene usata un'applicazione di esempio. Per informazioni sulla creazione di topologie mediante HDInsight Tools, vedere [Sviluppo di topologie C# mediante HDInsight Tools per Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/).

Eseguire i seguenti passaggi per distribuire una topologia di esempio nel cluster Storm in HDInsight e quindi visualizzare e gestire tale topologia.

1. Se non si è ancora provveduto a installare la versione più recente di HDInsight Tools per Visual Studio, vedere <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Introduzione all'uso di HDInsight Tools per Visual Studio</a>.

2. Aprire Visual Studio, selezionare **File**, **Nuovo** e quindi **Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** espandere **Installato**, **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Storm Sample**. Nella parte inferiore della finestra di dialogo immettere un nome per l'applicazione.

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Submit to Storm on HDInsight**.

	> [AZURE.NOTE] Se richiesto, immettere le credenziali di accesso per la sottoscrizione Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Selezionare il cluster Storm in HDInsight dall'elenco a discesa **Storm Cluster**, quindi selezionare **Submit**. È possibile verificare se l'invio è riuscito o meno usando la finestra **Output**.

3. Una volta che la topologia è stata inviata correttamente, verrà visualizzato l'elenco **Storm Topologies** relativo al cluster. Per visualizzare informazioni sulla topologia in esecuzione, selezionarla dall'elenco.

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] È possibile visualizzare **Storm Topologies** anche da **Esplora server**, espandendo **Azure**, **HDInsight** e quindi facendo clic su un cluster Storm in HDInsight e selezionando **View Storm Topologies**.

	Usare i collegamenti per gli spout o i bolt per visualizzare informazioni su questi componenti. Verrà aperta una nuova finestra per ogni elemento selezionato.

4. Nella visualizzazione **Topology Summary** selezionare **Kill** per arrestare la topologia.

	> [AZURE.NOTE] le topologie Storm continuano l'esecuzione fino a quando non vengono arrestate o fino a quando il cluster non viene eliminato.

## API REST

L'interfaccia utente di Storm si basa sull'API REST. È pertanto possibile eseguire funzionalità di gestione e monitoraggio simili usando l'API. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

L'API REST Storm è documentata in <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>. Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

### URI di base

L'URI di base per l'API REST nei cluster HDInsight è **https://&lt;NomeCluster>.azurehdinsight.net/stormui/api/v1/**, dove **NomeCluster** è il nome del cluster Storm in HDInsight in uso.

### Autenticazione

Le richieste all'API REST devono usare **l'autenticazione di base** con il nome e la password amministratore del cluster HDInsight. 

> [AZURE.NOTE] Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare **sempre** HTTPS per proteggere le comunicazioni con il cluster.

### Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster o nei computer che si trovano nella stessa rete virtuale di Azure del cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server Zookeeper non sarà accessibile da Internet.

## Passaggi successivi

A questo punto, dopo aver appreso come distribuire e monitorare le topologie usando Storm Dashboard, passare ai seguenti argomenti: [Sviluppo di topologie C# per Apache Storm in HDInsight mediante Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/) e [Sviluppo di topologie basate su Java per Apache Storm in HDInsight](../hdinsight-storm-develop-java-topology/).


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=45--> 
