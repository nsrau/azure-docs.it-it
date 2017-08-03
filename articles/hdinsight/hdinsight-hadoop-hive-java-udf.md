---
title: Funzione definita dall'utente (UDF) Java con Hive in HDInsight - Azure | Documentazione Microsoft
description: Informazioni su come creare una funzione definita dall'utente (UDF) basata su Java che funzioni con Hive. In questo esempio, UDF converte una tabella di stringhe di testo in caratteri minuscoli.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 4092f35b2959cee695a30744e0876bbfdfdfbd2c
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilizzare una UDF Java con Hive in HDInsight

Informazioni su come creare una funzione definita dall'utente (UDF) basata su Java che funzioni con Hive. La UDF Java di questo esempio converte una tabella di stringhe di testo in caratteri minuscoli.

## <a name="requirements"></a>Requisiti

* Un cluster HDInsight 

    > [!IMPORTANT]
    > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

    La maggior parte dei passaggi di questo documento vale per entrambi i cluster basati su Windows e Linux. I passaggi necessari per caricare la funzione definita dall'utente compilata nel cluster ed eseguirla sono tuttavia specifici per i cluster basati su Linux. Vengono forniti i collegamenti alle informazioni utili per i cluster basati su Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 o versione successiva (o equivalente, ad esempio OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Un editor di testo o ambiente IDE Java

    > [!IMPORTANT]
    > Se si creano i file Python in un client Windows, è necessario usare un editor che usa LF come terminazione di riga. Se non si è certi se l'editor usa LF o CRLF, vedere la sezione [Risoluzione dei problemi](#troubleshooting) , che include passaggi per la rimozione del carattere CR.

## <a name="create-an-example-java-udf"></a>Creare una UDF Java di esempio 

1. Da una riga di comando seguire questa procedura per creare un nuovo progetto Maven.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Se si utilizza PowerShell, è necessario racchiudere i parametri tra virgolette. Ad esempio: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Questo comando crea una directory denominata **exampleudf**, che contiene un progetto Maven.

2. Dopo aver creato il progetto, eliminare la directory **exampleudf/src/test** creata nell'ambito del progetto.

3. Aprire **exampleudf/pom.xml** e sostituire la voce `<dependencies>` esistente con il seguente XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Queste voci specificano le versioni di Hadoop e Hive incluse con HDInsight 3.5. È possibile trovare informazioni sulle versioni di Hadoop e Hive fornite con HDInsight dal documento relativo al [controllo delle versioni dei componenti di HDInsight](hdinsight-component-versioning.md) .

    Aggiungere una sezione `<build>` prima della riga `</project>` alla fine del file. Questa sezione deve contenere il seguente XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
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
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Queste voci definiscono la modalità di realizzazione del progetto. In particolare, la versione di Java usata dal progetto e il modo in cui creare un file uberjar per la distribuzione nel cluster.

    Salvare il file dopo avere apportato le modifiche.

4. Rinominare **exampleudf/src/main/java/com/microsoft/examples/App.java** in **ExampleUDF.java** e aprire il file nell'editor.

5. Sostituire i contenuti del file **ExampleUDF.java** con quanto segue, quindi salvare il file.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Questo codice implementa una funzione definita dall'utente che accetta un valore stringa e restituisce una versione in lettere minuscole della stringa.

## <a name="build-and-install-the-udf"></a>Compilare e installare la UDF

1. Eseguire il comando seguente per compilare la UDF e inserirla nel pacchetto:

    ```bash
    mvn compile package
    ```

    Questo comando compila e impacchetta la UDF nel file `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Usare il comando `scp` per copiare il file nel cluster HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Sostituire `myuser` con l'account utente SSH del cluster. Sostituire `mycluster` con il nome del cluster. Se l'account SSH è protetto da una password, viene richiesto di immetterla. Se è stata usato un certificato, può essere necessario usare il parametro `-i` per specificare il file della chiave privata.

3. Connettersi al cluster tramite SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

4. Dalla sessione SSH, copiare il file jar nell’archiviazione HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utilizzare la UDF da Hive

1. Per avviare il client Beeline, dalla sessione SSH usare il comando seguente.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Questo comando presuppone che sia stato utilizzato il valore predefinito di **admin** per l'account di accesso del cluster.

2. Una volta arrivati al prompt `jdbc:hive2://localhost:10001/>` , immettere il comando seguente per aggiungere la UDF a Hive ed esporla come una funzione.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Questo esempio assume che Archiviazione di Azure sia la risorsa di archiviazione predefinita per il cluster. Se il cluster usa invece Data Lake Store, modificare il valore `wasbs:///` in `adl:///`.

3. Utilizzare la UDF per convertire i valori recuperati da una tabella in stringhe con lettere minuscole.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Questa query seleziona la piattaforma del dispositivo (Android, Windows, iOS e così via) dalla tabella, converte la stringa in lettere minuscole e la visualizza. L'output appare simile al seguente testo:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Passaggi successivi

Per apprendere altri modi di utilizzare Hive, vedere [Utilizzare Hive con HDInsight](hdinsight-use-hive.md).

Per ulteriori informazioni sulle UDF di Hive, vedere la sezione [Operatori e funzioni definite dall’utente di Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) del wiki di Hive all’indirizzo apache.org.

