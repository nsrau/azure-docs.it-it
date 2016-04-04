<properties
	pageTitle="Sviluppare programmi MapReduce Java per HDInsight basato su Linux | Microsoft Azure"
	description="Informazioni su come sviluppare programmi MapReduce Java e distribuirli in HDInsight basati su Linux."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Sviluppare programmi MapReduce Java per Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

Questo documento illustra l'uso di Apache Maven per creare un'applicazione di MapReduce, quindi per distribuirla ed eseguirla in un Hadoop basato su Linux nel cluster HDInsight. Per informazioni sull'utilizzo di un Hadoop basato su Windows nel cluster HDInsight, vedere [Sviluppare programmi MapReduce Java per Hadoop in HDInsight (Windows)](hdinsight-develop-deploy-java-mapreduce.md)

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o versione successiva (o equivalente, ad esempio OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Una sottoscrizione di Azure**

- **Interfaccia della riga di comando di Azure**: per altre informazioni, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

##Configurare le variabili di ambiente

Quando si installa Java e JDK, è possibile impostare le variabili di ambiente indicate di seguito. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA\_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). Ad esempio, in un sistema OS X, Unix o Linux, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: deve contenere i percorsi seguenti:

	* **JAVA\_HOME** o il percorso equivalente

	* **JAVA\_HOME\\bin** o il percorso equivalente

	* Directory in cui è installato Maven

##Creare un nuovo progetto Maven

1. Da una sessione terminal o dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto.

3. Usare il comando __mvn__, che viene installato con Maven, per generare lo scaffolding per il progetto.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Verrà creata una nuova directory nella directory corrente, con il nome specificato dal parametro __artifactID__ (in questo esempio, **wordcountjava**), che conterrà gli elementi seguenti:

	* __pom.xml__: il modello a oggetti dei progetti ([POM o Project Object Model](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) che contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.

	* __src__: la directory che contiene la directory __main/java/org/apache/hadoop/examples__, in cui verrà creata l'applicazione.

3. Eliminare il file __src/test/java/org/apache/hadoop/examples/apptest.java__, perché non verrà usato in questo esempio.

##Aggiungere le dipendenze

1. Modificare il file __pom.xml__ e aggiungere il codice seguente nella sezione `<dependencies>`:

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	Questo codice indica a Maven che il progetto richiede le librerie (elencate in &lt;artifactId>) con una versione specifica (elencata in &lt;version>). In fase di compilazione, quest'ultimo verrà scaricato dal repository Maven predefinito. È possibile usare la [ricerca nel repository Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) per visualizzare più informazioni.

	`<scope>provided</scope>` indica a Maven che queste dipendenze non devono essere fornite con l'applicazione, perché saranno fornite dal cluster HDInsight in fase di esecuzione.

2. Aggiungere il codice seguente al file __pom.xml__. Il codice deve essere racchiuso tra tag `<project>...</project>` nel file, ad esempio tra `</dependencies>` e `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	Il primo plug-in configura il [plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/) che viene usato per compilare un file uberjar (detto a volte fatjar), che contiene le dipendenze richieste dall'applicazione. Impedisce anche la duplicazione di licenze all'interno del pacchetto jar, cosa che potrebbe causare problemi in alcuni sistemi.

	Il secondo plug-in configura il compilatore Maven, usato per impostare la versione di Java richiesta dall'applicazione per la versione usata nel cluster HDInsight.

3. Salvare il file __pom.xml__.

##Creare l'applicazione MapReduce

1. Passare alla directory __wordcountjava/src/main/java/org/apache/hadoop/examples__ e rinominare il file __App.java__ in __WordCount.java__.

2. Aprire il file __WordCount.java__ in un editor di testo e sostituire il contenuto con il seguente:

		package org.apache.hadoop.examples;

		import java.io.IOException;
		import java.util.StringTokenizer;
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
		import org.apache.hadoop.mapreduce.Mapper;
		import org.apache.hadoop.mapreduce.Reducer;
		import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
		import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class WordCount {

		  public static class TokenizerMapper
		       extends Mapper<Object, Text, Text, IntWritable>{

		    private final static IntWritable one = new IntWritable(1);
		    private Text word = new Text();

		    public void map(Object key, Text value, Context context
		                    ) throws IOException, InterruptedException {
		      StringTokenizer itr = new StringTokenizer(value.toString());
		      while (itr.hasMoreTokens()) {
		        word.set(itr.nextToken());
		        context.write(word, one);
		      }
		    }
		  }

		  public static class IntSumReducer
		       extends Reducer<Text,IntWritable,Text,IntWritable> {
		    private IntWritable result = new IntWritable();

		    public void reduce(Text key, Iterable<IntWritable> values,
		                       Context context
		                       ) throws IOException, InterruptedException {
		      int sum = 0;
		      for (IntWritable val : values) {
		        sum += val.get();
		      }
		      result.set(sum);
		      context.write(key, result);
		    }
		  }

		  public static void main(String[] args) throws Exception {
		    Configuration conf = new Configuration();
		    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		    if (otherArgs.length != 2) {
		      System.err.println("Usage: wordcount <in> <out>");
		      System.exit(2);
		    }
		    Job job = new Job(conf, "word count");
		    job.setJarByClass(WordCount.class);
		    job.setMapperClass(TokenizerMapper.class);
		    job.setCombinerClass(IntSumReducer.class);
		    job.setReducerClass(IntSumReducer.class);
		    job.setOutputKeyClass(Text.class);
		    job.setOutputValueClass(IntWritable.class);
		    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		    System.exit(job.waitForCompletion(true) ? 0 : 1);
		  }
		}

	Si noti che il nome del pacchetto è **org.apache.hadoop.examples** e il nome della classe è **WordCount**. Questi nomi verranno usati per l'invio del processo MapReduce.

3. Salvare il file.

##Compilare l'applicazione.

1. Passare alla directory __wordcountjava__ se non è già visualizzata.

2. Usare il comando seguente per compilare un file JAR contenente l'applicazione:

		mvn clean package

	In questo modo verranno eliminati gli eventuali elementi di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.

3. Al termine dell'esecuzione del comando la directory __wordcountjava/target__ conterrà un file denominato __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Il file __wordcountjava 1.0-SNAPSHOT.jar__ è un file di tipo uberjar che contiene non solo il processo WordCount, ma anche le dipendenze richieste dal processo in fase di esecuzione.


##<a id="upload"></a>Caricare il file JAR

Usare il comando seguente per caricare il file jar nel nodo head di HDInsight.

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

In questo modo i file verranno copiati dal sistema locale nel nodo head.

> [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata. Ad esempio: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Eseguire il processo MapReduce

1. Connettersi a HDInsight con il protocollo SSH come descritto negli articoli seguenti:

    - [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Nella sessione SSH usare il comando seguente per eseguire l'applicazione MapReduce:

		yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

	L'applicazione WordCount MapReduce verrà usata per contare le parole contenute nel file davinci.txt e archiviare i risultati in \_\___wasb:///example/data/wordcountout__. I file di input e di output saranno archiviati nell'archiviazione predefinita del cluster.

3. Dopo il completamento del processo, usare il comando seguente per visualizzare i risultati:

		hdfs dfs -cat wasb:///example/data/wordcountout/*

	Dovrebbe essere visualizzato un elenco di parole e conteggi, con valori simili ai seguenti:

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>Passaggi successivi

In questo documento si è appreso come sviluppare un processo Java MapReduce. Vedere i documenti seguenti per altre modalità di utilizzo di HDInsight.

- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni, vedere anche il [Centro per sviluppatori Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

<!---HONumber=AcomDC_0323_2016-->