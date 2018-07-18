---
title: Eseguire pacchetti SSIS tramite attività Esecuzione del pacchetto SSIS - Azure | Microsoft Docs
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) da una pipeline di Azure Data Factory usando l'attività di esecuzione del pacchetto SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/25/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 5ff397e8b13d56b3b034854c507f8bef05008812
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054722"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività di esecuzione del pacchetto SSIS in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto SSIS in una pipeline di Azure Data Factory usando un'attività di esecuzione del pacchetto SSIS. 

## <a name="prerequisites"></a>prerequisiti

### <a name="azure-sql-database"></a>database SQL di Azure 
La procedura dettagliata riportata in questo articolo usa un database SQL di Azure che ospita il catalogo SSIS. È anche possibile usare un'istanza SQL gestita di Azure (anteprima).

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Se non è disponibile, creare un runtime di integrazione SSIS di Azure seguendo le istruzioni dettagliate riportate in [Esercitazione: Distribuire pacchetti SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfaccia utente di Data Factory (portale di Azure)
In questa sezione viene usata l'interfaccia utente di Data Factory per creare una pipeline di Data Factory con un'attività di esecuzione del pacchetto SSIS che esegue un pacchetto SSIS.

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
La prima operazione da eseguire è creare una data factory usando il portale di Azure. 

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Passare al [portale di Azure](https://portal.azure.com). 
3. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Il nome non è disponibile - Errore](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
    Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2** per la **versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate da Data Factory. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre località.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
    ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda separata. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS
In questo passaggio viene usata l'interfaccia utente di Data Factory per creare una pipeline. Si aggiunge un'attività di esecuzione del pacchetto SSIS alla pipeline e la si configura per eseguire il pacchetto SSIS. 

1. Nella pagina delle attività iniziali fare clic su **Create pipeline** (Crea pipeline): 

    ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Esegui pacchetto SSIS** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività SSIS nell'area di progettazione](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Nella scheda **Generale** scheda delle proprietà per l'attività di esecuzione del pacchetto SSIS, fornire un nome e una descrizione per l'attività. Facoltativamente, impostare i valori per timeout e ripetizione.

    ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Nella scheda **Impostazioni** delle proprietà per l'attività di esecuzione del pacchetto SSIS selezionare il runtime di integrazione Azure-SSIS associato al database `SSISDB` in cui è distribuito il pacchetto. Fornire il percorso del pacchetto nel database `SSISDB` nel formato `<folder name>/<project name>/<package name>.dtsx`. Facoltativamente specificare l'esecuzione a 32 bit e un livello di registrazione predefinito o personalizzato e fornire un percorso di ambiente nel formato `<folder name>/<environment name>`.

    ![Impostare le proprietà nella scheda Impostazioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Per convalidare la configurazione della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>**.

6. Pubblicare la pipeline in Data Factory facendo clic sul pulsante **Publish All** (Pubblica tutto). 

### <a name="optionally-parameterize-the-activity"></a>Facoltativamente, parametrizzare l'attività

Facoltativamente, assegnare valori, espressioni o funzioni che possano fare riferimento alle variabili di sistema di Data Factory, ai parametri di progetto o di pacchetto in formato JSON nella scheda **Avanzata**. Ad esempio, è possibile assegnare i parametri della pipeline di Data Factory per il progetto SSIS o i parametri del pacchetto come illustrato nella schermata seguente:

![Aggiungere parametri per l'attività di esecuzione del pacchetto SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

### <a name="run-and-monitor-the-pipeline"></a>Eseguire e monitorare la pipeline
In questa sezione si attiva un'esecuzione della pipeline e quindi la si monitora. 

1. Per attivare un'esecuzione della pipeline, fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

    ![Trigger now (Attiva adesso)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**. 

3. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni (ad esempio l'Ora di inizio dell'esecuzione). Per aggiornare la visualizzazione, fare clic su **Aggiorna**.

    ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Verrà visualizzata una sola esecuzione attività dal momento che la pipeline ha una sola attività (l'attività di esecuzione del pacchetto SSIS).

    ![Esecuzioni attività](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. È possibile eseguire la **query** seguente sul database SSISDB nel server SQL Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione di attività della pipeline e usare l'ID per controllare i log di esecuzione più completi e i messaggi di errore in SSMS.

    ![Ottenere l'ID di esecuzione.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> È anche possibile creare un trigger pianificato per la pipeline in modo che questa venga eseguita in base a una pianificazione (oraria, giornaliera e così via). Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
In questa sezione si userà Azure PowerShell per creare una pipeline di Data Factory con un'attività SSIS che esegue un pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
È possibile usare la stessa data factory che ha il runtime di integrazione SSIS di Azure oppure crearne una distinta. La procedura seguente descrive le operazioni necessarie per creare una data factory. Si crea una pipeline con un'attività SSIS nella data factory. L'attività SSIS esegue il pacchetto SSIS. 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Data Factory consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2, Europa occidentale e Asia sud-orientale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Creare una pipeline con un'attività SSIS 
In questo passaggio viene creata una pipeline con un'attività SSIS. L'attività esegue il pacchetto SSIS. 

1. Creare un file JSON denominato **RunSSISPackagePipeline.json** nella cartella **C:\ADF\RunSSISPackage** con contenuto simile all'esempio seguente:

    > [!IMPORTANT]
    > Sostituire i nomi di oggetto, le descrizioni, i percorsi, i valori di proprietà e parametri, le password e altri valori delle variabili prima di salvare il file. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  In Azure PowerShell passare alla cartella `C:\ADF\RunSSISPackage`.

3. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Usare il cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

È anche possibile monitorare la pipeline tramite il portale di Azure. Per istruzioni dettagliate, vedere [Monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Creare un trigger
Nel passaggio precedente è stata eseguita la pipeline su richiesta. È anche possibile creare un trigger di pianificazione per eseguire la pipeline in base a una pianificazione (oraria, giornaliera e così via).

1. Creare un file JSON denominato **MyTrigger.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente: 

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
2. In **Azure PowerShell** passare alla cartella **C:\ADF\RunSSISPackage**.
3. Eseguire il cmdlet **Set AzureRmDataFactoryV2Trigger**, che crea il trigger. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Confermare che il trigger è avviato eseguendo il cmdlet **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Eseguire il comando seguente dopo l'ora successiva. Ad esempio, se l'ora corrente è 15:25 UTC, eseguire il comando alle 16 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    È possibile eseguire la query seguente rispetto al database SSISDB nel server SQL di Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passaggi successivi
Vedere il post di blog seguente:
-   [Modernize and extend your ETL/ELT workflows with SSIS activities in ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/) (Modernizzare ed estendere i flussi di lavoro ETL/ELT con attività SSIS nelle pipeline di Azure Data Factory)
