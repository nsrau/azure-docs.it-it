<properties 
	pageTitle="Esempio di Hadoop per il conteggio di parole dei flussi in C# in HDInsight | Azure" 
	description="Informazioni su come scrivere i programmi MapReduce in C# che usa l'interfaccia di streaming Hadoop e su come eseguirli in HDInsight usando i cmdlet di PowerShell." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# Esempio MapReduce per il conteggio di parole dei flussi in C# in Hadoop in HDInsight
 
In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. Questa esercitazione spiega come scrivere programmi MapReduce in C# che usa l'interfaccia di streaming Hadoop e come scrivere programmi in Azure HDInsight usando i cmdlet di Azure PowerShell. 

Nell'esempio sia il mapper che il reducer sono file eseguibili che leggono l'input da [stdin][stdin-stdout-stderr] (riga per riga) e inviano l'output a [stdout][stdin-stdout-stderr]. Il programma consente di contare tutte le parole del testo.

Quando si specifica un file eseguibile per **mapper**, ogni attività del mapper avvia il file eseguibile come processo distinto all'avvio del mapper. Durante l'esecuzione dell'attività del mapper, gli input vengono convertiti in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo. Nel frattempo il mapper raccoglie gli output basati su riga dal flusso stdout del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del mapper. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore. Se la riga non contiene caratteri di tabulazione, come chiave viene considerata l'intera riga e il valore è Null. 

Quando si specifica un file eseguibile per **reducer**, ogni attività del reducer avvia il file eseguibile come processo distinto all'avvio del reducer. Durante l'esecuzione dell'attività del reducer, le coppie chiave-valore di input vengono convertite in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo. Nel frattempo il reducer raccoglie gli output basati su riga dal flusso [stdout][stdin-stdout-stderr] del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del reducer. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore. 

Per altre sull'interfaccia Hadoop Streaming, vedere la pagina relativa all'utilità [Hadoop Streaming][hadoop-streaming]. 
 
**Si apprenderà come:**	
	
* Utilizzare Azure PowerShell per eseguire un programma di streaming in C# per analizzare i dati contenuti in un file in HDInsight.		
* Scrivere codice C# in cui viene usata l'interfaccia Hadoop Streaming.


**Prerequisiti**:	

- È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://azure.microsoft.com/it-it/pricing/free-trial/).

- È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Effettuare il provisioning di cluster HDInsight](../hdinsight-provision-clusters/).

- È necessario che Azure PowerShell sia installato e configurato per l'uso con l'account utente. Per le relative istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].


## Contenuto dell'articolo
Questo argomento descrive come eseguire l'esempio, presenta il codice Java per il programma MapReduce, riepiloga le nozioni acquisite e descrive alcuni passaggi successivi. L'argomento include le sezioni seguenti:
	
1. [Eseguire l'esempio con Azure PowerShell](#run-sample)	
2. [Codice C# per Hadoop Streaming](#java-code)
3. [Riepilogo](#summary)	
4. [Passaggi successivi](#next-steps)	

<h2><a id="run-sample"></a>Eseguire l'esempio con Azure PowerShell</h2>

**Per eseguire il processo MapReduce**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

2. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


3. Eseguire il comando seguente per definire il processo MapReduce.
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	I parametri consentono di specificare le funzioni mapper e reducer, nonché i file di input e di output.
                 
4. Eseguire i comandi seguenti per eseguire il processo MapReduce, attendere il completamento del processo e quindi stampare l'errore standard:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5. Eseguire i comandi seguenti per visualizzare i risultati relativi al conteggio delle parole.

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
			$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
	      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, sarà necessario cambiare il nome del file di output.
	
<h2><a id="java-code"></a>Codice C# per Hadoop Streaming</h2>

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

 

Nel codice del mapper nel file cat.cs viene usato un oggetto StreamReader per leggere i caratteri del flusso in ingresso nella console. A sua volta, tale oggetto scrive il flusso nel flusso di output standard con il metodo statico Console.Writeline.


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


Nel codice del reducer nel file wc.cs viene usato un oggetto [StreamReader][streamreader]   per leggere dal flusso di input standard i caratteri che sono stati trasmessi dal mapper cat.exe. Durante la lettura dei caratteri con il metodo [Console.Writeline][console-writeline] viene effettuato il conteggio delle parole, inclusi gli spazi e caratteri di fine riga alla fine di ogni parola. Il totale viene quindi scritto nel flusso di output standard con il metodo [Console.Writeline][console-writeline]. 

<h2><a id="summary"></a>Riepilogo</h2>

In questa esercitazione è stata illustrata la distribuzione di un processo MapReduce in HDInsight tramite Hadoop Streaming.

<h2><a id="next-steps"></a>Passaggi successivi</h2>

Per le esercitazioni relative all'esecuzione di altri esempi in cui vengono fornite istruzioni sull'uso di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Esempio: Calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio: Conteggio parole][hdinsight-sample-wordcount]
* [Esempio: GraySort da 10 GB][hdinsight-sample-10gb-graysort]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/it-it/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/it-it/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=42-->
