---
title: "Richiamare il pacchetto SSIS utilizzando Azure Data Factory - attività Stored Procedure | Documenti Microsoft"
description: "In questo articolo viene descritto come richiamare un pacchetto di SQL Server Integration Services (SSIS) da una pipeline di Data Factory di Azure utilizzando l'attività Stored Procedure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 713e9ad7a76c15cbde912954e00991a80b995683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Richiamare un pacchetto SSIS utilizzando attività di stored procedure in Azure Data Factory
In questo articolo viene descritto come richiamare un pacchetto SSIS da una pipeline di Data Factory di Azure utilizzando un'attività di stored procedure. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si utilizza la versione 1 del servizio Data Factory, che è disponibile a livello generale (GA), vedere [pacchetti SSIS di richiamare utilizzando attività di stored procedure nella versione 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-sql-database"></a>database SQL di Azure 
La procedura dettagliata in questo articolo viene utilizzato un database SQL di Azure che ospita il catalogo SSIS. È inoltre possibile utilizzare un'istanza gestita di SQL Azure (anteprima privata).

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Creare un runtime di integrazione di Azure SSIS se non si dispone di una seguendo le istruzioni dettagliate nella [esercitazione: pacchetti di distribuzione SSIS](tutorial-deploy-ssis-packages-azure.md).

### <a name="azure-powershell"></a>Azure PowerShell
Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
È possibile utilizzare la stessa data factory in cui è IR di SSIS di Azure o creare una factory di dati separato. La procedura seguente viene descritta la procedura per creare una data factory. Creare una pipeline con attività stored procedure in questa data factory. L'attività di stored procedure viene eseguita una stored procedure nel database SSISDB per eseguire il pacchetto SSIS. 

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando.
2. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando. 
3. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Per creare la data factory, eseguire il cmdlet **Set-AzureRmDataFactoryV2** usando le proprietà Location e ResourceGroupName della variabile $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure
Creare un servizio collegato per collegare il database SQL di Azure che ospita il catalogo per la data factory di SSIS. Data Factory utilizza le informazioni in questo servizio collegato per connettersi al database SSISDB ed esegue una stored procedure per eseguire un pacchetto SSIS. 

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** in **C:\ADF\RunSSISPackage** cartella con il seguente contenuto: 

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;username&gt;, e &lt;password&gt; con valori di Database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. In **Azure PowerShell**, passare il **C:\ADF\RunSSISPackage** cartella.

3. Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con attività di stored procedure 
In questo passaggio è creare una pipeline con attività di stored procedure. L'attività richiama la stored procedure sp_executesql per l'esecuzione del pacchetto SSIS. 

1. Creare un file JSON denominato **RunSSISPackagePipeline.json** nel **C:\ADF\RunSSISPackage** cartella con il seguente contenuto:

    > [!IMPORTANT]
    > Sostituire &lt;nome della cartella&gt;, &lt;nome progetto&gt;, &lt;nome pacchetto&gt; con nomi di cartella, progetto e del pacchetto nel catalogo SSIS prima di salvare il file. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Per creare la pipeline: **RunSSISPackagePipeline**, eseguire il **Set AzureRmDataFactoryV2Pipeline** cmdlet.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Utilizzare il **Invoke AzureRmDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>Creare un trigger
Nel passaggio precedente è stata richiamata la pipeline su richiesta. È anche possibile creare un trigger di pianificazione per eseguire la pipeline in una pianificazione (oraria, giornaliera, e così via.).

1. Creare un file JSON denominato **MyTrigger.json** in **C:\ADF\RunSSISPackage** cartella con il seguente contenuto: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. In **Azure PowerShell**, passare il **C:\ADF\RunSSISPackage** cartella.
3. Eseguire il **Set AzureRmDataFactoryV2Trigger** cmdlet per creare il trigger. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Per impostazione predefinita, il trigger è in stato di arresto. Avviare il trigger eseguendo il **inizio AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Verificare che il trigger viene avviato eseguendo la **Get AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Eseguire il comando seguente dopo l'ora di successiva. Ad esempio, se l'ora corrente è UTC alle 15:25, eseguire il comando in UTC 4 PM. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    È possibile eseguire la query seguente nel database SSISDB nel server SQL Azure per verificare che il pacchetto è stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passaggi successivi
È inoltre possibile monitorare la pipeline tramite il portale di Azure. Per istruzioni dettagliate, vedere [monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
