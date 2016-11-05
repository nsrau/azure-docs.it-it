---
title: Utilizzare una funzione definita dall'utente (UDF) Java con Hive in HDInsight | Microsoft Docs
description: Informazioni su come creare e utilizzare una funzione definita dall'utente (UDF) Java da Hive in HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr

---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilizzare una UDF Java con Hive in HDInsight
Hive è la soluzione ideale per usare i dati in HDInsight, ma in alcuni casi è necessario un linguaggio più generico. Hive consente di creare funzioni definite dall'utente (UDF) mediante l’uso di una vasta gamma di linguaggi di programmazione. Il presente documento illustra come usare una funzione definita dall'utente Java da Hive.

## <a name="requirements"></a>Requisiti
* Una sottoscrizione di Azure.
* Un cluster HDInsight (Windows o basato su Linux)
  
  > [!NOTE]
  > La maggior parte dei passaggi descritti in questo documento funzionerà per entrambi i tipi di cluster; tuttavia, i passaggi necessari per caricare la funzione definita dall'utente compilata nel cluster ed eseguirla sono specifici per i cluster basati su Linux. Vengono forniti i collegamenti alle informazioni utili per i cluster basati su Windows.
  > 
  > 
* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o versione successiva (o equivalente, ad esempio OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* Un editor di testo o ambiente IDE Java
  
  > [!IMPORTANT]
  > Se si usa un server HDInsight basato su Linux e i file Python vengono creati in un client Windows, è necessario usare un editor che usa LF come terminazione di riga. Se non si è certi se l'editor usa LF o CRLF, vedere la sezione [Risoluzione dei problemi](#troubleshooting) , che include passaggi per la rimozione del carattere CR mediante le utilità nel cluster HDInsight.
  > 
  > 

## <a name="create-an-example-udf"></a>Creare una UDF di esempio
1. Da una riga di comando seguire questa procedura per creare un nuovo progetto Maven.
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > Se si utilizza PowerShell, è necessario racchiudere i parametri tra virgolette. ad esempio `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.
   > 
   > 
   
    In questo modo si crea una nuova directory denominata **exampleudf**, che contiene il progetto Maven.
2. Dopo aver creato il progetto, eliminare la directory **exampleudf/src/test** creata come parte del progetto, poiché non verrà utilizzata in questo esempio.
3. Aprire **exampleudf/pom.xml** e sostituire la voce `<dependencies>` esistente con:
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    Queste voci specificano la versione di Hadoop e Hive inclusa con i cluster HDInsight 3.3 e 3.4. È possibile trovare informazioni sulle versioni di Hadoop e Hive fornite con HDInsight dal documento relativo al [controllo delle versioni dei componenti di HDInsight](hdinsight-component-versioning.md) .
   
    Aggiungere una sezione `<build>` prima della riga `</project>` alla fine del file. Questa sezione deve contenere quanto segue:
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
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
   
    Queste voci definiscono la modalità di realizzazione del progetto. In particolare, la versione di Java usata dal progetto e il modo in cui creare un file uberjar per la distribuzione nel cluster.
   
    Salvare il file dopo avere apportato le modifiche.
4. Rinominare **exampleudf/src/main/java/com/microsoft/examples/App.java** in **ExampleUDF.java** e aprire il file nell'editor.
5. Sostituire i contenuti del file **ExampleUDF.java** con quanto segue, quindi salvare il file.
   
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
   
    Ciò implementa una funzione definita dall'utente che accetta un valore stringa e restituisce una versione in lettere minuscole della stringa.

## <a name="build-and-install-the-udf"></a>Compilare e installare la UDF
1. Eseguire il comando seguente per compilare la UDF e inserirla nel pacchetto:
   
        mvn compile package
   
    Consente di compilare la funzione definita dall'utente e inserirla nel pacchetto **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**.
2. Usare il comando `scp` per copiare il file nel cluster HDInsight.
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    Sostituire **myuser** con l'account utente SSH del cluster. Sostituire **mycluster** con il nome del cluster. Se l'account SSH è protetto da una password, questa dovrà essere immessa. Se è stata usato un certificato, può essere necessario usare il parametro `-i` per specificare il file della chiave privata.
3. Connettersi al cluster tramite SSH. 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti.
   
   * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
4. Dalla sessione SSH, copiare il file jar nell’archiviazione HDInsight.
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Utilizzare la UDF da Hive
1. Per avviare il client Beeline, dalla sessione SSH usare il comando seguente.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Questo comando presuppone che sia stato utilizzato il valore predefinito di **admin** per l'account di accesso del cluster.
2. Una volta arrivati al prompt `jdbc:hive2://localhost:10001/>` , immettere il comando seguente per aggiungere la UDF a Hive ed esporla come una funzione.
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. Utilizzare la UDF per convertire i valori recuperati da una tabella in stringhe con lettere minuscole.
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    Ciò consente di selezionare la piattaforma del dispositivo (Android, Windows, iOS, ecc.) dalla tabella, convertire la stringa in lettere minuscole e visualizzarla. Il risultato sarà simile a quello indicato di seguito.
   
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

<!--HONumber=Oct16_HO2-->


