---
title: Richiama il pacchetto SSIS tramite l'attività Azure Data Factory stored procedure
description: Questo articolo descrive come chiamare un pacchetto di SQL Server Integration Services (SSIS) da una pipeline di Azure Data Factory usando l'attività stored procedure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: f0a63db95d0948951ec98159af381e0a04ac91ff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666388"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Chiamare un pacchetto SSIS usando l'attività stored procedure in Azure Data Factory
Questo articolo descrive come chiamare un pacchetto SSIS da una pipeline di Azure Data Factory usando un'attività stored procedure. 

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere [Richiamare pacchetti SSIS tramite attività stored procedure](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-sql-database"></a>Database SQL di Azure 
La procedura dettagliata riportata in questo articolo usa un database SQL di Azure che ospita il catalogo SSIS. È anche possibile usare un'istanza gestita di database SQL di Azure.

### <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Se non è disponibile, creare un runtime di integrazione SSIS di Azure seguendo le istruzioni dettagliate riportate in [Esercitazione: Distribuire pacchetti SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Non è possibile usare la versione 1 di Data Factory per creare un runtime di integrazione Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
In questa sezione verrà usato Azure PowerShell per creare una pipeline di Data Factory con un'attività stored procedure che chiama un pacchetto SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
La procedura seguente descrive le operazioni necessarie per creare una data factory. Viene creata una pipeline con un'attività stored procedure in questa data factory. L'attività stored procedure esegue una stored procedure nel database SSISDB per l'esecuzione del pacchetto SSIS.

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando.
2. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando. 
3. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Per creare la data factory, eseguire il cmdlet **New-AzDataFactory** seguente usando la proprietà Location e ResourceGroupName della variabile $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure
Creare un servizio collegato per collegare il database SQL di Azure che ospita il catalogo SSIS alla data factory. Data Factory usa le informazioni in questo servizio collegato per connettersi al database SSISDB ed esegue una stored procedure per l'esecuzione di un pacchetto SSIS. 

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente: 

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. In **Azure PowerShell** passare alla cartella **C:\ADF\RunSSISPackage**.
3. Eseguire il cmdlet **New-AzDataFactoryLinkedService** per creare il servizio collegato: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
Questo set di dati di output è un set di dati fittizio che determina la pianificazione della pipeline. Si noti che il parametro frequency è impostato su Hour e il parametro interval è impostato su 1. Pertanto, la pipeline viene eseguita una volta ogni ora tra le ore di inizio e fine della pipeline. 

1. Creare un file OutputDataset.json con il contenuto seguente: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Eseguire il cmdlet **New-AzDataFactoryDataset** per creare un set di dati. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con un'attività stored procedure 
In questo passaggio viene creata una pipeline con un'attività stored procedure. L'attività chiama la stored procedure sp_executesql per l'esecuzione del pacchetto SSIS. 

1. Creare un file JSON denominato **MyPipeline.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;folder name&gt;, &lt;project name&gt; e &lt;package name&gt; con i nomi di cartella, progetto e pacchetto nel catalogo SSIS prima di salvare il file.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Per creare la pipeline: **RunSSISPackagePipeline**, eseguire il cmdlet **New-AzDataFactoryPipeline** .

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Eseguire **Get-AzDataFactorySlice** per ottenere dettagli su tutte le sezioni del set di dati di output * *, ovvero la tabella di output della pipeline.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Si noti che il valore di StartDateTime specificato qui corrisponde all'ora di inizio specificata nel codice JSON della pipeline. 
1. Eseguire **Get-AzDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione specifica.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato **Pronto** oppure **Operazione non riuscita**. 

    È possibile eseguire la query seguente rispetto al database SSISDB nel server SQL di Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sull'attività stored procedure, vedere l'articolo [Attività di stored procedure](data-factory-stored-proc-activity.md).

