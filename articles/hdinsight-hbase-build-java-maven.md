<properties title="Compilare un'applicazione HBase con Maven" pageTitle="Compilare un'applicazione HBase con Maven" description="Informazioni su come usare Apache Maven per compilare un'applicazione Apache HBase basata su Java e quindi distribuirla in Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr" />

## Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)

In questo articolo si apprenderà come creare e compilare un'applicazione [Apache HBase][Apache HBase] in Java usando Apache Maven, quindi a usare l'applicazione con HDInsight di Azure (Hadoop.)

[Maven][Maven] è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e creazione di report per i progetti Java. In questo articolo si apprenderà come usarlo per creare un'applicazione Java di base che crea, interroga ed elimina una tabella HBase su un cluster HDInsight di Azure.

## Requisiti

-   [Piattaforma Java JDK][Piattaforma Java JDK] 7 o versione successiva

-   [Maven][Maven]

-   [Un cluster HDInsight di Azure con HBase][Un cluster HDInsight di Azure con HBase]

## Creare il progetto

1.  Dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuol creare il progetto, ad esempio, `cd code\hdinsight`.

2.  Usare il comando **mvn**, che viene installato con Maven, per generare lo scaffolding per il progetto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Verrà creata una nuova directory nella directory corrente, con il nome specificato dal parametro **artifactID** (in questo esempio, **hbaseapp**), che conterrà i seguenti elementi.

    -   **pom.xml**: il modello a oggetti dei progetti ([POM][POM], Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto

    -   **src**: la directory che contiene la directory **main\\java\\com\\microsoft\\examples**, in cui verrà creata l'applicazione.

3.  Eliminare il file **src\\test\\java\\com\\microsoft\\examples\\apptest.java**, perché non verrà usato in questo esempio.

## Aggiornare il modello a oggetti dei progetti

1.  Modificare il file **pom.xml** aggiungere il codice seguente nella sezione `<dependencies>`.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Ciò indica a Maven che il progetto richiedere la versione **0.98.4-hadoop2** di **hbase-client**. In fase di compilazione, quest'ultimo verrà scaricato dal repository Maven predefinito. È possibile usare la [ricerca nel repository Maven][ricerca nel repository Maven] per visualizzare più informazioni su questa dipendenza.

2.  Aggiungere il codice seguente al file **pom.xml**. Il codice deve essere incluso tra tag `<project>...</project>` nel file; ad esempio, tra `</dependencies>` e `</project>`.

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

    In tal modo verrà configurata una risorsa (**conf\\hbase-site.xml**,) che contiene informazioni di configurazione per HBase.

    > [WACOM.NOTE] È anche possibile impostare i valori di configurazione tramite codice. Per scoprire come procedere, leggere i commenti nell'esempio di **CreateTable** seguente.

    Verrà configurato anche il plug-in [maven-shade-plugin][maven-shade-plugin], usato per prevenire la duplicazione della licenza nel file JAR compilato da Maven. Il motivo per cui viene usato questo plug-in è che i file di licenza duplicati causano un errore in fase di esecuzione sul cluster HDInsight. L'utilizzo del plug-in maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

    Il plug-in maven-shade-plugin produrrà anche un file uberjar (o fatjar), che contiene tutte le dipendenze richieste dall'applicazione.

3.  Salvare il file **pom.xml**.

4.  Creare una nuova directory denominata **conf** nella directory **hbaseapp**. Nella directory **conf**, creare un nuovo file denominato **hbase-site.xml** e usare il codice seguente come contenuto.

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

    > [WACOM.NOTE] Si tratta di un file hbase-site.xml file di formato ridotto al minimo, che contiene solo le impostazioni essenziali per il cluster HDInsight. Per una versione completa del file di configurazione hbase-site.xml usato da HDInsight, [accedere in remoto al desktop del cluster HDInsight][accedere in remoto al desktop del cluster HDInsight]: il file hbase-site.xml si trova nella directory C:\\apps\\dist\\hbase-\<numero versione\>-hadoop2\\conf. La parte del numero di versione del percorso del file cambierà in quanto HBase viene aggiornato sul cluster.

5.  Salvare il file **hbase-site.xml**.

## Creazione dell'applicazione

1.  Passare alla directory **hbaseapp\\src\\com\\microsoft\\examples** e rinominare il file app.java\_\_ in **CreateTable.java**.

2.  Aprire il file **CreateTable.java** e sostituire il contenuto esistente con quello seguente.

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
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

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

3.  Salvare il file **CreateTable.java**.

4.  Nella directory **hbaseapp\\com\\microsoft\\examples** creare un nuovo file denominato **SearchByEmail.java**. Usare il codice seguente come contenuto di questo file.

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

    È possibile usare la classe **SearchByEmail** per eseguire query sulle righe in base all'indirizzo e-mail. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa le classe.

5.  Salvare il file **SearchByEmail.java**.

6.  Nella directory **hbaseapp\\com\\microsoft\\examples** creare un nuovo file denominato **DeleteTable.java**. Usare il codice seguente come contenuto di questo file.

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

    Questa classe consente solo di pulire i dati di questo esempio procedendo prima a disabilitare, poi a eliminare la tabella creata dalla classe **CreateTable**.

7.  Salvare il file **CreateTable.java**.

## Compilare e creare il pacchetto dell'applicazione

1.  Aprire un prompt dei comandi e passare alla directory **hbaseapp**.

2.  Usare il comando seguente per compilare un file JAR contenente l'applicazione.

        mvn clean package

    In tal modo, verranno eliminati eventuali artefatti di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.

3.  Quando il comando viene completato, la directory **hbaseapp\\target** conterrà un file denominato **hbaseapp-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] Il file **hbaseapp-1.0-SNAPSHOT.jar** è un file uberjar (talvolta chiamato fatjar,) che contiene tutte le dipendenze richieste per eseguire l'applicazione.

## Caricare il file JAR e avviare un processo

> [WACOM.NOTE] Ci sono molti modi per caricare un file nel cluster HDInsight, come descritto nella sezione [Caricare dati per processi Hadoop in HDInsight][Caricare dati per processi Hadoop in HDInsight]. Nella procedura seguente viene usato [Azure PowerShell][Azure PowerShell].

1.  Dopo aver installato e configurato [Azure PowerShell][Azure PowerShell], creare un nuovo file denominato **hbase-runner.psm1**. Usare il codice seguente come contenuto di questo file.

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

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
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

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Questo file contiene due moduli:

    -   **Add-HDInsightFile**: usato per caricare file in HDInsight

    -   **Start-HBaseExample**: usato per eseguire le classi create in precedenza

2.  Salvare il file **hbase-runner.psm1**.

3.  Aprire una nuova finestra di Azure PowerShell, passare alla directory **hbaseapp** e quindi eseguire il comando seguente.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambiare il percorso con la posizione del file **hbase-runner.psm1** creato in precedenza. Verrà registrato il modulo per questa sessione di PowerShell.

4.  Usare il comando seguente per caricare il file **hbaseapp-1.0-SNAPSHOT.jar** nel cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Sostituire **hdinsightclustername** con il nome del cluster HDInsight. Il comando caricherà quindi il file **hbaseapp-1.0-SNAPSHOT.jar** nel percorso **example/jars** dello spazio di archiviazione primario per il cluster HDInsight.

5.  Dopo aver caricato i file, usare il codice seguente per creare una nuova tabella usando **hbaseapp**.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Sostituire **hdinsightclustername** con il nome del cluster HDInsight.

    Questo comando consente di creare una nuova tabella denominata **people** nel cluster HDInsight.

6.  Per cercare le voci nella tabella, usare il comando seguente.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Sostituire **hdinsightclustername** con il nome del cluster HDInsight.

    Verrà usata la classe SearchByEmail per eseguire la ricerca di eventuali righe in cui la colonna **contactinformation** della famiglia, colonna **email**, contiene la stringa **contoso.com**. Dovrebbero essere visualizzati i risultati seguenti:

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    L'utilizzo di **fabrikam.com** per il valore `-emailRegex` restituirà gli utenti il cui campo e-mail contiene **fabrikam.com**. Poiché questa ricerca viene implementata usando un filtro basato su un'espressione regolare, è anche possibile immettere espressioni regolari come **^r**, che restituiranno voci in cui l'e-mail inizia con la lettera "r".

## Eliminare la tabella

Dopo aver finito con l'esempio, usare il comando seguente dalla sessione di PowerShell per eliminare la tabella **people** usata in questo esempio.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Sostituire **hdinsightclustername** con il nome del cluster HDInsight.

## Risoluzione dei problemi

### Nessun risultato o risultati imprevisti quando si usa **Start-HBaseExample**

Usare il parametro `-showErr` per visualizzare il flusso STDERR prodotto durante l'esecuzione del processo.

  [Apache HBase]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Piattaforma Java JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Un cluster HDInsight di Azure con HBase]: /it-it/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [ricerca nel repository Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [accedere in remoto al desktop del cluster HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Caricare dati per processi Hadoop in HDInsight]: /it-it/documentation/articles/hdinsight-upload-data/
  [Azure PowerShell]: /it-it/documentation/articles/install-configure-powershell/
