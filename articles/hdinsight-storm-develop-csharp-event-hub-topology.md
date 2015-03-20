<properties
   pageTitle="Elaborare eventi dell'hub eventi con Storm in HDInsight | Azure"
   description="Informazioni su come elaborare i dati dell'hub eventi con una topologia Storm C# creata in Visual Studio tramite HDInsight Tools per Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/03/2015"
   ms.author="larryfr"/>

# Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)

L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo Spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per analizzare questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm tramite il Bolt dell'hub eventi. 

In questo documento si apprenderà come usare HDInsight Tools per Visual Studio, oltre a Spout e Bolt dell'hub eventi, per creare due topologie C#/Java ibride:

* **EventHubWriter**: genera dati casualmente e li scrive nell'hub eventi.

* **EventHubReader**: legge dati dall'hub eventi e li archivia nell'archivio tabelle di Azure.

## Prerequisiti

* <a href="../hdinsight-storm-getting-started/" target="_blank">Cluster Apache Storm in HDInsight</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Hub eventi di Azure</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools per Visual Studio</a>, versione del 17/02/2015 o successiva

## Progetto completato

È possibile scaricare una versione completa del progetto creato in questo articolo all'indirizzo [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid). Sarà tuttavia necessario fornire le impostazioni di configurazione attenendosi alla procedura riportata in questo documento.

> [AZURE.NOTE] Quando si usa il progetto completato, è necessario eseguire **Gestione pacchetti NuGet** per ripristinare i pacchetti richiesti da questa soluzione.

## Spout e Bolt dell'hub eventi

Spout e Bolt dell'hub eventi sono componenti Java che facilitano l'uso dell'hub eventi da Apache Storm. Anche se questi componenti sono scritti in Java, con HDInsight Tools per Visual Studio è possibile creare topologie ibride che combinano componenti C# e Java.

Spout e Bolt vengono distribuiti come un singolo file di archivio Java (con estensione JAR) denominato **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Scaricare il file JAR

La versione più recente del file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** è disponibile nella cartella **lib** del progetto <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a>. Per scaricare il file, usare uno dei metodi seguenti.

> [AZURE.NOTE] Per Spout e Bolt è stata inviata la richiesta di inclusione nel progetto Apache Storm. Per altre informazioni, vedere la <a href="https://github.com/apache/storm/pull/336/files">richiesta pull</a>.

* **Scaricare un file ZIP**: nel sito <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> fare clic sul pulsante **Download ZIP** per scaricare un file con estensione ZIP contenente il progetto.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Dopo aver completato il download, è possibile estrarre l'archivio. Il file sarà disponibile nella directory **lib**.

* **Clonare il progetto**: se <a href="http://git-scm.com/" target="_blank">Git</a> è installato, usare il comando seguente per clonare il repository in locale, quindi trovare il file nella directory **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, eseguire la procedura seguente.

1. Nel [portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO | Bus di servizio | Hub eventi | Creazione personalizzata**.

2. Nella finestra di dialogo **Crea un nuovo hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Infine fare clic sulla **freccia**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] È consigliabile selezionare lo stesso **Percorso** di Storm nel server HDInsight per ridurre la latenza e i costi.

2. Nella finestra di dialogo **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Dopo la creazione dell'hub eventi, selezionare lo spazio dei nomi e quindi **Hub eventi**. Infine, selezionare l'hub eventi creato precedentemente.

4. Selezionare **Configura**, quindi creare due nuovi criteri di accesso con le informazioni seguenti.

	<table>
	<tr><th>Nome</th><th>Autorizzazioni</th></tr>
	<tr><td>writer</td><td>Invio</td></tr>
	<tr><td>reader</td><td>Attesa</td></tr>
	</table>

	Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** in fondo alla pagina. Verranno creati i criteri di accesso condivisi che saranno usati per inviare (writer) e rimanere in ascolto (reader) in questo hub eventi.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** in fondo alla pagina per recuperare la chiave per i criteri **writer** e **reader**. Salvare queste informazioni perché verranno usate in seguito.

## Configurare l'archivio tabelle

L'archivio tabelle verrà usato per memorizzare i valori letti dall'hub eventi, in quanto è facilmente visibile da Visual Studio tramite **Esplora server**. Per creare un nuovo archivio tabelle, eseguire la procedura seguente.

1. Nel [portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO | Servizi dati | Archiviazione | Creazione rapida**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Immettere un **Nome** per l'account di archiviazione, selezionare un **Percorso** e quindi selezionare il **segno di spunta** per creare l'account di archiviazione.

	> [AZURE.NOTE] È consigliabile selezionare lo stesso **Percorso** dell'hub eventi e di Storm nel server HDInsight per ridurre la latenza e i costi.

3. Dopo avere effettuato il provisioning del nuovo account di archiviazione, selezionare l'account e quindi usare il collegamento **Gestisci chiavi di accesso** in fondo alla pagina per recuperare il **Nome account di archiviazione** e la **Chiave di accesso primaria**. Salvare queste informazioni che verranno usate in seguito.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Creare EventHubWriter

In questa sezione si creerà una topologia che scrive dati nell'hub eventi usando il Bolt dell'hub eventi.

1. Se la versione più recente di HDInsight Tools per Visual Studio non è ancora installata, vedere <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introduzione all'uso di HDInsight Tools per Visual Studio</a>.

2. Aprire Visual Studio, selezionare **File**, **Nuovo** e quindi **Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** espandere **Installato**, **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Applicazione Storm**. Nella parte inferiore della finestra di dialogo immettere **EventHubWriter** come nome dell'applicazione.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Dopo aver creato il progetto, è necessario disporre dei seguenti file:

	* **Program.cs**: definisce la topologia per il progetto. Per impostazione predefinita viene creata una topologia predefinita costituita da uno Spout e da un Bolt.

	* **Spout.cs**: Spout di esempio.

	* **Bolt.cs**: Bolt di esempio. Questo verrà eliminato in quanto si userà il Bolt dell'hub eventi per la scrittura nell'hub eventi.

### Configurazione

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **EventHubWriter** e quindi scegliere **Proprietà**.

2. Nelle proprietà del progetto selezionare **Impostazioni** e quindi **Il progetto non contiene un file di impostazioni predefinito. Fare clic qui per crearne uno.**

3. Immettere le impostazioni seguenti. Usare le informazioni per l'hub eventi create in precedenza nella colonna **Valore**.

	<table>
	<tr><th style="text-align:left">Nome</th><th style="text-align:left">Tipo</th><th style="text-align:left">Ambito</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">stringa</td><td style="text-align:left">Applicazione</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">stringa</td><td style="text-align:left">Applicazione</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">stringa</td><td style="text-align:left">Applicazione</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">stringa</td><td style="text-align:left">Applicazione</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Applicazione</td></tr>
	</table>

4. Salvare e chiudere la pagina delle proprietà.

### Definire la topologia

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Bolt.cs** e scegliere **Elimina**. Poiché si usa il Bolt dell'hub eventi Java, questo file non è necessario.

2. Aprire il file **Program.cs** e aggiungere quanto segue immediatamente dopo la riga `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	La prima riga legge il numero di partizioni dalle proprietà definite in precedenza. La seconda riga definisce un deserializzatore che viene usato per deserializzare i dati JSON generati dallo Spout in una `java.lang.String` in modo che il Bolt dell'hub eventi sia in grado di utilizzarli.

4. Trovare il codice seguente.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Sostituirlo con quanto segue.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Viene creato uno Spout e il numero di partizioni dell'hub eventi viene usato come hint di parallelismo per questo componente. In questo modo viene creata un'istanza dello Spout per ogni partizione.
	
	Viene anche dichiarato che il flusso di output dovrà usare il deserializzatore creato in precedenza.

5. Immediatamente dopo il codice precedente aggiungere quanto segue.

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName, 
			"true"));

	Viene creato un nuovo costruttore per il Bolt Java, che sarà usato in fase di esecuzione per configurare una nuova istanza del Bolt. In questo caso, si userà <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> per configurare lo Spout con le informazioni di configurazione dell'hub eventi aggiunte in precedenza. In particolare, questo codice viene usato da HDInsight in fase di esecuzione per eseguire le operazioni seguenti:

	* Creare una nuova istanza di **com.microsoft.eventhubs.bolt.EventHubBoltConfig** usando le informazioni dell'hub eventi fornite.
	* Creare una nuova istanza di **com.microsoft.eventhubs.bolt.EventHubBolt**, passando l'istanza di **EventHubBoltConfig**.

6. Trovare il codice seguente.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Sostituirlo con quanto segue.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Indica alla topologia di usare come Bolt **JavaComponentConstructor** dal passaggio precedente. In questa topologia è possibile fare riferimento al componente con il nome descrittivo "EventHubBolt". L'hint di parallelismo viene impostato sul numero di partizioni per l'hub eventi e sottoscrive i dati prodotti dallo Spout ("Spout").

A questo punto, la procedura per **Program.cs** è completata. La topologia è stata definita, ma ora è necessario modificare **Spout.cs** in modo da generare dati che l'hub eventi sia in grado di usare.

### Modificare lo Spout

Il Bolt dell'hub eventi prevede una singola stringa che verrà indirizzata all'hub eventi. Nell'esempio seguente si modificherà il file predefinito **Spout.cs** per produrre una stringa JSON.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubWriter** e scegliere **Gestisci pacchetti NuGet**. Cercare il pacchetto **Json.Net**, quindi aggiungerlo alla soluzione. In questo modo sarà possibile creare facilmente dati JSON che saranno inviati all'hub eventi tramite il Bolt.

1. Aprire **Spout.cs** e aggiungere quanto segue all'inizio del file.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	In questo modo sarà più facile usare i dati JSON.

3. Trovare il codice seguente.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Sostituirlo con quanto segue.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Verrà modificata la definizione dei dati creati dallo Spout per l'uso dei dati **stringa** e definito un **serializzatore JSON personalizzato**.

2. Sostituire il metodo **NextTuple** con quanto segue.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Verranno generati casualmente un ID dispositivo e un valore, quindi verrà usato Json.net per generare un oggetto JSON con questi valori.

3. Salvare il file **Spout.cs**.

A questo punto si dispone di una topologia di base che genera dati casuali e li archivia nell'hub eventi tramite il Bolt dell'hub eventi. Si procederà ora alla creazione del lettore (reader).

## Creare EventHubReader

In questa sezione si creerà una topologia che legge i dati dall'hub eventi tramite lo Spout dell'hub eventi.

2. Aprire Visual Studio, selezionare **File**, **Nuovo** e quindi **Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** espandere **Installato**, **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Applicazione Storm**. Nella parte inferiore della finestra di dialogo immettere **EventHubReader** come nome dell'applicazione.

### Configurazione

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **EventHubReader** e quindi scegliere **Proprietà**.

2. Nelle proprietà del progetto selezionare **Impostazioni** e quindi **Il progetto non contiene un file di impostazioni predefinito. Fare clic qui per crearne uno.**

3. Immettere le impostazioni seguenti. Usare le informazioni per l'hub eventi e l'account di archiviazione create in precedenza nella colonna **Valore**.

	<table>
	<tr><th style="text-align:left">Nome</th><th style="text-align:left">Tipo</th><th style="text-align:left">Ambito</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">stringa</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">stringa</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">stringa</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">stringa</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(Stringa di connessione)</th><th style="text-align:left">Applicazione</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">stringa</th><th style="text-align:left">Applicazione</th></tr>
	</table>

	Per **TableName** immettere il nome della tabella da usare per archiviare gli eventi.

	Questi valori verranno usati dalla topologia per comunicare con l'hub eventi e l'archivio tabelle.

4. Salvare e chiudere la pagina delle proprietà.

### Definire la topologia

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Spout.cs** e scegliere **Elimina**. Poiché si usa lo Spout dell'hub eventi Java, questo file non è necessario.

2. Aprire il file **Program.cs** e aggiungere quanto segue immediatamente dopo la riga `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	Il numero di partizioni viene letto e assegnato a una variabile locale, perché verrà usato più volte.

	Il costruttore `JavaComponentConstructor` definisce la modalità di costruzione dello Spout Java in fase di esecuzione. In questo caso, si userà <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> per configurare lo Spout con le informazioni di configurazione dell'hub eventi aggiunte in precedenza. In particolare, questo codice viene usato da HDInsight in fase di esecuzione per eseguire le operazioni seguenti:

	* Creare una nuova istanza di **com.microsoft.eventhubs.spout.EventHubSpoutConfig** usando le informazioni dell'hub eventi fornite.
	
	* Creare una nuova istanza di **com.microsoft.eventhubs.spout.EventHubSpout** passando l'istanza di **EventHubSpoutConfig**.

5. Trovare il codice seguente.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Sostituirlo con quanto segue.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Indica alla topologia di usare come Spout **JavaComponentConstructor** dal passaggio precedente e di assegnargli il nome "EventHubSpout". Per il componente viene anche impostato l'hint di parallelismo sul numero di partizioni nell'hub eventi. 

2. Aggiungere quanto segue immediatamente dopo il codice precedente.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Viene creato un serializzatore personalizzato che sarà usato per serializzare le informazioni prodotte dallo Spout Java nel formato JSON.

3. Trovare il codice seguente.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Sostituirlo con quanto segue.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Questo codice indica alla topologia di usare un Bolt (definito in Bolt.cs). Le informazioni aggiunte indicano alla topologia di usare il serializzatore personalizzato per i dati utilizzati da questo componente, che proviene dallo Spout Java **EventHubSpout**.

A questo punto, la procedura per **Program.cs** è completata. La topologia è stata definita, ma ora è necessario creare una classe helper per scrivere dati nell'archivio tabelle. Occorre quindi modificare il file **Bolt.cs** affinché sia in grado di riconoscere i dati prodotti dallo Spout.

### Creare una classe helper

Quando si scrivono dati nell'archivio tabelle, è necessario creare una classe in cui siano descritti questi dati.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubReader** e scegliere **Aggiungi**, **Nuova classe**. Denominare la nuova classe **Devices.cs**.

2. Aprire **Devices.cs** e sostituire il codice predefinito con il seguente.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;
		
		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }
		
		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Nell'archivio tabelle verranno create entità che consistono in una chiave di partizione (impostata sull'ID dispositivo letto dall'hub eventi), una chiave di riga univoca e un valore letto dall'hub eventi. A ogni entità sarà anche associato un timestamp, che verrà creato automaticamente all'inserimento dell'entità nella tabella.

### Modificare il Bolt

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubReader** e scegliere **Gestisci pacchetti NuGet**. Cercare il pacchetto **Json.Net**, quindi aggiungerlo alla soluzione. In questo modo sarà più facile elaborare i dati JSON ricevuti dallo Spout. Aggiungere anche il pacchetto **Archiviazione di Microsoft Azure** che consentirà di scrivere nell'archivio tabelle.

1. Aprire **Bolt.cs** e aggiungere quanto segue all'inizio del file.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	In questo modo sarà possibile usare più facilmente i dati JSON dal Bolt e scriverli nell'archivio tabelle.

2. Trovare l'istruzione `private int count;` e sostituirla con quanto segue.

        private CloudTable table;

	Verrà usata durante la connessione alla tabella.

4. Trovare il codice seguente.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Sostituirlo con quanto segue.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Indicherà al Bolt che riceverà un valore **stringa** invece di un **int** e che i dati dovranno essere deserializzati tramite **CustomizedInteropJSONDeserialzer**.

3. Immediatamente dopo il codice precedente aggiungere quanto segue.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Verrà stabilita la connessione alla tabella **events** usando la stringa di connessione configurata in precedenza. Se non esiste, verrà creata.

2. Trovare il metodo **Execute** e sostituirlo con quanto segue.

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
            }
        }

	Questo codice usa Json.net per analizzare i dati JSON dallo Spout e quindi rileva i valori dai campi **deviceId** e **deviceValue**. Viene quindi creato un nuovo oggetto **Device**, usando **deviceId** durante l'inizializzazione per impostare la chiave di partizione per la tabella. Il valore viene quindi impostato su **deviceValue** e infine viene inserita l'entità nella tabella.

A questo punto si dispone di una topologia completa che leggerà i dati dall'hub eventi e li archivierà nell'archivio tabelle in una tabella denominata **events**.

## Distribuire le topologie

1. Aprire la soluzione **EventHubReader**. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubReader** e scegliere **Submit to Storm on HDInsight**.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Nella finestra di dialogo **Submit Topology** selezionare una voce in **Storm Cluster**. Espandere **Additional Configurations**, selezionare **Java File Paths**, fare clic su **...** e selezionare la directory che contiene il file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** scaricato in precedenza. Infine selezionare **Submit**.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Dopo l'invio della topologia, verrà aperto **Storm Topologies Viewer**. Selezionare la topologia **EventHubReader** a sinistra della finestra di dialogo per visualizzare le relative statistiche. Al momento non dovrebbe verificarsi alcuna operazione, perché non sono ancora stati scritti eventi nell'hub eventi.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Aprire la soluzione **EventHubWriter**. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubWriter** e scegliere**Submit to Storm on HDInsight**.

2. Nella finestra di dialogo **Submit Topology** selezionare una voce in **Storm Cluster**. Espandere **Additional Configurations**, selezionare **Java File Paths**, fare clic su **...** e selezionare la directory che contiene il file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** scaricato in precedenza. Infine selezionare **Submit**.

5. Dopo l'invio della topologia, aggiornare l'elenco delle topologie in **Storm Topologies Viewer** per verificare che siano entrambe in esecuzione nel cluster.

6. Se sono in esecuzione, selezionare **Esplora server**, quindi espandere**Azure**, **Archiviazione** e infine espandere l'account di archiviazione creato in precedenza. Nell'account di archiviazione espandere **Tabelle**. Infine fare doppio clic sulla tabella **events** per aprirla. Dovrebbero essere visibili i dati archiviati nella tabella dalla topologia **EventHubReader**.

	* Gli eventi vengono generati dalla topologia **EventHubWriter** e scritti nell'hub eventi.

	* **EventHubReader** legge quindi gli eventi dall'hub eventi e li archivia nella tabella **events** nell'archivio tabelle.

## Arrestare le topologie

Per arrestare le topologie, selezionarle singolarmente in **Storm Topology Viewer**, quindi fare clic su **Kill**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Riepilogo

In questo documento si è appreso come usare Spout e Bolt dell'hub eventi Java da una topologia C# per usare i dati nell'hub eventi di Azure. Per altre informazioni sulla creazione di topologie C#, vedere gli articoli seguenti.

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [Esempi per Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
