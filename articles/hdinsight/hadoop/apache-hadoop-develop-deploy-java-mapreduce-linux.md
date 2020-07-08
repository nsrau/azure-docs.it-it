---
title: Creare Java MapReduce per Apache Hadoop - Azure HDInsight
description: Informazioni sull'uso di Apache Maven per creare un'applicazione MapReduce basata su Java, quindi per eseguirla con Hadoop su Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: 415ffcbef9afbeb60764227d29164af2b06348e1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086756"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Sviluppare programmi Java MapReduce per Apache Hadoop in HDInsight

Informazioni sull'uso di Apache Maven per creare un'applicazione MapReduce basata su Java, quindi per eseguirla con Apache Hadoop su Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

L'ambiente usato per questo articolo è un computer che esegue Windows 10. I comandi sono stati eseguiti in un prompt dei comandi e i vari file sono stati modificati con blocco note. Modificare di conseguenza per l'ambiente in uso.

Da un prompt dei comandi, immettere i comandi seguenti per creare un ambiente di lavoro:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Creare un progetto Maven

1. Immettere il comando seguente per creare un progetto Maven denominato **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Questo comando crea una directory con il nome specificato dal `artifactID` parametro (**wordcountjava** in questo esempio). Questa directory contiene gli elementi seguenti:

    * `pom.xml`: il modello a oggetti dei progetti ([POM o Project Object Model](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) che contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.
    * src\main\java\org\apache\hadoop\examples: contiene il codice dell'applicazione.
    * src\test\java\org\apache\hadoop\examples: contiene i test per l'applicazione.

1. Rimuovere il codice di esempio generato. Eliminare i file di test e dell'applicazione generati `AppTest.java` e `App.java` immettendo i comandi seguenti:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti dei progetti

Per informazioni di riferimento complete sul file pom.xml, vedere https://maven.apache.org/pom.html. Aprire `pom.xml` immettendo il comando seguente:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Aggiungere le dipendenze

In `pom.xml` aggiungere il testo seguente nella `<dependencies>` sezione:

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

Questa operazione definisce le librerie richieste (elencate in &lt;artifactId\>) con una versione specifica (elencata in &lt;version\>). In fase di compilazione, queste dipendenze vengono scaricata dal repository Maven predefinito. È possibile usare la [ricerca nel repository Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) per visualizzare più informazioni.

`<scope>provided</scope>` indica a Maven che queste dipendenze non devono essere fornite con l'applicazione, perché vengono fornite dal cluster HDInsight in fase di esecuzione.

> [!IMPORTANT]
> La versione usata deve corrispondere alla versione di Hadoop presente nel cluster. Per altre informazioni sulle versioni, vedere il documento sulle [versioni dei componenti HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Configurare la compilazione

I plug-in di Maven consentono di personalizzare le fasi di compilazione del progetto. Questa sezione viene usata per aggiungere plug-in, risorse e altre opzioni di configurazione della compilazione.

Aggiungere il codice seguente al `pom.xml` file, quindi salvare e chiudere il file. Nel file, il testo deve essere incluso tra i tag `<project>...</project>`, ad esempio tra `</dependencies>` e `</project>`.

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

Questa sezione Configura il plug-in Apache Maven Compiler e il plug-in shader per Apache Maven. Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Questo plug-in viene usato per evitare che i file di licenza duplicati causino un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

Il plug-in maven-shade-plugin produce anche un file uberjar, che contiene tutte le dipendenze richieste dall'applicazione.

Salvare il file `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Creare l'applicazione MapReduce

1. Immettere il comando seguente per creare e aprire un nuovo file `WordCount.java` . Selezionare **Sì** quando viene richiesto di creare un nuovo file.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Quindi copiare e incollare il codice Java riportato di seguito nel nuovo file. Chiudere quindi il file.

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

## <a name="build-and-package-the-application"></a>Compilare e creare il pacchetto dell'applicazione

Dalla directory `wordcountjava` usare il comando seguente per compilare un file JAR contenente l'applicazione:

```cmd
mvn clean package
```

In tal modo, vengono eliminati eventuali elementi di compilazione precedenti, scaricate le dipendenze non ancora installate e quindi compilato e creato il pacchetto dell'applicazione.

Al completamento del comando, la directory `wordcountjava/target` contiene un file denominato `wordcountjava-1.0-SNAPSHOT.jar`.

> [!NOTE]
> Il file `wordcountjava-1.0-SNAPSHOT.jar` è un file di tipo uberjar che contiene non solo il processo WordCount, ma anche le dipendenze richieste dal processo durante il runtime.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Caricare il file JAR ed eseguire i processi (SSH)

La procedura seguente usa `scp` per copiare il file JAR nel nodo head primario di Apache HBase nel cluster HDInsight. Il comando `ssh` viene quindi usato per connettersi al cluster ed eseguire l'esempio direttamente nel nodo head.

1. Caricare il file jar nel cluster. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight e quindi immettere il comando seguente:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Connettersi al cluster. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight e quindi immettere il comando seguente:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nella sessione SSH usare il comando seguente per eseguire l'applicazione MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Questo comando avvia l'applicazione MapReduce WordCount. Il file di input è `/example/data/gutenberg/davinci.txt` e la directory di output è `/example/data/wordcountout`. I file di input e di output saranno archiviati nell'archiviazione predefinita del cluster.

1. Dopo il completamento del processo, usare il comando seguente per visualizzare i risultati:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Dovrebbe essere visualizzato un elenco di parole e conteggi, con valori simili al testo seguente:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come sviluppare un processo Java MapReduce. Vedere i documenti seguenti per altre modalità di utilizzo di HDInsight.

* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
* [Centro per sviluppatori Java](https://azure.microsoft.com/develop/java/)
