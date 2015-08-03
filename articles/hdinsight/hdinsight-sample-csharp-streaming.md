<properties
	pageTitle="Esempio di Hadoop per il conteggio di parole dei flussi in C# | Microsoft Azure"
	description="Informazioni su come scrivere programmi MapReduce in C# usando l'interfaccia Hadoop Streaming e su come eseguirli in HDInsight mediante i cmdlet di PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# Esempio MapReduce per il conteggio di parole dei flussi in C# in Hadoop in HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. Questa esercitazione spiega come scrivere programmi MapReduce in C# usando l'interfaccia Hadoop Streaming e come scrivere programmi in Azure HDInsight usando i cmdlet di Azure PowerShell.

> [AZURE.NOTE]La procedura descritta in questa esercitazione è valida solo per i cluster HDInsight basati su Windows. Per un esempio di flusso per cluster HDInsight basati su Linux, vedere [Sviluppo di programmi per la creazione di flussi Python per HDInsight](hdinsight-hadoop-streaming-python.md).

Nell'esempio il mapper e il reducer sono file eseguibili che leggono l'input da [stdin][stdin-stdout-stderr] (riga per riga) e inviano l'output a [stdout][stdin-stdout-stderr]. Il programma consente di contare tutte le parole del testo.

Quando si specifica un file eseguibile per **mapper**, ogni attività del mapper avvia il file eseguibile come processo distinto all'avvio del mapper. Durante l'esecuzione dell'attività del mapper, gli input vengono convertiti in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo.

Nel frattempo il mapper raccoglie gli output basati su riga dal flusso stdout del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del mapper. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore. Se la riga non contiene caratteri di tabulazione, come chiave viene considerata l'intera riga e il valore è Null.

Quando si specifica un file eseguibile per **reducer**, ogni attività del reducer avvia il file eseguibile come processo distinto all'avvio del reducer. Durante l'esecuzione dell'attività del reducer, le coppie chiave-valore di input vengono convertite in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo.

Nel frattempo il reducer raccoglie gli output basati su riga dal flusso [stdout][stdin-stdout-stderr] del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del reducer. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore.

Per altre informazioni sull'interfaccia Hadoop Streaming, vedere la pagina relativa all'utilità [Hadoop Streaming][hadoop-streaming].

**In questa esercitazione si apprenderà come:**

* Usare Azure PowerShell per eseguire un programma per la creazione di flussi in C# da usare per l'analisi dei dati contenuti in un file in HDInsight.
* Scrivere codice C# in cui viene usata l'interfaccia Hadoop Streaming.


**Prerequisiti**:

Per eseguire le procedure descritte è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un cluster HDInsight**. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md).
- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Eseguire l'esempio con Azure PowerShell

**Per eseguire il processo MapReduce**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

3. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Per definire il processo MapReduce, eseguire il comando seguente:

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	I parametri consentono di specificare le funzioni mapper e reducer, nonché i file di input e di output.

5. Eseguire i comandi seguenti per eseguire il processo MapReduce, attendere il completamento del processo e quindi stampare il messaggio di errore standard:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. Eseguire i comandi seguenti per visualizzare i risultati relativi al conteggio delle parole:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, è necessario cambiare il nome del file di output.


## <a id="java-code"></a>Codice C# per Hadoop Streaming


Nel programma MapReduce vengono usate l'applicazione cat.exe come interfaccia di mapping per trasmettere il testo nella console e l'applicazione wc.exe come interfaccia di riduzione per contare il numero di parole trasmesse da un documento. Sia il mapper che il reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



Il codice del mapper nel file cat.cs usa un oggetto [StreamReader][streamreader] per leggere i caratteri del flusso in ingresso nella console. A sua volta, tale oggetto scrive il flusso nel flusso di output standard con il metodo statico [Console.Writeline][console-writeline].


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


Il codice del reducer nel file wc.cs usa un oggetto [StreamReader][streamreader] per leggere dal flusso di input standard i caratteri che sono stati trasmessi dal mapper cat.exe. Durante la lettura dei caratteri con il metodo [Console.Writeline][console-writeline] viene eseguito il conteggio delle parole, inclusi gli spazi e caratteri di fine riga alla fine di ogni parola. Il totale viene quindi scritto nel flusso di output standard con il metodo [Console.Writeline][console-writeline].


## <a id="summary"></a>Riepilogo

In questa esercitazione è stata illustrata la distribuzione di un processo MapReduce in HDInsight mediante Hadoop Streaming.

## <a id="next-steps"></a>Passaggi successivi


Per esercitazioni relative all'esecuzione di altri esempi in cui vengono fornite istruzioni sull'esecuzione di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Esempio di calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio di conteggio delle parole][hdinsight-sample-wordcount]
* [Esempio GraySort da 10 GB][hdinsight-sample-10gb-graysort]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=July15_HO4-->