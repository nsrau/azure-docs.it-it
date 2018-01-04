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
ms.openlocfilehash: 8aabe45a1627b1a897ca9fe4bda581c7a3f6bc03
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Richiamare un pacchetto SSIS utilizzando attività di stored procedure in Azure Data Factory
In questo articolo viene descritto come richiamare un pacchetto SSIS da una pipeline di Data Factory di Azure utilizzando un'attività di stored procedure. 

> [!NOTE]
> In questo articolo si applica alla versione 1 della Data Factory, che è in genere disponibile. Se si utilizza la versione 2 del servizio Data Factory, che è in anteprima pubblica, vedere [pacchetti SSIS di richiamare utilizzando attività di stored procedure nella versione 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-sql-database"></a>database SQL di Azure 
La procedura dettagliata in questo articolo viene utilizzato un database SQL di Azure che ospita il catalogo SSIS. È inoltre possibile utilizzare un'istanza gestita di SQL Azure (anteprima privata).

### <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Creare un runtime di integrazione di Azure SSIS se non si dispone di una seguendo le istruzioni dettagliate nella [esercitazione: pacchetti di distribuzione SSIS](../tutorial-deploy-ssis-packages-azure.md). È necessario creare una factory di dati della versione 2 per creare un runtime di integrazione di Azure SSIS. 

### <a name="azure-powershell"></a>Azure PowerShell
Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
La procedura seguente viene descritta la procedura per creare una data factory. Creare una pipeline con attività stored procedure in questa data factory. L'attività di stored procedure viene eseguita una stored procedure nel database SSISDB per eseguire il pacchetto SSIS.

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
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

5. Per creare data factory, eseguire il comando seguente **New AzureRmDataFactory** cmdlet, utilizzando la proprietà percorso e ResourceGroupName dalla variabile $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure
Creare un servizio collegato per collegare il database SQL di Azure che ospita il catalogo per la data factory di SSIS. Data Factory utilizza le informazioni in questo servizio collegato per connettersi al database SSISDB ed esegue una stored procedure per eseguire un pacchetto SSIS. 

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** in **C:\ADF\RunSSISPackage** cartella con il seguente contenuto: 

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con valori di Database SQL di Azure prima di salvataggio del file.

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
2. In **Azure PowerShell**, passare il **C:\ADF\RunSSISPackage** cartella.
3. Eseguire il **New AzureRmDataFactoryLinkedService** cmdlet per creare il servizio collegato: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-an-output-dataset"></a>Creare un set di dati di output
Questo set di dati di output è un set di dati fittizi che controlla la pianificazione della pipeline. Si noti che la frequenza è impostata su ora e intervallo è impostato su 1. Pertanto, l'esecuzione della pipeline dopo un'ora all'interno della pipeline inizio e di fine. 

1. Creare un file OuputDataset.json con il seguente contenuto: 
    
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
2. Eseguire il **New AzureRmDataFactoryDataset** per creare un set di dati. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con attività di stored procedure 
In questo passaggio è creare una pipeline con attività di stored procedure. L'attività richiama la stored procedure sp_executesql per l'esecuzione del pacchetto SSIS. 

1. Creare un file JSON denominato **MyPipeline.json** nel **C:\ADF\RunSSISPackage** cartella con il seguente contenuto:

    > [!IMPORTANT]
    > Sostituire &lt;nome della cartella&gt;, &lt;nome progetto&gt;, &lt;nome pacchetto&gt; con nomi di cartella, progetto e del pacchetto nel catalogo SSIS prima di salvare il file.

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

2. Per creare la pipeline: **RunSSISPackagePipeline**, eseguire il **New AzureRmDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

2. Eseguire **Get AzureRmDataFactorySlice** per ottenere informazioni dettagliate su tutte le sezioni dell'output set di dati * *, ovvero la tabella di output della pipeline.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Si noti che il valore di StartDateTime specificato qui corrisponde all'ora di inizio specificata nel codice JSON della pipeline. 
3. Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione specifica.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato **Pronto** oppure **Operazione non riuscita**. 

    È possibile eseguire la query seguente nel database SSISDB nel server SQL Azure per verificare che il pacchetto è stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sull'attività stored procedure, vedere il [attività Stored Procedure](data-factory-stored-proc-activity.md) articolo.

