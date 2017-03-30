---
title: Compilare un&quot;applicazione Java HBase per Azure HDInsight basato su Windows | Microsoft Docs
description: Informazioni su come usare Apache Maven per compilare un&quot;applicazione Apache HBase basata su Java e poi distribuirla in un cluster Azure HDInsight basato su Windows.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e7a2fef8860984c2e4e3749202868940b836ca9f
ms.lasthandoff: 03/17/2017


---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Usare Maven per compilare applicazioni Java che usano HBase con HDInsight basato su Windows (Hadoop)
In questo articolo si apprenderà come creare e compilare un'applicazione [Apache HBase](http://hbase.apache.org/) in Java usando Apache Maven. quindi a usare l'applicazione con HDInsight di Azure (Hadoop).

[Maven](http://maven.apache.org/) è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e report per i progetti Java. In questo articolo si apprende come usarlo per creare un'applicazione Java di base che crea, interroga ed elimina una tabella HBase su un cluster HDInsight di Azure.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="requirements"></a>Requisiti
* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive
* [Maven](http://maven.apache.org/)
* Un cluster HDInsight basato su Windows con HBase

    > [!NOTE] 
    > Le procedure descritte in questo documento sono state testate con le versioni del cluster HDInsight 3.2 e 3.3. I valori predefiniti specificati negli esempi sono per un cluster HDInsight 3.3.

## <a name="create-the-project"></a>Creare il progetto
1. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto, ad esempio `cd code\hdinsight`.
2. Usare il comando **mvn** , che viene installato con Maven, per generare lo scaffolding per il progetto.
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    Questo comando crea una directory nel percorso corrente, con il nome specificato dal parametro **artifactID** (in questo esempio **hbaseapp**). La directory contiene gli elementi seguenti:
   
   * **pom.xml**: il modello a oggetti dei progetti ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.
   * **src**: la directory che contiene la directory **main\java\com\microsoft\examples**, in cui verrà creata l'applicazione.
3. Eliminare il file **src\test\java\com\microsoft\examples\apptest.java** perché non viene usato in questo esempio.

## <a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti dei progetti
1. Modificare il file **pom.xml** e aggiungere il codice seguente nella sezione `<dependencies>`:
   
        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>
   
    Questa sezione indica a Maven che il progetto richiede la versione **1.1.2** di **hbase-client**. In fase di compilazione, questa dipendenza viene scaricata dal repository Maven predefinito. È possibile usare la [ricerca nel repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per ottenere altre informazioni su questa dipendenza.
   
   > [!IMPORTANT]
   > Il numero di versione deve corrispondere alla versione di HBase fornita con il cluster HDInsight. Usare la tabella seguente per trovare il numero di versione corretto.
   > 
   > 
   
   | Versione del cluster HDInsight | Versione di HBase da usare |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |
   
    Per altre informazioni sulle versioni e sui componenti di HDInsight, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md).
2. Se si usa un cluster HDInsight 3.3, è necessario aggiungere anche il codice seguente alla sezione `<dependencies>` :
   
        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
   
    Questa dipendenza caricherà i componenti phoenix-core usati da Hbase versione 1.1.x.
3. Aggiungere il codice seguente al file **pom.xml**. Questa sezione deve essere racchiusa tra tag `<project>...</project>` nel file, ad esempio tra `</dependencies>` e `</project>`.
   
        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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
   
    La sezione `<resources>` configura una risorsa (**conf\hbase-site.xml**) che contiene informazioni di configurazione per HBase.
   
   > [!NOTE]
   > È anche possibile impostare i valori di configurazione tramite codice. Per scoprire come procedere, leggere i commenti nell'esempio **CreateTable** seguente.
   > 
   > 
   
    Questa sezione `<plugins>` configura i [plug-in compiler per Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e i [plug-in shade per Maven](http://maven.apache.org/plugins/maven-shade-plugin/). Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Il motivo per cui viene usato questo plug-in è che i file di licenza duplicati causano un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.
   
    Il plug-in maven-shade-plugin produce anche un file uberjar (o fatjar), che contiene tutte le dipendenze richieste dall'applicazione.
4. Salvare il file **pom.xml** .
5. Creare una nuova directory denominata **conf** nella directory **hbaseapp**. Nella directory **conf** creare un file denominato **hbase-site.xml**. Usare quanto segue come contenuto del file:
   
        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
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
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>
   
    Questo file verrà usato per caricare la configurazione di HBase per un cluster HDInsight.
   
   > [!NOTE]
   > Si tratta di un file hbase-site.xml con contenuto minimo e comprende solo le impostazioni essenziali per il cluster HDInsight.

6. Salvare il file **hbase-site.xml**.

## <a name="create-the-application"></a>Creazione dell'applicazione
1. Passare alla directory **hbaseapp\src\main\java\com\microsoft\examples** e rinominare il file app.java in **CreateTable.java**.
2. Aprire il file **CreateTable.java** e sostituire il contenuto esistente con il codice seguente:
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;
   
        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");
   
            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);
   
            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };
   
            HTable table = new HTable(config, "people");
   
            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }
   
    Si tratta della classe **CreateTable**, che consente di creare una tabella denominata **people** e di popolarla con alcuni utenti predefiniti.
3. Salvare il file **CreateTable.java**.
4. Nella directory **hbaseapp\src\main\java\com\microsoft\examples** creare un nuovo file denominato **SearchByEmail.java**. Usare il codice seguente come contenuto di questo file:
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;
   
        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }
   
            // Open the table
            HTable table = new HTable(config, "people");
   
            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");
   
            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );
   
            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);
   
            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }
   
    È possibile usare la classe **SearchByEmail** per eseguire query sulle righe in base all'indirizzo e-mail. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa la classe.
5. Salvare il file **SearchByEmail.java**.
6. Nella directory **hbaseapp\src\main\hava\com\microsoft\examples** creare un nuovo file denominato **DeleteTable.java**. Usare il codice seguente come contenuto di questo file:
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
   
        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }
   
    Questa classe consente di pulire i dati di questo esempio disabilitando ed eliminando la tabella creata dalla classe **CreateTable**.
7. Salvare il file **DeleteTable.java**.

## <a name="build-and-package-the-application"></a>Compilare e creare il pacchetto dell'applicazione
1. Aprire un prompt dei comandi e passare alla directory **hbaseapp**.
2. Usare il comando seguente per compilare un file JAR contenente l'applicazione:
   
        mvn clean package
   
    In tal modo, vengono eliminati eventuali artefatti di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.
3. Quando il comando viene completato, la directory **hbaseapp\target** contiene un file denominato **hbaseapp-1.0-SNAPSHOT.jar**.
   
   > [!NOTE]
   > Il file **hbaseapp-1.0-SNAPSHOT.jar** è un file uberjar (talvolta chiamato fatjar) che contiene tutte le dipendenze richieste per eseguire l'applicazione.

## <a name="upload-the-jar-file-and-start-a-job"></a>Caricare il file JAR e avviare un processo
Ci sono molti modi per caricare un file nel cluster HDInsight, come descritto nella sezione [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md). La procedura seguente usa Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Dopo aver installato e configurato Azure PowerShell, creare un nuovo file denominato **hbase-runner.psm1**. Usare il codice seguente come contenuto di questo file:
   
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
   
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
   
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
   
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
   
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
   
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
   
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
   
        Set-StrictMode -Version 3
   
        # Is the Azure module installed?
        FindAzure
   
        # Get the login for the HDInsight cluster
        $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"
   
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
   
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
   
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds
        }
   
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
   
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
   
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
   
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
   
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
   
            Set-StrictMode -Version 3
   
            # Is the Azure module installed?
            FindAzure
   
            # Get authentication for the cluster
            $creds=Get-Credential
   
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
   
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
   
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
   
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
   
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
   
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
   
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*
   
    Questo file contiene due moduli:
   
   * **Add-HDInsightFile**: usato per caricare file in HDInsight
   * **Start-HBaseExample**: usato per eseguire le classi create prima
2. Salvare il file **hbase-runner.psm1**.
3. Aprire una nuova finestra di Azure PowerShell, passare alla directory **hbaseapp** e quindi eseguire questo comando.
   
        PS C:\ Import-Module c:\path\to\hbase-runner.psm1
   
    Cambiare il percorso con la posizione del file **hbase-runner.psm1** creato prima. Viene registrato il modulo per questa sessione di Azure PowerShell.
4. Usare il comando seguente per caricare il file **hbaseapp-1.0-SNAPSHOT.jar** nel cluster HDInsight.
   
        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
   
    Sostituire **hdinsightclustername** con il nome del cluster HDInsight. Il comando carica il file **hbaseapp-1.0-SNAPSHOT.jar** nel percorso **example/jars** dell'archivio primario per il cluster HDInsight.
5. Dopo avere caricato i file, usare il codice seguente per creare una tabella con **hbaseapp**:
   
        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
   
    Sostituire **hdinsightclustername** con il nome del cluster HDInsight.
   
    Questo comando consente di creare una nuova tabella denominata **people** nel cluster HDInsight. Non viene mostrato alcun output nella finestra della console.
6. Per cercare le voci nella tabella, usare il comando seguente:
   
        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
   
    Sostituire **hdinsightclustername** con il nome del cluster HDInsight.
   
    Questo comando usa la classe **SearchByEmail** per eseguire la ricerca di eventuali righe in cui la famiglia della colonna **contactinformation** e la colonna **email** contengono la stringa **contoso.com**. Dovrebbero essere visualizzati i risultati seguenti:
   
          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6
   
    L'uso di **fabrikam.com** per il valore `-emailRegex` restituirà gli utenti il cui campo email contiene **fabrikam.com**. Poiché questa ricerca viene implementata usando un filtro basato su un'espressione regolare, è anche possibile immettere espressioni regolari come **^r**, che restituiranno voci in cui email inizia con la lettera "r".

## <a name="delete-the-table"></a>Eliminare la tabella
Dopo aver finito con l'esempio, usare il comando seguente dalla sessione di Azure PowerShell per eliminare la tabella **people** usata in questo esempio:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Sostituire **hdinsightclustername** con il nome del cluster HDInsight.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nessun risultato o risultati imprevisti quando si usa Start-HBaseExample
Usare il parametro `-showErr` per visualizzare l'errore standard (STDERR) prodotto durante l'esecuzione del processo.


