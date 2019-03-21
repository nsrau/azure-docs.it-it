---
title: Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS - Azure | Microsoft Docs
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) da una pipeline di Azure Data Factory tramite l'attività Esegui pacchetto SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259704"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto SSIS in una pipeline di Azure Data Factory tramite l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non è disponibile, creare un Azure-SSIS Integration Runtime seguendo le istruzioni dettagliate riportate in [Esercitazione: Distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Eseguire un pacchetto nel portale di Azure
In questa sezione viene usata l'app/interfaccia utente di Azure Data Factory per creare una pipeline di Azure Data Factory con l'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS
In questo passaggio viene usata l'app/interfaccia utente di Azure Data Factory per creare una pipeline. È necessario aggiungere un'attività Esegui pacchetto SSIS alla pipeline e configurarla per l'esecuzione del pacchetto SSIS. 

1. Nella home page/panoramica di Azure Data Factory nel portale di Azure fare clic sul riquadro **Crea e monitora** per avviare l'app/interfaccia utente di Azure Data Factory in una scheda separata. 

   ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Nella pagina **Attività iniziali** fare clic su **Creare una pipeline**: 

   ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare un'attività **Esegui pacchetto SSIS** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività Esegui pacchetto SSIS nell'area di progettazione](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Nella scheda **Generale** per l'attività Esegui pacchetto SSIS immettere un nome e una descrizione per l'attività. Facoltativamente, impostare i valori per timeout e ripetizione.

   ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Nella scheda **Impostazioni** per l'attività Esegui pacchetto SSIS selezionare la voce Azure-SSIS IR associata al database SSISDB in cui è distribuito il pacchetto. Se il pacchetto utilizza l'autenticazione di Windows per accedere ad archivi dati, ad esempio, SQL Server/file condivisioni in locale, controllare i file di Azure e così via, il **autenticazione di Windows** casella di controllo e immettere il dominio/nome utente/password per il pacchetto esecuzione. Se per l'esecuzione del pacchetto è necessario il runtime a 32 bit, selezionare la casella di controllo **Runtime a 32 bit**. Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzata** se invece si vuole immettere una registrazione personalizzata. Quando Azure-SSIS IR è in esecuzione e la casella di controllo **Manual entries** (Voci manuali) è deselezionata, è possibile individuare e selezionare i progetti/cartelle/pacchetti/ambienti esistenti da SSISDB. Fare clic sul pulsante **Aggiorna** per recuperare i nuovi progetti/cartelle/pacchetti/ambienti aggiunti da SSISDB, in modo che siano disponibili per l'esplorazione e la selezione. 

   ![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Quando il runtime di integrazione Azure-SSIS non è in esecuzione o il **manualmente le voci** casella di controllo è selezionata, è possibile immettere i percorsi di ambiente e pacchetto da SSISDB direttamente nei formati seguenti: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. Quando nella scheda **Parametri per SSIS** per l'attività Esegui pacchetto SSIS Azure-SSIS IR è in esecuzione e la casella di controllo **Manual entries** (Voci manuali) nella scheda **impostazioni** è deselezionata, vengono visualizzati i parametri per SSIS esistenti nel progetto/pacchetto selezionato da SSISDB perché sia possibile assegnarvi i valori. In caso contrario, è possibile immetterli uno per uno per assegnare manualmente i valori. Assicurarsi che siano presenti e che siano stati immessi correttamente perché l'esecuzione del pacchetto riesca. È possibile aggiungere contenuto dinamico per i valori usando le espressioni, funzioni, variabili di sistema di Azure Data factory e variabili/parametri di pipeline di Azure Data factory. In alternativa, è possibile usare i segreti archiviati nell'Azure Key Vault (AKV) come i relativi valori. A tale scopo, fare clic sui **AZURE KEY VAULT** casella di controllo accanto al parametro pertinente, seleziona/Modifica del servizio collegato Azure Key Vault esistente o crearne uno nuovo e quindi selezionare il nome/versione del segreto per il valore del parametro.  Quando si crea o si modifica il servizio collegato Azure Key Vault, è possibile selezionare o modificare l'Azure Key Vault esistente o crearne uno nuovo, ma concedere l'accesso identità gestita Azure Data factory per l'Azure Key Vault se non è già. È anche possibile immettere i segreti direttamente nel formato seguente: `<AKV linked service name>/<secret name>/<secret version>`.

   ![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Nel **gestioni connessioni** scheda per l'attività Esegui pacchetto SSIS, quando il runtime di integrazione Azure-SSIS è in esecuzione e il **manualmente le voci** casella di controllo **impostazioni** scheda viene deselezionata, le gestioni connessioni esistenti nel progetto o pacchetto selezionato da SSISDB verranno visualizzate per l'utente assegnare valori alle relative proprietà. In caso contrario, è possibile immetterli singolarmente per assegnare valori alle relative proprietà manualmente: assicurarsi che esistano e siano state immesse correttamente per l'esecuzione del pacchetto abbia esito positivo. È possibile aggiungere contenuto dinamico per i valori delle proprietà utilizzando espressioni, funzioni, variabili di sistema di Azure Data factory e variabili/parametri di pipeline di Azure Data factory. In alternativa, è possibile usare i segreti archiviati nell'Azure Key Vault (AKV) come valori delle relative proprietà. A tale scopo, fare clic sui **AZURE KEY VAULT** casella di controllo accanto alla proprietà pertinente, seleziona/Modifica del servizio collegato Azure Key Vault esistente o crearne uno nuovo e quindi selezionare il nome/versione del segreto per il valore della proprietà.  Quando si crea o si modifica il servizio collegato Azure Key Vault, è possibile selezionare o modificare l'Azure Key Vault esistente o crearne uno nuovo, ma concedere l'accesso identità gestita Azure Data factory per l'Azure Key Vault se non è già. È anche possibile immettere i segreti direttamente nel formato seguente: `<AKV linked service name>/<secret name>/<secret version>`.

   ![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Nella scheda **Override proprietà** per l'attività Esegui pacchetto SSIS è possibile immettere uno per uno i percorsi delle proprietà esistenti nel pacchetto selezionato da SSISDB per assegnarvi i valori manualmente. Assicurarsi che siano presenti e che siano stati immessi correttamente perché l'esecuzione del pacchetto riesca. Ad esempio, per eseguire l'override del valore della variabile utente, immetterne il percorso usando questo formato: `\Package.Variables[User::YourVariableName].Value`. È anche possibile aggiungere contenuto dinamico ai valori usando espressioni, funzioni, variabili di sistema di Azure Data Factory e variabili/parametri della pipeline di Azure Data Factory.

   ![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. Per convalidare la configurazione della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>**.

9. Pubblicare la pipeline in Azure Data Factory facendo clic sul pulsante **Pubblica tutti**. 

### <a name="run-the-pipeline"></a>Eseguire la pipeline
In questo passaggio viene attivata un'esecuzione della pipeline. 

1. Per attivare un'esecuzione della pipeline, fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

   ![Trigger now (Attiva adesso)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**. 

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni (ad esempio l'Ora di inizio dell'esecuzione). Per aggiornare la visualizzazione, fare clic su **Aggiorna**.

   ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Verrà visualizzata una sola esecuzione attività dal momento che la pipeline ha una sola attività (l'attività di esecuzione del pacchetto SSIS).

   ![Esecuzioni attività](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. È possibile eseguire la **query** seguente sul database SSISDB nel server SQL Azure per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione di attività della pipeline e usare l'ID per controllare i log di esecuzione più completi e i messaggi di errore in SSMS.

   ![Ottenere l'ID di esecuzione.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger

È anche possibile creare un trigger pianificato per la pipeline in modo che questa venga eseguita in base a una pianificazione (oraria, giornaliera e così via). Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Eseguire un pacchetto con PowerShell
In questa sezione viene usato Azure PowerShell per creare una pipeline di Azure Data Factory con l'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni dettagliate riportate in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Creare una pipeline di Azure Data Factory con Azure-SSIS IR
È possibile usare una pipeline di Azure Data Factory esistente in cui è già stato effettuato il provisioning di Azure-SSIS IR oppure crearne una nuova con Azure-SSIS IR seguendo le istruzioni riportate in [Esercitazione: Distribuire pacchetti SSIS in Azure tramite PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS 
In questo passaggio si crea una pipeline con un'attività di esecuzione del pacchetto SSIS. L'attività esegue il pacchetto SSIS. 

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
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
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
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
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

2. In Azure PowerShell passare alla cartella `C:\ADF\RunSSISPackage`.

3. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il **Set-AzDataFactoryV2Pipeline** cmdlet.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
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

### <a name="run-the-pipeline"></a>Eseguire la pipeline
Usare la **Invoke-AzDataFactoryV2Pipeline** cmdlet per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
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

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger
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
           }]
       }
   }    
   ```
2. In **Azure PowerShell** passare alla cartella **C:\ADF\RunSSISPackage**.
3. Eseguire la **Set-AzDataFactoryV2Trigger** cmdlet, che crea il trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il **Start-AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Verificare che il trigger viene avviato eseguendo la **Get-AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Eseguire il comando seguente dopo l'ora successiva. Ad esempio, se l'ora corrente è 15:25 UTC, eseguire il comando alle 16 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
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
