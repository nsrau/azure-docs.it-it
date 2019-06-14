---
title: Usare Apache Maven per compilare un client Java HBase per HDInsight di Azure
description: Informazioni su come usare Apache Maven per compilare un'applicazione Apache HBase basata su Java e poi distribuirla in HBase in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726452"
---
# <a name="build-java-applications-for-apache-hbase"></a>Compilare applicazioni Java per Apache HBase

Informazioni su come creare e compilare un'applicazione [Apache HBase](https://hbase.apache.org/) in Java. Quindi usare l'applicazione con HBase in Azure HDInsight.

La procedura descritta in questo documento usa [Apache Maven](https://maven.apache.org/) per creare e compilare il progetto. Maven è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e report per i progetti Java.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster di Apache HBase in HDInsight. Visualizzare [Introduzione ad Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Se si usa PowerShell, è necessario il [modulo di AZ](https://docs.microsoft.com/powershell/azure/overview).

* Un editor di testo. Questo articolo usa Microsoft Notepad.

> [!IMPORTANT]  
> I cmdlet di Azure PowerShell [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) e [Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) attualmente non funzionano quando [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md) è abilitata nell'account di archiviazione .

## <a name="test-environment"></a>Ambiente di test
L'ambiente usato per questo articolo è stato un computer che eseguono Windows 10.  I comandi sono stati eseguiti in un prompt dei comandi e i vari file sono stati modificati con blocco note. Modificare in modo appropriato per l'ambiente.

Da un prompt dei comandi, immettere i comandi seguenti per creare un ambiente di lavoro:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Creare un progetto Maven

1. Immettere il comando seguente per creare un progetto Maven denominato **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Questo comando crea una nuova directory denominata `hbaseapp` nella posizione corrente, contenente un progetto Maven di base. Il secondo comando modifica la directory di lavoro `hbaseapp`. Il terzo comando crea una nuova directory, `conf`, che verrà usato in un secondo momento. La directory `hbaseapp` contiene gli elementi seguenti:

    * `pom.xml`:  Il modello a oggetti dei progetti (](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)POM, Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.
    * `src\main\java\com\microsoft\examples`: contiene il codice dell'applicazione.
    * `src\test\java\com\microsoft\examples`: contiene i test per l'applicazione.

2. Rimuovere il codice di esempio generato. Eliminare i file dell'applicazione e il test generato `AppTest.java`, e `App.java` immettendo i comandi seguenti:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aggiornare il modello a oggetti dei progetti

Per un riferimento completo del file POM. XML, vedere https://maven.apache.org/pom.html.  Apri `pom.xml` immettendo il comando seguente:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Aggiungere le dipendenze

Nelle `pom.xml`, aggiungere il testo seguente nel `<dependencies>` sezione:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Questa sezione indica che il progetto richiede i componenti **hbase-client** e **phoenix-core**. In fase di compilazione, queste dipendenze vengono scaricata dal repository Maven predefinito. È possibile usare la [ricerca nel repository centrale Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) per ottenere altre informazioni su questa dipendenza.

> [!IMPORTANT]  
> Il numero di versione del client HBase deve corrispondere alla versione di Apache HBase fornita con il cluster HDInsight. Usare la tabella seguente per trovare il numero di versione corretto.

| Versione del cluster HDInsight | Versione di Apache HBase da usare |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Per altre informazioni sulle versioni e sui componenti di HDInsight, vedere [Quali sono i diversi componenti di Apache Hadoop disponibili in HDInsight?](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Configurare la compilazione

I plug-in di Maven consentono di personalizzare le fasi di compilazione del progetto. Questa sezione viene usata per aggiungere plug-in, risorse e altre opzioni di configurazione della compilazione.

Aggiungere il codice seguente per il `pom.xml` file, quindi salvare e chiudere il file. Nel file, il testo deve essere incluso tra i tag `<project>...</project>`, ad esempio tra `</dependencies>` e `</project>`.

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
                <version>3.8.0</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
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

Questa sezione configura i [plug-in compiler per Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) e i [plug-in shade per Apache Maven](https://maven.apache.org/plugins/maven-shade-plugin/). Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Questo plug-in viene usato per evitare che i file di licenza duplicati causino un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

Il plug-in maven-shade-plugin produce anche un file uberjar, che contiene tutte le dipendenze richieste dall'applicazione.

### <a name="download-the-hbase-sitexml"></a>Salvare il file hbase-site.xml

Usare il comando seguente per copiare la configurazione di HBase dal cluster HBase nella directory `conf`. Sostituire `CLUSTERNAME` con HDInsight il nome del cluster e quindi immettere il comando:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Creazione dell'applicazione

### <a name="implement-a-createtable-class"></a>Implementare una classe CreateTable

Immettere il comando seguente per creare e aprire un nuovo file `CreateTable.java`. Selezionare **Sì** al prompt per creare un nuovo file.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file. Quindi chiudere il file.

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

Questo codice è il `CreateTable` classe, che crea una tabella denominata `people` e popolarla con alcuni utenti predefiniti.

### <a name="implement-a-searchbyemail-class"></a>Implementare una classe SearchByEmail

Immettere il comando seguente per creare e aprire un nuovo file `SearchByEmail.java`. Selezionare **Sì** al prompt per creare un nuovo file.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file. Quindi chiudere il file.

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

Il `SearchByEmail` classe può essere usata per eseguire query per le righe dall'indirizzo di posta elettronica. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa la classe.

### <a name="implement-a-deletetable-class"></a>Implementare una classe DeleteTable

Immettere il comando seguente per creare e aprire un nuovo file `DeleteTable.java`. Selezionare **Sì** al prompt per creare un nuovo file.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Quindi copiare e incollare il codice java seguente nel nuovo file. Quindi chiudere il file.

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

Il `DeleteTable` classe pulisce le tabelle HBase create in questo esempio disabilitando ed eliminando la tabella creata dal `CreateTable` classe.

## <a name="build-and-package-the-application"></a>Compilare e creare il pacchetto dell'applicazione

1. Dalla directory `hbaseapp` usare il comando seguente per compilare un file JAR contenente l'applicazione:

    ```cmd
    mvn clean package
    ```

    Questo comando compila e crea il pacchetto dell'applicazione in un file con estensione .jar.

2. Al completamento del comando, la directory `hbaseapp/target` contiene un file denominato `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > Il file `hbaseapp-1.0-SNAPSHOT.jar` è un file uber jar. Questo file contiene tutte le dipendenze richieste per eseguire l'applicazione.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Caricare il file JAR ed eseguire i processi (SSH)

La procedura seguente usa `scp` per copiare il file JAR nel nodo head primario di Apache HBase nel cluster HDInsight. Il comando `ssh` viene quindi usato per connettersi al cluster ed eseguire l'esempio direttamente nel nodo head.

1. Caricare il file jar nel cluster. Sostituire `CLUSTERNAME` con HDInsight il nome del cluster e quindi immettere il comando seguente:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Connettersi al cluster HBase. Sostituire `CLUSTERNAME` con HDInsight il nome del cluster e quindi immettere il comando seguente:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. Per creare una tabella HBase tramite l'applicazione Java, usare il comando seguente nella chiamata a open ssh connessione:

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

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Caricare il file JAR ed eseguire i processi (PowerShell)

La procedura seguente usa Azure PowerShell [modulo di AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) per caricare il file JAR nella risorsa di archiviazione predefinito per il cluster di Apache HBase. I cmdlet di HDInsight vengono quindi usati per eseguire gli esempi in modalità remota.

1. Dopo aver installato e configurato il modulo di AZ, creare un file denominato `hbase-runner.psm1`. Usare il testo seguente come contenuto del file:

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
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
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
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
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
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
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

2. Salvare il `hbase-runner.psm1` del file nei `hbaseapp` directory.

3. Registrare i moduli Azure PowerShell. Aprire una nuova finestra di Azure PowerShell e modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster. Immettere quindi i comandi seguenti:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Usare il comando seguente per caricare il `hbaseapp-1.0-SNAPSHOT.jar` nel cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Quando richiesto, immettere il nome e la password dell'account di accesso al cluster (admin). Il comando Carica il `hbaseapp-1.0-SNAPSHOT.jar` nel percorso `example/jars` nell'archivio primario per il cluster.

5. Per creare una tabella mediante `hbaseapp`, usare il comando seguente:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Quando richiesto, immettere il nome e la password dell'account di accesso al cluster (admin).

    Questo comando consente di creare una nuova tabella denominata **people** in HBase nel cluster HDInsight. Non viene mostrato alcun output nella finestra della console.

6. Per cercare le voci nella tabella, usare il comando seguente:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Quando richiesto, immettere il nome e la password dell'account di accesso al cluster (admin).

    Questo comando usa la classe `SearchByEmail` per cercare le righe in cui la famiglia della colonna `contactinformation` e la colonna `email` contengono la stringa `contoso.com`. Dovrebbero essere visualizzati i risultati seguenti:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    L'uso di **fabrikam.com** per il valore `-emailRegex` restituirà gli utenti il cui campo email contiene **fabrikam.com**. È anche possibile usare espressioni regolari come termini di ricerca. Ad esempio, **^ r** restituisce gli indirizzi di posta elettronica che iniziano con la lettera "r".

7. Per eliminare la tabella, usare il comando seguente:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nessun risultato o risultati imprevisti quando si usa Start-HBaseExample

Usare il parametro `-showErr` per visualizzare l'errore standard (STDERR) prodotto durante l'esecuzione del processo.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare SQuirreL SQL con Apache HBase](apache-hbase-phoenix-squirrel-linux.md)
