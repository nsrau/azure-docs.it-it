<properties linkid="manage-services-hdinsight-sample-pi-estimator" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight Pi estimator sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight Pi estimator sample" authors="bradsev" />

Esempio di calcolo del Pi greco con HDInsight
=============================================

In questo argomento viene illustrato come eseguire un semplice programma MapReduce per il calcolo del valore della costante matematica Pi greco con Azure HDinsight tramite Azure Powershell.

Il programma utilizza un metodo statistico (simile al metodo Monte Carlo) per calcolare il valore di Pi greco. I punti posizionati in modo casuale all’interno di un quadrato unitario rientrano anche in un cerchio inscritto in tale quadrato con una probabilità pari all’area del cerchio, Pi/4. Il valore di Pi greco può essere calcolato in base al valore di 4R, dove R indica il rapporto tra il numero di punti che si trovano all’interno del cerchio e il numero totale di punti che si trovano all’interno del quadrato. La precisione del calcolo è direttamente proporzionale al numero di punti utilizzati.

Il codice Java PiEstimator, contenente le funzioni per il mapping e per la riduzione, è disponibile di seguito per l'analisi. Il programma per il mapping consente di generare un numero specifico di punti posizionati in modo casuale all'interno di un quadrato unitario e quindi di contare il numero di tali punti inclusi all'interno del cerchio. Il programma per la riduzione consente di accumulare i punti contati dalle funzioni di mapping e quindi di calcolare il valore di Pi greco in base alla formula 4R, dove R indica il rapporto tra il numero di punti che si trovano all’interno del cerchio e il numero totale di punti che si trovano all’interno del quadrato.

Lo script fornito per questo esempio invia un processo JAR Hadoop ed è configurato per l'esecuzione con un valore pari a 16 mappe, ognuna delle quali è necessaria per il calcolo di 10 milioni di punti campione da parte dei valori del parametro. È possibile modificare tali valori di parametro per ottimizzare il valore stimato di Pi greco. A scopo di riferimento, si noti che le prime 10 cifre decimali di Pi greco sono 3,1415926535.

Il file con estensione jar contenente i file necessari a Hadoop su Azure per la distribuzione dell'applicazione è un file con estensione zip ed è disponibile per il download. È possibile decomprimerlo utilizzando diverse utilità di compressione e quindi esaminare i file quando lo si desidera.

Gli altri esempi disponibili per imparare rapidamente a utilizzare HDInsight per l'esecuzione di processi MapReduce sono elencati in [Esecuzione degli esempi di HDInsight](/en-us/manage/services/hdinsight/howto-run-samples), insieme a collegamenti a istruzioni relative all'esecuzione di tali processi.

**Si acquisiranno le nozioni seguenti:**

-   Come utilizzare Azure PowerShell per eseguire il programma MapReduce per il calcolo del Pi greco su Azure HDInsight.
-   Aspetto di un programma MapReduce in Java.

**Prerequisiti**:

-   È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://www.windowsazure.com/it-it/pricing/free-trial/).

-   È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   È necessario che Azure PowerShell sia installato e configurato per l'utilizzo con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

Contenuto dell'articolo
-----------------------

In questo argomento viene illustrato come eseguire l'esempio, viene presentato il codice Java per il programma MapReduce per il calcolo del Pi greco, vengono riepilogati i concetti appresi e vengono indicati alcuni passaggi successivi. L'argomento include le sezioni seguenti:

1.  [Esecuzione dell'esempio con Azure PowerShell](#run-sample)
2.  [Codice Java per il programma MapReduce per il calcolo del Pi greco](#java-code)
3.  [Riepilogo](#summary)
4.  [Passaggi successivi](#next-steps)

Esecuzione dell'esempio con Azure PowerShell
--------------------------------------------

**Per inviare il processo MapReduce**

1.  Aprire Azure PowerShell. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).
2.  Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:

         $subscriptionName = "<SubscriptionName>"   # Azure subscription name
         $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

         $piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

    > [WACOM.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.

    Il primo argomento indica il numero di mappe da creare. Il valore predefinito è 16. Il secondo argomento indica il numero di esempi generati per ogni mappa. Il valore predefinito è 10 milioni. Questo programma utilizza pertanto 16\*10 milioni = 160 milioni di punti casuali per il calcolo del Pi greco.

4.  Eseguire i comandi seguenti per inviare il processo MapReduce e attendere il completamento del processo:

         # Run the Pi Estimator MapReduce job.
         Select-AzureSubscription $subscriptionName
         $piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

         # Wait for the job to complete.  
         $piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5.  Eseguire il comando seguente per recuperare l'output standard del processo MapReduce:

         # Print output and standard error file of the MapReduce job
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

    A scopo di confronto, si noti che le prime 10 cifre decimali di Pi greco sono 3,1415926535.

Codice Java per il programma MapReduce per il calcolo del Pi greco
------------------------------------------------------------------

 	/**	
 	* Licensed to the Apache Software Foundation (ASF) under one	
 	* or more contributor license agreements. See the NOTICE file	
 	* distributed with this work for additional information	
 	* regarding copyright ownership. The ASF licenses this file	
 	* to you under the Apache License, Version 2.0 (the	
 	* "License"); you may not use this file except in compliance	
 	* with the License. You may obtain a copy of the License at	
 	*
	* http://www.apache.org/licenses/LICENSE-2.0	
 	*	
 	* Unless required by applicable law or agreed to in writing, software	
 	* distributed under the License is distributed on an "AS IS" BASIS,	
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and	
 	* limitations under the License.		
 	*/	

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi   
    //using quasi-Monte Carlo method.   
    //  
    //Mapper:   
    //Generate points in a unit square  
    //and then count points inside/outside of the inscribed circle of the square.   
    //  
    //Reducer:  
    //Accumulate points inside/outside results from the mappers.    
    //Let numTotal = numInside + numOutside.    
    //The fraction numInside/numTotal is a rational approximation of    
    //the value (Area of the circle)/(Area of the square),  
    //where the area of the inscribed circle is Pi/4    
    //and the area of unit square is 1. 
    //Then, Pi is estimated value to be 4(numInside/numTotal).  
    //  

	public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output 
	static private final Path TMP\_DIR = new Path( PiEstimator.class.getSimpleName() + "\_TMP\_3\_141592654");

    //2-dimensional Halton sequence {H(i)},      
    //where H(i) is a 2-dimensional point and i >= 1 is the index.       
    //Halton sequence is used to generate sample points for Pi estimation.   
	private static class HaltonSequence {
    // Bases 
	static final int[] P = {2, 3};
    //Maximum number of digits allowed   
	static final int[] K = {63, 40}; 

	private long index;
	private double[] x; 
	private double[][] q; 
	private int[][] d;

	//Initialize to H(startindex),	
	//so the sequence begins with H(startindex+1).	
 	HaltonSequence(long startindex) {	
 	index = startindex;	
 	x = new double[K.length];	
 	q = new double[K.length][];	
 	d = new int[K.length][];	
 	for(int i = 0; i < K.length; i++) {	
 	q[i] = new double[K[i]];	
 	d[i] = new int[K[i]];	
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;
	
 	for(int j = 0; j < K[i]; j++) {	
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];	
 	d[i][j] = (int)(k % P[i]);	
 	k = (k - d[i][j])/P[i];	
 	x[i] += d[i][j] * q[i][j];	
 	}	
 	}
 	}

	//Compute next point.	
	//Assume the current point is H(index).	
	//Compute H(index+1).	
	//@return a 2-dimensional point with coordinates in [0,1)^2		
 	double[] nextPoint() {		
 	index++;	
 	for(int i = 0; i < K.length; i++) {	
 	for(int j = 0; j < K[i]; j++) {	
 	d[i][j]++;	
 	x[i] += q[i][j];	
 	if (d[i][j] < P[i]) {	
 	break;		
 	}	
 	d[i][j] = 0;	
 	x[i] -= (j == 0? 1.0: q[i][j-1]);	
 	}	
 	}	
 	return x;	
 	}	
 	}

    //Mapper class for Pi estimation.    
    //Generate points in a unit square and then     
    //count points inside/outside of the inscribed circle of the square.    

	public static class PiMapper extends MapReduceBase implements Mapper&lt;LongWritable, LongWritable, BooleanWritable, LongWritable\> {

    //Map method.    
    //@param offset samples starting from the (offset+1)th sample.  
    //@param size the number of samples for this map    
    //@param out output {ture->numInside, false->numOutside}  
    //@param reporter   
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());		
 	long numInside = 0L;	
 	long numOutside = 0L;	

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.		
	//Accumulate points inside/outside results from the mappers.		
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.	
 	@Override	
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.		
	// @param isInside Is the points inside? 	
	// @param values An iterator to a list of point counts	
	// @param output dummy, not used here.	
	// @param reporter	

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}
	
 	//Reduce task done, write output to a file.	
 	@Override	
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class, 
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}
	
	//Run a map/reduce job for estimating Pi.	
	//@return the estimated value of Pi.		
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	) 	
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());
	
 	jobConf.setInputFormat(SequenceFileInputFormat.class);
	
 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);
	
 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);
	
 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);
	
 	// turn off speculative execution, because DFS doesn't handle		
 	// multiple writers to the same file.		
 	jobConf.setSpeculativeExecution(false);
	
 	//setup input/output directories	
 	final Path inDir = new Path(TMP_DIR, "in");	
 	final Path outDir = new Path(TMP_DIR, "out");	
 	FileInputFormat.setInputPaths(jobConf, inDir);	
 	FileOutputFormat.setOutputPath(jobConf, outDir);	
	
 	final FileSystem fs = FileSystem.get(jobConf);		
 	if (fs.exists(TMP_DIR)) {	
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");	
	 }	
	 if (!fs.mkdirs(inDir)) {	
	 throw new IOException("Cannot create input directory " + inDir);	
	 }	

	 //generate an input file for each map task		
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);	
	 final LongWritable offset = new LongWritable(i * numPoints);	
	 final LongWritable size = new LongWritable(numPoints);	
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();	
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }
	
	 //start a map/reduce job		
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");
	
	 //read outputs		
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }
	
	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }
	
	//Parse arguments and then runs a map/reduce job.	
	//Print output in standard out.		
	//@return a non-zero if there is an error. Otherwise, return 0. 	
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);
	
	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);
	
	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }
	
	 //main method for running it as a stand alone command. 		
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
  
Riepilogo
---------

In questa esercitazione è stato illustrato come eseguire un processo MapReduce su HDInsight e come utilizzare metodi Monte Carlo che richiedono e generano set di dati di grandi dimensioni, che possono essere gestiti da questo servizio.

Passaggi successivi
-------------------

Per le esercitazioni relative all'esecuzione di altri esempi in cui vengono fornite istruzioni sull'utilizzo di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Esempio: GraySort da 10 GB](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Esempio: Conteggio parole](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Esempio: streaming C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

