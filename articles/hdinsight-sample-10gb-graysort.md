<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Esempio GraySort da 10 GB | Azure" metaKeywords="hdinsight, hadoop, hdinsight administration, hdinsight administration azure" description="Learn how to run a general purpose GraySort on Hadoop with HDInsight using Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The 10GB GraySort sample" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Esempio GraySort di Hadoop da 10 GB in HDInsight
 
Questo argomento di esempio illustra come eseguire un programma MapReduce GraySort per Hadoop di uso generale in Azure HDInsight usando Azure PowerShell. GraySort è un ordinamento benchmark che usa come metrica la velocità di ordinamento (TB/minuto) ottenuta durante l'ordinamento di quantità molto elevate di dati, in genere almeno 100 TB. 

In questo esempio vengono usati solo 10 GB di dati, in modo da consentire un'esecuzione relativamente rapida. Vengono usate le applicazioni MapReduce sviluppate da Owen O'Malley e Arun Murthy, vincitrici del benchmark annuale di ordinamento di terabyte di uso generale ("daytona") nel 2009 con una velocità pari a 0,578 TB/min (100 TB in 173 minuti). Per altre informazioni su questo e su altri benchmark di ordinamento, vedere il sito [Sortbenchmark](http://sortbenchmark.org/).

In questo esempio vengono usati tre set di programmi MapReduce:	
 
1. **TeraGen** è un programma MapReduce che può essere usato per generare le righe di dati da ordinare.
2. **TeraSort** esegue il campionamento dei dati di input e usa MapReduce per ordinare i dati in un ordine totale. TeraSort è un ordinamento standard di funzioni MapReduce, ad eccezione di un partitioner personalizzato che usa un elenco ordinato di N-1 chiavi sottoposte a campionamento che definiscono l'intervallo di chiavi per ogni riduzione. In particolare, tutte le chiavi che corrispondono ai criteri sample[i-1] <= key < sample[i] vengono inviate a reduce i. Ciò consente di assicurare che l'output di reduce i sia inferiore all'output di reduce i+1.
3. **TeraValidate** è un programma MapReduce che convalida l'ordinamento globale dell'output. Crea una funzione Map per ogni file nella directory di output e ogni funzione Map assicura che ogni chiave sia inferiore o uguale alla precedente. La funzione Map genera inoltre record della prima e dell'ultima chiave di ogni file, mentre la funzione Reduce assicura che la prima chiave del file sia superiore all'ultima chiave di file i-1. Eventuali problemi vengono segnalati come output della funzione Reduce insieme alle chiavi che non rispettano l'ordinamento.

Il formato di input e il formato di output, usati da tutte e tre le applicazioni, consentono di leggere e scrivere i file di testo nel formato corretto. Nell'output della funzione Reduce la replica è impostata su 1, invece che sul valore predefinito 3, poiché il contesto del benchmark non necessita della replica dei dati di output in più nodi.

 
**Si apprenderà come:**		
* Come usare Azure PowerShell per eseguire una serie di programmi MapReduce in Azure HDInsight.		
* Aspetto di un programma MapReduce in Java.


**Prerequisiti**:	

- È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://azure.microsoft.com/it-it/pricing/free-trial/).

- È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight](../hdinsight-provision-clusters/)

- È necessario che Azure PowerShell sia installato e configurato per l'uso con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell][powershell-install-configure].

##Contenuto dell'articolo
Questo argomento descrive come eseguire la serie di programmi MapReduce che costituiscono l'esempio, presenta il codice Java per il programma MapReduce, riepiloga i concetti appresi e indica alcuni passaggi successivi. L'argomento include le sezioni seguenti:
	
1. [Eseguire l'esempio con Azure PowerShell](#run-sample)	
2. [Codice Java per il programma MapReduce TeraSort](#java-code)
3. [Riepilogo](#summary)	
4. [Passaggi successivi](#next-steps)	

<h2><a id="run-sample"></a>Eseguire l'esempio con Azure PowerShell</h2>

L'esempio richiede tre attività, ognuna delle quali corrisponde a uno dei programmi MapReduce illustrati nell'introduzione:	

1. Generazione dei dati per l'ordinamento tramite l'esecuzione del processo MapReduce **TeraGen**.	
2. Ordinamento dei dati tramite l'esecuzione del processo MapReduce **TeraSort**.		
3. Conferma del corretto ordinamento dei dati tramite l'esecuzione del processo MapReduce **TeraValidate**.	


**Per eseguire il programma TeraGen**	

1. Aprire Azure PowerShell. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].
2. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"
                 
4. Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

	> [WACOM.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.
	
	L'argomento *"-Dmapred.map.tasks=50"* specifica che verranno create 50 funzioni Map per l'esecuzione del processo. L'argomento *100000000* specifica la quantità di dati da generare. L'argomento finale, */example/data/10GB-sort-input*, specifica la directory di output in cui vengono salvati i risultati e che include l'input per la fase di ordinamento successiva.

5. Eseguire i comandi seguenti per inviare il processo, attendere il completamento del processo e quindi stampare l'errore standard:

		# Run the TeraGen MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName         
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


**Per eseguire il programma TeraSort**			

1. Aprire Azure PowerShell.
2. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"

3. Per definire il processo MapReduce, eseguire il comando seguente: 	 

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

	L'argomento *"-Dmapred.map.tasks=50"* specifica che verranno create 50 funzioni Map per l'esecuzione del processo. L'argomento *100000000* specifica la quantità di dati da generare. Gli ultimi due argomenti specificano le directory di input e di output. 

4. Eseguire i comandi seguenti per inviare il processo, attendere il completamento del processo e quindi stampare l'errore standard:

		# Run the TeraSort MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job 
		Select-AzureSubscription $subscriptionName        
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


**Per eseguire il programma TeraValidate**
		 		
1. Aprire Azure PowerShell.
2. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:
	
		# Provide the Azure subscription name and the HDInsight cluster name.
		$subscriptionName = "myAzureSubscriptionName"   
		$clusterName = "myClusterName"
                 
3. Per definire il processo MapReduce, eseguire il comando seguente: 

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

	L'argomento *"-Dmapred.map.tasks=50"* specifica che verranno create 50 funzioni Map per l'esecuzione del processo. L'argomento *"-Dmapred.reduce.tasks=25"* specifica che verranno create 25 attività Reduce per l'esecuzione del processo. Gli ultimi due argomenti specificano le directory di input e di output.  
 

4. Eseguire i comandi seguenti per inviare il processo MapReduce, attendere il completamento del processo e quindi stampare l'errore standard:

		# Run the TeraSort MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job 
		Select-AzureSubscription $subscriptionName 
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 


<h2><a id="java-code"></a>Codice Java per il programma MapReduce TeraSort</h2>

Il codice per il programma MapReduce TeraSort viene presentato per la verifica in questa sezione. 


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one	
	 * or more contributor license agreements.  See the NOTICE file	
	 * distributed with this work for additional information	
	 * regarding copyright ownership.  The ASF licenses this file	
	 * to you under the Apache License, Version 2.0 (the	
	 * "License"); you may not use this file except in compliance	
	 * with the License.  You may obtain a copy of the License at	
	 *	
	 *     http://www.apache.org/licenses/LICENSE-2.0	
	 *	
	 * Unless required by applicable law or agreed to in writing, software	
	 * distributed under the License is distributed on an "AS IS" BASIS,	
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.	
	 * See the License for the specific language governing permissions and	
	 * limitations under the License.	
	 */	
	
	package org.apache.hadoop.examples.terasort;
	
	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;
	
	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;
	
	/**	
	 * Generates the sampled split points, launches the job, 	
	 * and waits for it to finish. 	
	 * <p>
	 * To run the program: 	
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>	
	 */	
	
	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);
	
	  /**	
	   * A partitioner that splits text keys into roughly equal 	
	   * partitions in a global sorted order.	
	   */	
	
	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;
	
	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }
	
	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];
	      
	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }
	
	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }
	
	
	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p, 
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }
	
	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }
	
	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }
	
	    public TotalOrderPartitioner() {
	    }
	
	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }
	    
	  }
	  
	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }
	
	  /**
	   * @param args
	   */
	
	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }
	
	}
  

<h2><a id="summary"></a>Riepilogo</h2>

In questo esempio è stato illustrato come eseguire una serie di processi MapReduce usando Azure HDInsight, in cui l'output di un processo viene usato come input per il processo successivo della serie.

<h2><a id="next-steps"></a>Passaggi successivi</h2>

Per le esercitazioni relative all'esecuzione di altri esempi in cui vengono fornite istruzioni sull'uso di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Esempio: Calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio: Conteggio parole][hdinsight-sample-wordcount]
* [Esempio: streaming C#][hdinsight-sample-csharp-streaming]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx


[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/



<!--HONumber=35.1-->
