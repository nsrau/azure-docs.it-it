---
title: Funzione definita dall'utente (UDF) Java con Apache Hive Azure HDInsight
description: Informazioni su come creare una funzione definita dall'utente (UDF) basata su Java che funzioni con Apache Hive. In questo esempio, UDF converte una tabella di stringhe di testo in caratteri minuscoli.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327201"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Usare una funzione definita dall'utente Java con Apache Hive in HDInsight

Informazioni su come creare una funzione definita dall'utente (UDF) basata su Java che funzioni con Apache Hive. La UDF Java di questo esempio converte una tabella di stringhe di testo in caratteri minuscoli.

## <a name="prerequisites"></a>prerequisiti

* Un cluster Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.
* Lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria dei cluster. Si tratta di wasb://per archiviazione di Azure, abfs://per Azure Data Lake Storage Gen2 o adl://per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per Archiviazione di Azure, l'URI sarà `wasbs://`.  Vedere anche l'articolo sul [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

* Un editor di testo o ambiente IDE Java

    > [!IMPORTANT]  
    > Se si creano i file Python in un client Windows, è necessario usare un editor che usa LF come terminazione di riga. Se non si è certi se l'editor usa LF o CRLF, vedere la sezione [Risoluzione dei problemi](#troubleshooting) , che include passaggi per la rimozione del carattere CR.

## <a name="test-environment"></a>Ambiente di test

L'ambiente usato per questo articolo è un computer che esegue Windows 10.  I comandi sono stati eseguiti in un prompt dei comandi e i vari file sono stati modificati con blocco note. Modificare di conseguenza per l'ambiente in uso.

Da un prompt dei comandi, immettere i comandi seguenti per creare un ambiente di lavoro:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Creare una UDF Java di esempio

1. Creare un nuovo progetto Maven immettendo il comando seguente:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Questo comando crea una directory denominata `exampleudf`, che contiene il progetto Maven.

2. Una volta creato il progetto, eliminare la directory `exampleudf/src/test` creata come parte del progetto immettendo il comando seguente:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Aprire `pom.xml` immettendo il comando seguente:

    ```cmd
    notepad pom.xml
    ```

    Sostituire quindi la voce di `<dependencies>` esistente con il codice XML seguente:

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

    Queste voci specificano le versioni di Hadoop e Hive incluse in HDInsight 3.6. È possibile trovare informazioni sulle versioni di Hadoop e Hive fornite con HDInsight dal documento relativo al [controllo delle versioni dei componenti di HDInsight](../hdinsight-component-versioning.md) .

    Aggiungere una sezione `<build>` prima della riga `</project>` alla fine del file. Questa sezione deve contenere il seguente XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
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
                <version>3.2.1</version>
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

4. Immettere il comando seguente per creare e aprire un nuovo file `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Quindi copiare e incollare il codice Java riportato di seguito nel nuovo file. Chiudere quindi il file.

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

Nei comandi seguenti sostituire `sshuser` con il nome utente effettivo, se diverso. Sostituire `mycluster` con il nome del cluster effettivo.

1. Compilare e creare il pacchetto della funzione definita dall'utente immettendo il comando seguente:

    ```cmd
    mvn compile package
    ```

    Questo comando compila e impacchetta la UDF nel file `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Usare il comando `scp` per copiare il file nel cluster HDInsight immettendo il comando seguente:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Connettersi al cluster tramite SSH immettendo il comando seguente:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Dalla sessione SSH aperta copiare il file jar nell'archiviazione HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utilizzare la UDF da Hive

1. Avviare il client di controllo della connessione dalla sessione SSH immettendo il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Questo comando presuppone che sia stato utilizzato il valore predefinito di **admin** per l'account di accesso del cluster.

2. Una volta arrivati al prompt `jdbc:hive2://localhost:10001/>` , immettere il comando seguente per aggiungere la UDF a Hive ed esporla come una funzione.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Utilizzare la UDF per convertire i valori recuperati da una tabella in stringhe con lettere minuscole.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Questa query consente di selezionare lo stato della tabella, convertire la stringa in lettere minuscole e quindi visualizzarli insieme al nome non modificato. L'output appare simile al seguente testo:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si esegue il processo hive, è possibile che si verifichi un errore simile al testo seguente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Questo problema potrebbe essere causato dalle terminazioni di riga nel file di Python. Molti editor di Windows usano per impostazione predefinita CRLF come terminazione di riga, mentre le applicazioni Linux prevedono in genere LF.

È possibile usare le istruzioni di PowerShell seguenti per rimuovere i caratteri CR prima di caricare il file in HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre modalità di utilizzo di Apache Hive, vedere [Cosa sono Apache Hive e HiveQL in Azure HDInsight](hdinsight-use-hive.md).

Per altre informazioni sulle UDF di Hive, vedere la sezione [Apache Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Operatori e funzioni definite dall’utente di Apache Hive) del wiki di Hive all’indirizzo apache.org.
