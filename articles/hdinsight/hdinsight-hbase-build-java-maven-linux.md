---
title: Client Java HBase - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Apache Maven per compilare un'applicazione Apache HBase basata su Java e poi distribuirla in HBase in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: 
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: 03c88397e36c0fc7f19410e49f6b6f1a607659f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="build-java-applications-for-apache-hbase"></a>Compilare applicazioni Java per Apache HBase

Informazioni su come creare e compilare un'applicazione [Apache HBase](http://hbase.apache.org/) in Java. Quindi usare l'applicazione con HBase in Azure HDInsight.

La procedura descritta in questo documento usa [Maven](http://maven.apache.org/) per creare e compilare il progetto. Maven è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e report per i progetti Java.

> [!NOTE]
> La procedura descritta in questo documento è stata testata molto recentemente con HDInsight 3.6.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Requisiti

* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 8 o versioni successive.

    > [!NOTE]
    > HDInsight 3.5 e versioni successive richiedono Java 8. Le versioni precedenti di HDInsight richiedono Java 7.

* [Maven](http://maven.apache.org/)

* [Un cluster Azure HDInsight basato su Linux con HBase](hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

  > [!NOTE]
  > Le procedure descritte in questo documento sono state testate con le versioni del cluster HDInsight 3.4 e 3.5. I valori predefiniti specificati negli esempi sono relativi a un cluster HDInsight 3.5.

## <a name="create-the-project"></a>Creare il progetto

1. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto, ad esempio `cd code\hbase`.

2. Usare il comando **mvn** , che viene installato con Maven, per generare lo scaffolding per il progetto.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Se si usa PowerShell, è necessario racchiudere i parametri `-D` tra virgolette doppie.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Questo comando crea una directory con lo stesso nome del parametro **artifactID** (in questo esempio **hbaseapp**). La directory contiene gli elementi seguenti:

   * **pom.xml**: il modello a oggetti dei progetti ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.
   * **src**: la directory che contiene la directory **main/java/com/microsoft/examples**, in cui viene creata l'applicazione.

3. Eliminare il file `src/test/java/com/microsoft/examples/apptest.java`. Non viene usato in questo esempio.

## <a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti dei progetti

1. Modificare il file `pom.xml` e aggiungere il codice seguente nella sezione `<dependencies>`:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    Questa sezione indica che il progetto richiede i componenti **hbase-client** e **phoenix-core**. In fase di compilazione, queste dipendenze vengono scaricata dal repository Maven predefinito. È possibile usare la [ricerca nel repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per ottenere altre informazioni su questa dipendenza.

   > [!IMPORTANT]
   > Il numero di versione del client HBase deve corrispondere alla versione di HBase fornita con il cluster HDInsight. Usare la tabella seguente per trovare il numero di versione corretto.

   | Versione del cluster HDInsight | Versione di HBase da usare |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 e 3.6 |1.1.2 |

    Per altre informazioni sulle versioni e sui componenti di HDInsight, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md).

3. Aggiungere il codice seguente al file **pom.xml**. Nel file, il testo deve essere incluso tra i tag `<project>...</project>`, ad esempio tra `</dependencies>` e `</project>`.

   ```xml
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
                <source>1.8</source>
                <target>1.8</target>
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
   ```

    Questa sezione configura una risorsa (`conf/hbase-site.xml`) che contiene informazioni di configurazione per HBase.

   > [!NOTE]
   > È anche possibile impostare i valori di configurazione tramite codice. Vedere i commenti nell'esempio `CreateTable`.

    Questa sezione configura anche i [plug-in compiler per Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e i [plug-in shade per Maven](http://maven.apache.org/plugins/maven-shade-plugin/). Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Questo plug-in viene usato per evitare che i file di licenza duplicati causino un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

    Il plug-in maven-shade-plugin produce anche un file uberjar, che contiene tutte le dipendenze richieste dall'applicazione.

4. Salvare il file.`pom.xml`

5. Creare una directory denominata `conf` nella directory `hbaseapp`. La directory viene usata per contenere le informazioni di configurazione per la connessione a HBase.

6. Usare il comando seguente per copiare la configurazione di HBase dal cluster HBase nella directory `conf`. Sostituire `USERNAME` con il nome di accesso a SSH. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Per altre informazioni sull'uso di `ssh` e `scp`, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Creazione dell'applicazione

1. Andare alla directory `hbaseapp/src/main/java/com/microsoft/examples` e rinominare il file app.java in `CreateTable.java`.

2. Aprire il file `CreateTable.java` e sostituire il contenuto esistente con il testo seguente:

   ```java
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
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

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
   ```

    Si tratta della classe **CreateTable**, che consente di creare una tabella denominata **people** e di popolarla con alcuni utenti predefiniti.

3. Salvare il file.`CreateTable.java`

4. Creare un nuovo file denominato `SearchByEmail.java` nella directory `hbaseapp/src/main/java/com/microsoft/examples`. Usare il testo seguente come contenuto del file:

   ```java
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

        // Create a regex filter
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
   ```

    È possibile usare la classe **SearchByEmail** per eseguire query sulle righe in base all'indirizzo e-mail. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa la classe.

5. Salvare il file.`SearchByEmail.java`

6. Creare un nuovo file denominato `DeleteTable.java` nella directory `hbaseapp/src/main/hava/com/microsoft/examples`. Usare il testo seguente come contenuto del file:

   ```java
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
   ```

    Questa classe pulisce le tabelle HBase create in questo esempio disabilitando ed eliminando la tabella creata dalla classe `CreateTable`.

7. Salvare il file.`DeleteTable.java`

## <a name="build-and-package-the-application"></a>Compilare e creare il pacchetto dell'applicazione

1. Dalla directory `hbaseapp` usare il comando seguente per compilare un file JAR contenente l'applicazione:

    ```bash
    mvn clean package
    ```

    Questo comando compila e crea il pacchetto dell'applicazione in un file con estensione .jar.

2. Al completamento del comando, la directory `hbaseapp/target` contiene un file denominato `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > Il file `hbaseapp-1.0-SNAPSHOT.jar` è un file uber jar. Questo file contiene tutte le dipendenze richieste per eseguire l'applicazione.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Caricare il file JAR ed eseguire i processi (SSH)

La procedura seguente usa `scp` per copiare il file JAR nel nodo head primario di HBase nel cluster HDInsight. Il comando `ssh` viene quindi usato per connettersi al cluster ed eseguire l'esempio direttamente nel nodo head.

1. Per caricare il file jar nel cluster, usare il comando seguente:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Sostituire `USERNAME` con il nome di accesso a SSH. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

2. Per collegarsi al cluster HBase, usare il comando seguente:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Sostituire `USERNAME` con il nome di accesso a SSH. Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

3. Per creare una nuova tabella HBase tramite l'applicazione Java, usare il comando seguente:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Il comando crea una nuova tabella HBase denominata **people** e la popola con i dati.

4. Per la ricerca degli indirizzi di posta elettronica memorizzati nella tabella, usare il comando seguente:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Si ottengono i risultati seguenti:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Per eliminare la tabella, usare il comando seguente:

    

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Caricare il file JAR ed eseguire i processi (PowerShell)

La procedura seguente usa Azure PowerShell per caricare il file JAR nella risorsa di archiviazione predefinita per il cluster HBase. I cmdlet di HDInsight vengono quindi usati per eseguire gli esempi in modalità remota.

1. Dopo aver installato e configurato Azure PowerShell, creare un file denominato `hbase-runner.psm1`. Usare il testo seguente come contenuto del file:

   ```powershell
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
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

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
   ```

    Questo file contiene due moduli:

   * **Add-HDInsightFile**: viene usato per caricare file nel cluster
   * **Start-HBaseExample**: usato per eseguire le classi create prima

2. Salvare il file.`hbase-runner.psm1`

3. Aprire una nuova finestra di Azure PowerShell, passare alla directory `hbaseapp` e quindi eseguire questo comando:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Cambiare il percorso con la posizione del file `hbase-runner.psm1` creato in precedenza. Questo comando registra il modulo in Azure PowerShell.

4. Usare il comando seguente per caricare il `hbaseapp-1.0-SNAPSHOT.jar` nel cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Sostituire `hdinsightclustername` con il nome del cluster. Il comando Carica il `hbaseapp-1.0-SNAPSHOT.jar` nel percorso `example/jars` nell'archivio primario per il cluster.

5. Per creare una tabella mediante `hbaseapp`, usare il comando seguente:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Sostituire `hdinsightclustername` con il nome del cluster.

    Questo comando consente di creare una nuova tabella denominata **people** in HBase nel cluster HDInsight. Non viene mostrato alcun output nella finestra della console.

6. Per cercare le voci nella tabella, usare il comando seguente:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Sostituire `hdinsightclustername` con il nome del cluster.

    Questo comando usa la classe `SearchByEmail` per cercare le righe in cui la famiglia della colonna `contactinformation` e la colonna `email` contengono la stringa `contoso.com`. Dovrebbero essere visualizzati i risultati seguenti:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    L'uso di **fabrikam.com** per il valore `-emailRegex` restituirà gli utenti il cui campo email contiene **fabrikam.com**. È anche possibile usare espressioni regolari come termini di ricerca. Ad esempio, **^ r** restituisce gli indirizzi di posta elettronica che iniziano con la lettera "r".

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nessun risultato o risultati imprevisti quando si usa Start-HBaseExample

Usare il parametro `-showErr` per visualizzare l'errore standard (STDERR) prodotto durante l'esecuzione del processo.

## <a name="delete-the-table"></a>Eliminare la tabella

Dopo aver completato l'esempio, usare il comando seguente per eliminare la tabella **people** usata nell'esempio:

__Da una sessione `ssh`__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Da Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare SQuirreL SQL con HBase](hdinsight-hbase-phoenix-squirrel-linux.md)
