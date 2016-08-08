<properties
pageTitle="Creare un'applicazione HBase tramite Maven e distribuire in HDInsight basato su Windows | Microsoft Azure"
description="Informazioni su come usare Apache Maven per compilare un'applicazione Apache HBase basata su Java e poi distribuirla in un cluster Azure HDInsight basato su Windows."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="07/25/2016"
ms.author="larryfr"/>

#Usare Maven per compilare applicazioni Java che usano HBase con HDInsight basato su Windows (Hadoop)

In questo articolo si apprenderà come creare e compilare un'applicazione [Apache HBase](http://hbase.apache.org/) in Java usando Apache Maven, quindi a usare l'applicazione con HDInsight di Azure (Hadoop).

[Maven](http://maven.apache.org/) è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e report per i progetti Java. In questo articolo si apprenderà come usarlo per creare un'applicazione Java di base che crea, interroga ed elimina una tabella HBase su un cluster HDInsight di Azure.

> [AZURE.NOTE] Le procedure in questo documento presuppongono l'uso di un cluster HDInsight basato su Windows. Per informazioni sull'uso di un cluster HDInsight basato su Linux, vedere [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight basato su Linux (Hadoop)](hdinsight-hbase-build-java-maven-linux.md)

##Requisiti

* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive

* [Maven](http://maven.apache.org/)

* [Un cluster HDInsight basato su Windows con HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

    > [AZURE.NOTE] Le procedure descritte in questo documento sono state testate con le versioni del cluster HDInsight 3.2 e 3.3. I valori predefiniti specificati negli esempi sono per un cluster HDInsight 3.3.

##Creare il progetto

1. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto, ad esempio `cd code\hdinsight`.

2. Usare il comando __mvn__, che viene installato con Maven, per generare lo scaffolding per il progetto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Viene creata una nuova directory nella directory corrente, con il nome specificato dal parametro __artifactID__ (in questo esempio, **hbaseapp**). che conterrà gli elementi seguenti:

    * __pom.xml__: il modello a oggetti dei progetti ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.

    * __src__: la directory che contiene la directory __main\\java\\com\\microsoft\\examples__, in cui verrà creata l'applicazione.

3. Eliminare il file __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, perché non verrà usato in questo esempio.

##Aggiornare il modello a oggetti dei progetti

1. Modificare il file __pom.xml__ aggiungere il codice seguente nella sezione `<dependencies>`.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Ciò indica a Maven che il progetto richiedere la versione __1.1.2__ di __hbase-client__. In fase di compilazione, quest'ultimo verrà scaricato dal repository Maven predefinito. È possibile usare la [ricerca nel repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per ottenere altre informazioni su questa dipendenza.

    > [AZURE.IMPORTANT] Il numero di versione deve corrispondere alla versione di HBase fornita con il cluster HDInsight. Usare la tabella seguente per trovare il numero di versione corretto.

    | Versione del cluster HDInsight | Versione di HBase da usare |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 | 1\.1.2 |

    Per altre informazioni sulle versioni e sui componenti di HDInsight, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md).

2. Se si usa un cluster HDInsight 3.3, è necessario aggiungere anche il codice seguente alla sezione `<dependencies>`:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Verranno caricati i componenti phoenix-core usati da Hbase versione 1.1.x.

2. Aggiungere il codice seguente al file __pom.xml__. Il codice deve essere incluso tra tag `<project>...</project>` nel file, ad esempio tra `</dependencies>` e `</project>`.

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

    In tal modo verrà configurata una risorsa (__conf\\hbase-site.xml__,) che contiene informazioni di configurazione per HBase.

    > [AZURE.NOTE] È anche possibile impostare i valori di configurazione tramite codice. Per scoprire come procedere, leggere i commenti nell'esempio __CreateTable__ seguente.

    Verranno inoltre configurati i [plug-in compiler per Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e [i plug-in shade per Maven](http://maven.apache.org/plugins/maven-shade-plugin/). Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Il motivo per cui viene usato questo plug-in è che i file di licenza duplicati causano un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

    Il plug-in maven-shade-plugin produce anche un file uberjar (o fatjar), che contiene tutte le dipendenze richieste dall'applicazione.

3. Salvare il file __pom.xml__.

4. Creare una nuova directory denominata __conf__ nella directory __hbaseapp__. Nella directory __conf__, creare un nuovo file denominato __hbase-site.xml__ e usare il codice seguente come contenuto:

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

    > [AZURE.NOTE] Si tratta di un file hbase-site.xml con contenuto minimo e comprende solo le impostazioni essenziali per il cluster HDInsight.

3. Salvare il file __hbase-site.xml__.

##Creazione dell'applicazione

1. Passare alla directory __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ e rinominare il file app.java in __CreateTable.java__.

2. Aprire il file __CreateTable.java__ e sostituire il contenuto esistente con quello seguente:

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

    Si tratta della classe __CreateTable__, che consente di creare una tabella denominata __people__ e di popolarla con alcuni utenti predefiniti.

3. Salvare il file __CreateTable.java__.

4. Nella directory __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ creare un nuovo file denominato __SearchByEmail.java__. Usare il codice seguente come contenuto di questo file:

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

    È possibile usare la classe __SearchByEmail__ per eseguire query sulle righe in base all'indirizzo e-mail. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa la classe.

5. Salvare il file __SearchByEmail.java__.

6. Nella directory __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__ creare un nuovo file denominato __DeleteTable.java__. Usare il codice seguente come contenuto di questo file:

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

    Questa classe consente di pulire i dati di questo esempio procedendo a disabilitare e a eliminare la tabella creata dalla classe __CreateTable__.

7. Salvare il file __DeleteTable.java__.

##Compilare e creare il pacchetto dell'applicazione

1. Aprire un prompt dei comandi e passare alla directory __hbaseapp__.

2. Usare il comando seguente per compilare un file JAR contenente l'applicazione:

        mvn clean package

    In tal modo, vengono eliminati eventuali artefatti di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.

3. Quando il comando viene completato, la directory __hbaseapp\\target__ conterrà un file denominato __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] Il file __hbaseapp-1.0-SNAPSHOT.jar__ è un file uberjar (talvolta chiamato fatjar), che contiene tutte le dipendenze richieste per eseguire l'applicazione.

##Caricare il file JAR e avviare un processo

Ci sono molti modi per caricare un file nel cluster HDInsight, come descritto nella sezione [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md). La procedura seguente usa Azure PowerShell.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Dopo aver installato e configurato Azure PowerShell, creare un nuovo file denominato __hbase-runner.psm1__. Usare il codice seguente come contenuto di questo file:

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
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
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
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
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

    * __Add-HDInsightFile__: usato per caricare file in HDInsight

    * __Start-HBaseExample__: usato per eseguire le classi create in precedenza

2. Salvare il file __hbase-runner.psm1__.

3. Aprire una nuova finestra di Azure PowerShell, passare alla directory __hbaseapp__ e quindi eseguire il comando seguente.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambiare il percorso con la posizione del file __hbase-runner.psm1__ creato in precedenza. Viene registrato il modulo per questa sessione di Azure PowerShell.

2. Usare il comando seguente per caricare il file __hbaseapp-1.0-SNAPSHOT.jar__ nel cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight. Il comando caricherà quindi il file __hbaseapp-1.0-SNAPSHOT.jar__ nel percorso __example/jars__ dell'archivio primario per il cluster HDInsight.

3. Dopo avere caricato i file, usare il codice seguente per creare una nuova tabella usando __hbaseapp__.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

    Questo comando consente di creare una nuova tabella denominata __people__ nel cluster HDInsight. Non viene mostrato alcun output nella finestra della console.

2. Per cercare le voci nella tabella, usare il comando seguente:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

    Questo comando usa la classe **SearchByEmail** per eseguire la ricerca di eventuali righe in cui la famiglia della colonna __contactinformation__ e la colonna __email__ contengono la stringa __contoso.com__. Dovrebbero essere visualizzati i risultati seguenti:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    L'uso di __fabrikam.com__ per il valore `-emailRegex` restituirà gli utenti il cui campo e-mail contiene __fabrikam.com__. Poiché questa ricerca viene implementata usando un filtro basato su un'espressione regolare, è anche possibile immettere espressioni regolari come __^r__, che restituiranno voci in cui l'e-mail inizia con la lettera "r".

##Eliminare la tabella

Dopo aver finito con l'esempio, usare il comando seguente dalla sessione di Azure PowerShell per eliminare la tabella __people__ usata in questo esempio.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Sostituire __hdinsightclustername__ con il nome del cluster HDInsight.

##Risoluzione dei problemi

###Nessun risultato o risultati imprevisti quando si usa Start-HBaseExample

Usare il parametro `-showErr` per visualizzare l'errore standard (STDERR) prodotto durante l'esecuzione del processo.

<!---HONumber=AcomDC_0727_2016-->