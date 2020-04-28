---
title: Eseguire processi Apache Sqoop tramite PowerShell e Azure HDInsight
description: Informazioni su come usare Azure PowerShell da una workstation per eseguire l'importazione e l'esportazione di Apache Sqoop tra un cluster Apache Hadoop e un database SQL di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: f39b595adf249b7412cb9b6b48f86b6fbd2c5e1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76263405"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Eseguire processi Apache Sqoop tramite Azure PowerShell per Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Azure PowerShell per eseguire i processi Apache Sqoop in Azure HDInsight per importare ed esportare dati tra un cluster HDInsight e un database SQL di Azure o un database SQL Server.  Questo articolo è una continuazione dell' [uso di Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Prerequisiti

* Una workstation con Azure PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/overview) installato.

* Il completamento della [configurazione dell'ambiente di test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Familiarità con Sqoop. Per ulteriori informazioni, vedere la [Guida dell'utente di Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="sqoop-export"></a>Esportazione con Sqoop

Da hive a SQL Server.

Questo esempio Mostra come esportare i dati dalla `hivesampletable` tabella hive alla `mobiledata` tabella nel database SQL. Impostare i valori per le variabili seguenti, quindi eseguire il comando.

```powershell
$hdinsightClusterName = ""
$httpPassword = ''
$sqlDatabasePassword = ''

# These values only need to be changed if the template was not followed.
$httpUserName = "admin"
$sqlServerLogin = "sqluser"
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerName.database.windows.net;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# start export
New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table mobiledata --hcatalog-table hivesampletable" `
    | Start-AzHDInsightJob `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential
```

### <a name="alternative-execution"></a>Esecuzione alternativa

1. Il codice seguente esegue la stessa esportazione; Tuttavia, fornisce un modo per leggere i log di output. Eseguire il codice per avviare l'esportazione.

    ```powershell
    $sqoopCommand = "export --connect $connectionString --table mobiledata --hcatalog-table hivesampletable"
    
    $sqoopDef = New-AzHDInsightSqoopJobDefinition `
        -Command $sqoopCommand
    
    $sqoopJob = Start-AzHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef
    ```

1. Il codice riportato di seguito consente di visualizzare i log di output. Eseguire il codice seguente:

    ```powershell
    Get-AzHDInsightJobOutput `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Get-AzHDInsightJobOutput `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    ```

Se viene visualizzato il messaggio di errore `The specified blob does not exist.`, riprovare tra qualche minuto.

## <a name="sqoop-import"></a>Importazione con Sqoop

Da SQL Server ad archiviazione di Azure. Questo esempio importa i dati dalla `mobiledata` tabella nel database SQL alla `wasb:///tutorials/usesqoop/importeddata` directory in HDInsight. I campi nei dati sono separati da un carattere di tabulazione e le righe terminano con un carattere di nuova riga. Questo esempio presuppone che l'esempio precedente sia stato completato.

```powershell
$sqoopCommand = "import --connect $connectionString --table mobiledata --target-dir wasb:///tutorials/usesqoop/importeddata --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1"


$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command $sqoopCommand

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Get-AzHDInsightJobOutput `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Get-AzHDInsightJobOutput `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

```

## <a name="additional-sqoop-export-example"></a>Esempio di esportazione Sqoop aggiuntiva

Si tratta di un esempio affidabile che consente di esportare `/tutorials/usesqoop/data/sample.log` i dati dall'account di archiviazione predefinito e quindi di importarli in `log4jlogs` una tabella denominata in un database SQL Server. Questo esempio non dipende dagli esempi precedenti.

Lo script di PowerShell seguente pre-elabora il file di origine e quindi lo esporta in un database SQL di Azure `log4jlogs`nella tabella. Sostituire `CLUSTERNAME`, `CLUSTERPASSWORD`e `SQLPASSWORD` con i valori usati dal prerequisito.

```powershell
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Limitazioni

HDInsight basato su Linux prevede le limitazioni seguenti:

* Esportazione in blocco: il connettore Sqoop usato per esportare i dati in Microsoft SQL Server o nel database SQL di Azure attualmente non supporta gli inserimenti bulk.

* Invio in batch: usando l'opzione `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di inviare in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache oozie con HDInsight](../hdinsight-use-oozie-linux-mac.md): usare l'azione Sqoop in un flusso di lavoro oozie.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per caricare i dati in HDInsight o in Archiviazione BLOB di Azure.
