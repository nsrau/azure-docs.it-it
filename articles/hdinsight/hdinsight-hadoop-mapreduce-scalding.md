---
title: Sviluppare processi MapReduce in Scalding con Maven| Microsoft Docs
description: Imparare a usare Maven per creare un processo MapReduce in Scalding, quindi distribuire ed eseguire il processo in un Hadoop nel cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: larryfr

---
# Sviluppare processi MapReduce in Scalding con Apache Hadoop in HDInsight
Scalding è una libreria Scala che semplifica la creazione di processi Hadoop MapReduce. Offre una sintassi concisa, nonché una stretta integrazione con Scala.

In questo documento vengono fornite informazioni sull'uso di Maven per creare un processo MapReduce di conteggio di parole di base scritto in Scalding. Si apprenderà quindi come distribuire ed eseguire questo processo su un cluster HDInsight.

## Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un cluster Hadoop basato su Windows o Linux in HDInsight**. Per altre informazioni vedere l'articolo relativo al [provisioning di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) o relativo al [provisioning di Hadoop basato su Windows in HDInsight](hdinsight-provision-clusters.md).
* **[Maven](http://maven.apache.org/)**
* **[Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive**

## Salvare e compilare il progetto
1. Usare il comando seguente per creare un nuovo progetto Maven.
   
        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false
   
    Questo comando creerà una nuova directory denominata **scaldingwordcount** e creerà lo scaffolding per un'applicazione di Scala.
2. Nella directory **scaldingwordcount**, aprire il file **pom.xml** e sostituire il contenuto con il codice seguente:
   
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>
   
    Questo file descrive il progetto, le dipendenze e i plug-in. Ecco le voci importanti:
   
   * **maven.compiler.source** e **maven.compiler.target**: imposta la versione Java per questo progetto
   * **repository**: i repository che contengono i file di dipendenza usati da questo progetto
   * **scalding-core\_2.11** e **hadoop-core**: questo progetto dipende da pacchetti di base Scalding e Hadoop
   * **maven-scala-plugin**: plug-in per compilare le applicazioni di scala
   * **maven-shade-plugin**: plug-in per creare file JAR (fat) con shade. Questo plug-in consente di applicare filtri e trasformazioni; in particolare:
     
     * **filtri**: i filtri applicati modificano le metainformazioni incluse nel file jar. Per evitare eccezioni di firma in fase di esecuzione sono esclusi vari file di firma che potrebbero essere inclusi con le dipendenze.
     * **esecuzioni**: la configurazione dell’esecuzione della fase pacchetto specifica la classe **com.twitter.scalding.Tool** come classe principale per il pacchetto. Senza questa opzione, è necessario specificare com.twitter.scalding.Tool, nonché la classe contenente la logica dell'applicazione, quando si esegue il processo con il comando di hadoop.
3. Eliminare la directory **src/test**, perché per questo esempio non verranno creati test.
4. Aprire il file **src/main/scala/com/microsoft/example/app.scala** e sostituire il contenuto con il codice seguente:
   
        package com.microsoft.example
   
        import com.twitter.scalding._
   
        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))
   
            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }
   
    Implementa un processo di conteggio di parole di base.
5. Salvare e chiudere i file.
6. Usare il comando seguente dalla directory **scaldingwordcount** per compilare e creare il pacchetto dell'applicazione:
   
        mvn package
   
    Una volta completato questo processo, il pacchetto contenente l'applicazione WordCount è reperibile in **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## Eseguire il processo in un cluster basato su Linux
> [!NOTE]
> La procedura seguente usa SSH e il comando di Hadoop. Per altri metodi di esecuzione di processi MapReduce, vedere [Uso di MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md).
> 
> 

1. Usare il comando seguente per caricare il pacchetto nel cluster HDInsight.
   
        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:
   
    In questo modo i file verranno copiati dal sistema locale nel nodo head.
   
   > [!NOTE]
   > Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata. Ad esempio, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`
   > 
   > 
2. Immettere il comando seguente per connettersi al nodo head del cluster:
   
        ssh username@clustername-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata. Ad esempio, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`
   > 
   > 
3. Dopo aver stabilito la connessione al nodo head, usare questo comando per eseguire il processo di conteggio di parole
   
        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout
   
    Viene eseguita la classe WordCount implementata in precedenza. `--hdfs` indica al processo di usare HDFS. `--input` specifica il file di testo di input, mentre `--output` specifica il percorso di output.
4. Dopo il completamento del processo, usare il comando seguente per visualizzare l'output.
   
        hdfs dfs -text wasbs:///example/wordcountout/*
   
    Verranno visualizzate informazioni simili alle seguenti:
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Eseguire il processo in un cluster basato su Windows
La procedura seguente usa Windows PowerShell. Per altri metodi di esecuzione di processi MapReduce, vedere [Uso di MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md).

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Avviare Azure PowerShell e accedere al proprio account Azure. Una volta specificate le credenziali, il comando restituisce le informazioni relative all'account.
   
        Add-AzureRMAccount
   
        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
2. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione da usare per la distribuzione.
   
        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>
   
   > [!NOTE]
   > È possibile usare `Get-AzureRMSubscription` per ottenere un elenco di tutte le sottoscrizioni associate all'account, con il relativo ID di sottoscrizione.
   > 
   > 
3. utilizzare lo script seguente per caricare ed eseguire il processo WordCount. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight, e verificare che `$fileToUpload` sia il percorso corretto per il file **scaldingwordcount-1.0-SNAPSHOT.jar**.
   
        #Cluster name, file to be uploaded, and where to upload it
        $clustername = "CLUSTERNAME"
        $fileToUpload = "scaldingwordcount-1.0-SNAPSHOT.jar"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"
   
        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
   
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
   
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
   
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                       "--input", `
                       "wasbs:///example/data/gutenberg/davinci.txt", `
                       "--output", `
                       "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError
   
     Quando si esegue lo script, verrà richiesto di immettere il nome utente dell’amministratore e la password per il cluster HDInsight. Eventuali errori che si verificano durante l'esecuzione del processo verranno registrati nella console.
4. Al termine del processo, l'output verrà scaricato nel file **output.txt** nella directory corrente. Eseguire il comando seguente per visualizzare i risultati:
   
        cat output.txt
   
    Il file deve contenere valori simili al seguente:
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Passaggi successivi
Dopo aver appreso come usare Scalding per creare processi MapReduce per HDInsight, vedere i seguenti collegamenti per esplorare altri modi di uso di Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->