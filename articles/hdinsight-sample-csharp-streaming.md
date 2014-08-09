<properties linkid="manage-services-hdinsight-sample-csharp-streaming" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight C# streaming wordcount sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight C# streaming wordcount sample" authors="bradsev" />

Esempio relativo al conteggio di parole con flusso in C\# di HDInsight
======================================================================

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. In questa esercitazione viene spiegato come scrivere programmi MapReduce in C\# che utilizza l'interfaccia di flusso Hadoop e come scrivere programmi in Azure HDInsight utilizzando Azure PowerShell.

Nell'esempio sia il mapper che il reducer sono file eseguibili che leggono l'input da [stdin](http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx) (riga per riga) e inviano l'output a [stdout](http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx). Il programma consente di contare tutte le parole del testo.

Quando si specifica un file eseguibile per **mapper**, ogni attività del mapper avvia il file eseguibile come processo distinto all'avvio del mapper. Durante l'esecuzione dell'attività del mapper, gli input vengono convertiti in righe che vengono quindi inviate al flusso [stdin](http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx) del processo. Nel frattempo il mapper raccoglie gli output basati su riga dal flusso stdout del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del mapper. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore. Se la riga non contiene caratteri di tabulazione, come chiave viene considerata l'intera riga e il valore è Null.

Quando si specifica un file eseguibile per **reducer**, ogni attività del reducer avvia il file eseguibile come processo distinto all'avvio del reducer. Durante l'esecuzione dell'attività del mapper, le coppie chiave-valore di input vengono convertite in righe che vengono quindi inviate al flusso [stdin](http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx) del processo. Nel frattempo il reducer raccoglie gli output basati su riga dal flusso [stdout](http://msdn.microsoft.com/it-it/library/3x292kth(v=vs.110).aspx) del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del reducer. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore.

Per ulteriori informazioni sull'interfaccia Hadoop Streaming, vedere la pagina relativa all'utilità [Hadoop Streaming](http://wiki.apache.org/hadoop/HadoopStreaming).

**Si apprenderà come:**

-   Utilizzare Azure PowerShell per eseguire un programma per la creazione di flussi in C\# per analizzare i dati contenuti in un file in HDInsight.
-   Scrivere codice C\# in cui viene utilizzata l'interfaccia Hadoop Streaming.

**Prerequisiti**:

-   È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://www.windowsazure.com/it-it/pricing/free-trial/).

-   È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   È necessario che Azure PowerShell sia installato e configurato per l'utilizzo con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

Contenuto dell'articolo
-----------------------

In questo argomento viene illustrato come eseguire l'esempio, viene presentato il codice Java per il programma MapReduce, vengono riepilogate le nozioni acquisite e vengono descritti alcuni passaggi successivi. L'argomento include le sezioni seguenti:

1.  [Esecuzione dell'esempio con Azure PowerShell](#run-sample)
2.  [Codice C\# per Hadoop Streaming](#java-code)
3.  [Riepilogo](#summary)
4.  [Passaggi successivi](#next-steps)

Esecuzione dell'esempio con Azure PowerShell
--------------------------------------------

**Per eseguire il processo MapReduce**

1.  Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

2.  Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:

         $subscriptionName = "<SubscriptionName>"   # Azure subscription name
         $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Eseguire il comando seguente per definire il processo MapReduce.

         # Create a MapReduce job definition for the streaming job.
         $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    I parametri consentono di specificare le funzioni mapper e reducer, nonché i file di input e di output.

4.  Eseguire i comandi seguenti per eseguire il processo MapReduce, attendere il completamento del processo e quindi stampare l'errore standard:

         # Run the C# Streaming MapReduce job.
         # Wait for the job to complete.
         # Print output and standard error file of the MapReduce job
         Select-AzureSubscription $subscriptionName
         $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  Eseguire i comandi seguenti per visualizzare i risultati relativi al conteggio delle parole.

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

Codice C\# per Hadoop Streaming
-------------------------------

Nel programma MapReduce vengono utilizzate l'applicazione cat.exe come interfaccia di mapping per trasmettere il testo nella console e l'applicazione wc.exe come interfaccia di riduzione per contare il numero di parole trasmesse da un documento. Sia il mapper che il reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).

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

Nel codice del mapper nel file cat.cs viene utilizzato un oggetto StreamReader per leggere i caratteri del flusso in ingresso nella console. A sua volta, tale oggetto scrive il flusso nel flusso di output standard con il metodo statico Console.Writeline.

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

Nel codice del reducer nel file wc.cs viene utilizzato un oggetto [StreamReader](http://msdn.microsoft.com/it-it/library/system.io.streamreader.aspx) per leggere dal flusso di input standard i caratteri che sono stati trasmessi dal mapper cat.exe. Durante la lettura dei caratteri con il metodo [Console.Writeline](http://msdn.microsoft.com/it-it/library/system.console.writeline) viene effettuato il conteggio delle parole, inclusi gli spazi e caratteri di fine riga alla fine di ogni parola. Il totale viene quindi scritto nel flusso di output standard con il metodo [Console.Writeline](http://msdn.microsoft.com/it-it/library/system.console.writeline).

Riepilogo
---------

In questa esercitazione è stata illustrata la distribuzione di un processo MapReduce in HDInsight tramite Hadoop Streaming.

Passaggi successivi
-------------------

Per le esercitazioni relative all'esecuzione di altri esempi in cui vengono fornite istruzioni sull'utilizzo di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Esempio: Calcolo del Pi greco](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Esempio: Conteggio parole](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Esempio: GraySort da 10 GB](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

