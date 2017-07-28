---
title: Creare Java MapReduce per Hadoop - Azure HDInsight | Microsoft Docs
description: Informazioni sull&quot;uso di Apache Maven per creare un&quot;applicazione MapReduce basata su Java, quindi per eseguirla con Hadoop su Azure HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 05/17/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 33705cff8d65b40aeca2cdaf0b102bf7fbbfa9d6
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Sviluppare programmi Java MapReduce per Hadoop in HDInsight

Informazioni sull'uso di Apache Maven per creare un'applicazione MapReduce basata su Java, quindi per eseguirla con Hadoop su Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 o versione successiva (o equivalente, ad esempio OpenJDK)
    
    > [!NOTE]
    > HDInsight versione 3.4 e precedenti usano Java 7. HDInsight 3.5 usa Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

Quando si installa Java e JDK, è possibile impostare le variabili di ambiente indicate di seguito. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME`: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). Ad esempio, in un sistema OS X, Unix o Linux, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`

* `PATH`: deve contenere i percorsi seguenti:
  
  * `JAVA_HOME` (o il percorso equivalente)

  * `JAVA_HOME\bin` (o il percorso equivalente)

  * Directory in cui è installato Maven

## <a name="create-a-maven-project"></a>Creare un progetto Maven

1. Da una sessione terminal o dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto.

2. Usare il comando `mvn`, che viene installato con Maven, per generare lo scaffolding per il progetto.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Questo comando crea una directory con il nome specificato dal parametro `artifactID` (in questo esempio **wordcountjava**). La directory contiene gli elementi seguenti:

   * `pom.xml`: il modello a oggetti dei progetti ([POM o Project Object Model](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) che contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.

   * `src`: la directory che contiene l'applicazione.

3. Eliminare il file `src/test/java/org/apache/hadoop/examples/apptest.java`. Non viene usato in questo esempio.

## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. Modificare il file `pom.xml` e aggiungere il testo seguente nella sezione `<dependencies>`:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Questa operazione definisce le librerie richieste (elencate in &lt;artifactId\>) con una versione specifica (elencata in &lt;version\>). In fase di compilazione, queste dipendenze vengono scaricata dal repository Maven predefinito. È possibile usare la [ricerca nel repository Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) per visualizzare più informazioni.
   
    `<scope>provided</scope>` indica a Maven che queste dipendenze non devono essere fornite con l'applicazione, perché vengono fornite dal cluster HDInsight in fase di esecuzione.

    > [!IMPORTANT]
    > La versione usata deve corrispondere alla versione di Hadoop presente nel cluster. Per altre informazioni sulle versioni, vedere il documento sulle [versioni dei componenti HDInsight](hdinsight-component-versioning.md).

2. Aggiungere il testo seguente al file `pom.xml`. Nel file, il testo deve essere incluso tra i tag `<project>...</project>`, ad esempio tra `</dependencies>` e `</project>`.

   ```xml
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
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    Il primo plug-in configura il [plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/)che viene usato per compilare un file uberjar (detto a volte fatjar), che contiene le dipendenze richieste dall'applicazione. Impedisce anche la duplicazione di licenze all'interno del pacchetto jar, cosa che potrebbe causare problemi in alcuni sistemi.

    Il secondo plug-in consente di configurare la versione Java di destinazione.

    > [!NOTE]
    > HDInsight 3.4 e versioni precedenti usano Java 7. HDInsight 3.5 usa Java 8.

3. Salvare il file.`pom.xml`

## <a name="create-the-mapreduce-application"></a>Creare l'applicazione MapReduce

1. Andare alla directory `wordcountjava/src/main/java/org/apache/hadoop/examples` e rinominare il file `App.java` in `WordCount.java`.

2. Aprire il file `WordCount.java` in un editor di testo e sostituire il contenuto con il testo seguente:
   
   ```java
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
   ```
   
    Si noti che il nome del pacchetto è `org.apache.hadoop.examples` e il nome della classe è `WordCount`. Questi nomi vengono usati per l'invio del processo MapReduce.

3. Salvare il file.

## <a name="build-the-application"></a>Compilare l'applicazione.

1. Passare alla directory `wordcountjava` se non è già visualizzata.

2. Usare il comando seguente per compilare un file JAR contenente l'applicazione:

   ```
   mvn clean package
   ```

    In tal modo, vengono eliminati eventuali elementi di compilazione precedenti, scaricate le dipendenze non ancora installate e quindi compilato e creato il pacchetto dell'applicazione.

3. Al completamento del comando, la directory `wordcountjava/target` contiene un file denominato `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > Il file `wordcountjava-1.0-SNAPSHOT.jar` è un file di tipo uberjar che contiene non solo il processo WordCount, ma anche le dipendenze richieste dal processo durante il runtime.

## <a id="upload"></a>Caricare il file JAR

Usare il comando seguente per caricare il file jar nel nodo head di HDInsight.

   ```bash
   scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Questo comando copia i file dal sistema locale nel nodo head. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Eseguire il processo MapReduce in Hadoop

1. Connettersi a HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Nella sessione SSH usare il comando seguente per eseguire l'applicazione MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Questo comando avvia l'applicazione MapReduce WordCount. Il file di input è `/example/data/gutenberg/davinci.txt` e la directory di output è `/example/data/wordcountout`. I file di input e di output saranno archiviati nell'archiviazione predefinita del cluster.

3. Dopo il completamento del processo, usare il comando seguente per visualizzare i risultati:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Dovrebbe essere visualizzato un elenco di parole e conteggi, con valori simili al testo seguente:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Passaggi successivi

In questo documento si è appreso come sviluppare un processo Java MapReduce. Vedere i documenti seguenti per altre modalità di utilizzo di HDInsight.

* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

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


