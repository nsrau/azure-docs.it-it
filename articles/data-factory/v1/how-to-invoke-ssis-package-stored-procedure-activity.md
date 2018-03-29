---
title: Chiamare un pacchetto SSIS usando Azure Data Factory - Attività stored procedure | Microsoft Docs
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
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 476dc5fb8a5d9fb7fbcee898517455c52a3a9d73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Chiamare un pacchetto SSIS usando l'attività stored procedure in Azure Data Factory
Questo articolo descrive come chiamare un pacchetto SSIS da una pipeline di Azure Data Factory usando un'attività stored procedure. 

> [!NOTE]
> Questo articolo si applica alla versione 1 di Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima pubblica, vedere [Chiamare pacchetti SSIS usando l'attività stored procedure nella versione 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>prerequisiti

### <a name="azure-sql-database"></a>database SQL di Azure 
La procedura dettagliata riportata in questo articolo usa un database SQL di Azure che ospita il catalogo SSIS. È anche possibile usare un'istanza gestita di database SQL di Azure (anteprima privata).

### <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Se non è disponibile, creare un runtime di integrazione SSIS di Azure seguendo le istruzioni dettagliate riportate in [Esercitazione: Distribuire pacchetti SSIS](../tutorial-create-azure-ssis-runtime-portal.md). È necessario creare una data factory della versione 2 per creare un runtime di integrazione SSIS di Azure. 

## <a name="azure-portal"></a>Portale di Azure
In questa sezione verrà usato il portale di Azure per creare una pipeline di Data Factory con un'attività stored procedure che chiama un pacchetto SSIS.

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
La prima operazione da eseguire è creare una data factory usando il portale di Azure. 

1. Passare al [portale di Azure](https://portal.azure.com). 
2. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](data-factory-naming-rules.md).

    `Data factory name ADFTutorialDataFactory is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
    Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V1** per la **versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate da Data Factory. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre località.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
    ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Fare clic sul riquadro **Creare e distribuire** per avviare l'editor di Data Factory.

    ![Editor di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure
Creare un servizio collegato per collegare il database SQL di Azure che ospita il catalogo SSIS alla data factory. Data Factory usa le informazioni in questo servizio collegato per connettersi al database SSISDB ed esegue una stored procedure per l'esecuzione di un pacchetto SSIS. 

1. Nell'editor di Data Factory scegliere **Nuovo archivio dati** dal menu e fare clic su **Database SQL di Azure**. 

    ![Nuovo archivio dati -> Database SQL di Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. Nel riquadro destro eseguire queste operazioni:

    1. Sostituire `<servername>` con il nome del server SQL di Azure. 
    2. Sostituire `<databasename>` con **SSISDB** (nome del database del catalogo SSIS). 
    3. Sostituire `<username@servername>` con il nome dell'utente che ha accesso al server SQL di Azure. 
    4. Sostituire `<password>` con la password dell'utente. 
    5. Distribuire il servizio collegato facendo clic sul pulsante **Distribuisci** sulla barra degli strumenti. 

        ![Servizio collegato per il database SQL Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Creare un set di dati fittizio per l'output
Questo set di dati di output è un set di dati fittizio che determina la pianificazione della pipeline. Si noti che il parametro frequency è impostato su Hour e il parametro interval è impostato su 1. Pertanto, la pipeline viene eseguita una volta ogni ora tra le ore di inizio e fine della pipeline. 

1. Nel riquadro sinistro dell'editor di Data Factory fare clic su **... Altro** -> **Nuovo set di dati** -> **Azure SQL**.

    ![Altro -> Nuovo set di dati](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Copiare il frammento di codice JSON seguente nell'editor JSON nel riquadro a destra. 
    
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
3. Fare clic su **Distribuisci** sulla barra degli strumenti. Questa azione distribuisce il set di dati al servizio Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con un'attività stored procedure 
In questo passaggio viene creata una pipeline con un'attività stored procedure. L'attività chiama la stored procedure sp_executesql per l'esecuzione del pacchetto SSIS. 

1. Nel riquadro sinistro fare clic su **... Altro** e quindi su **Nuova pipeline**.
2. Copiare il frammento di codice JSON seguente nell'editor JSON: 

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
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Fare clic su **Distribuisci** sulla barra degli strumenti. Questa azione distribuisce la pipeline al servizio Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline
La pianificazione per il set di dati di output è definita su base oraria. L'ora di fine della pipeline è cinque ore dopo l'ora di inizio. Pertanto, saranno visualizzate cinque esecuzioni di pipeline. 

1. Chiudere le finestre dell'editor in modo da visualizzare la home page per la data factory. Fare clic sul riquadro **Monitoraggio e gestione**. 

    ![Riquadro Diagramma](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Aggiornare l'**Ora di inizio** e l'**Ora di fine** impostandole su **01/18/2018 08:30 AM** e **01/20/2018 08:30 AM** e fare clic su **Applica**. Dovrebbero essere visualizzate le **finestre attività** associate all'esecuzione della pipeline. 

    ![Finestre attività](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Per altre informazioni sul monitoraggio delle pipeline, vedere [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
In questa sezione verrà usato Azure PowerShell per creare una pipeline di Data Factory con un'attività stored procedure che chiama un pacchetto SSIS.

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
La procedura seguente descrive le operazioni necessarie per creare una data factory. Viene creata una pipeline con un'attività stored procedure in questa data factory. L'attività stored procedure esegue una stored procedure nel database SSISDB per l'esecuzione del pacchetto SSIS.

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

5. Per creare la data factory, eseguire il cmdlet **New-AzureRmDataFactory** seguente usando le proprietà Location e ResourceGroupName della variabile $ResGrp: 
    
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
3. Eseguire il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
Questo set di dati di output è un set di dati fittizio che determina la pianificazione della pipeline. Si noti che il parametro frequency è impostato su Hour e il parametro interval è impostato su 1. Pertanto, la pipeline viene eseguita una volta ogni ora tra le ore di inizio e fine della pipeline. 

1. Creare un file OuputDataset.json con il contenuto seguente: 
    
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
2. Eseguire il cmdlet **New-AzureRmDataFactoryDataset** per creare un set di dati. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
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

2. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il cmdlet **New-AzureRmDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

2. Eseguire **Get-AzureRmDataFactorySlice** per ottenere dettagli su tutte le sezioni del set di dati di output \**, ovvero la tabella di output della pipeline.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Si noti che il valore di StartDateTime specificato qui corrisponde all'ora di inizio specificata nel codice JSON della pipeline. 
3. Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione specifica.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato **Pronto** oppure **Operazione non riuscita**. 

    È possibile eseguire la query seguente rispetto al database SSISDB nel server SQL di Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sull'attività stored procedure, vedere l'articolo [Attività di stored procedure](data-factory-stored-proc-activity.md).

