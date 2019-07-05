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
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484881"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) nella pipeline di Azure Data Factory (ADF) usando l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non è disponibile, creare un Azure-SSIS Integration Runtime seguendo le istruzioni dettagliate riportate in [Esercitazione: Il provisioning di Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

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

4. Nel **impostazioni** scheda per l'attività Esegui pacchetto SSIS, selezionare un runtime di integrazione SSIS di Azure in cui si desidera eseguire il pacchetto. Se il pacchetto utilizza l'autenticazione di Windows per accedere ad archivi dati, ad esempio, SQL Server/file condivisioni in locale, controllare i file di Azure e così via, il **autenticazione di Windows** casella di controllo e immettere i valori per le credenziali di esecuzione del pacchetto (**Domain**/**Username**/**Password**). In alternativa, è possibile usare i segreti archiviati nell'Azure Key Vault (AKV) come i relativi valori. A tale scopo, fare clic sui **AZURE KEY VAULT** caselle di controllo delle credenziali appropriate, selezionare o modificare il servizio collegato Azure Key Vault esistente o crearne uno nuovo e quindi selezionare il nome/versione del segreto per il valore della credenziale.  Quando si crea o si modifica il servizio collegato Azure Key Vault, è possibile selezionare o modificare l'Azure Key Vault esistente o crearne uno nuovo, ma concedere l'accesso identità gestita Azure Data factory per l'Azure Key Vault se non è già. È anche possibile immettere i segreti direttamente nel formato seguente: `<AKV linked service name>/<secret name>/<secret version>`. Se per l'esecuzione del pacchetto è necessario il runtime a 32 bit, selezionare la casella di controllo **Runtime a 32 bit**. 

   Per la **posizione pacchetto**, selezionare **SSISDB**, **File System (pacchetto)** , oppure **File System (progetto)** . Se si seleziona **SSISDB** come percorso del pacchetto, viene selezionato automaticamente se è stato effettuato il provisioning del runtime di integrazione SSIS di Azure con il catalogo SSIS (SSISDB) ospitato dal Database SQL di Azure, server/istanza gestita, è necessario specificare il pacchetto per l'esecuzione che è stata distribuita in SSISDB. Se il runtime di integrazione Azure-SSIS è in esecuzione e il **manualmente le voci** casella di controllo è deselezionata, è possibile individuare e selezionare le cartelle/progetti/pacchetti/ambienti esistenti da SSISDB. Fare clic sul pulsante **Aggiorna** per recuperare i nuovi progetti/cartelle/pacchetti/ambienti aggiunti da SSISDB, in modo che siano disponibili per l'esplorazione e la selezione. 
   
   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzata** se invece si vuole immettere una registrazione personalizzata. 

   ![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se il runtime di integrazione Azure-SSIS non è in esecuzione o il **manualmente le voci** casella di controllo è selezionata, è possibile immettere i percorsi di ambiente e pacchetto da SSISDB direttamente nei formati seguenti: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se si seleziona **File System (pacchetto)** come percorso del pacchetto, viene selezionato automaticamente se è stato eseguito il provisioning del runtime di integrazione SSIS di Azure senza SSISDB, è necessario specificare il pacchetto per l'esecuzione, fornendo una (Universal Naming Convention Percorso UNC) del file di pacchetto (`.dtsx`) nei **percorso pacchetto**. Ad esempio, se si archivia il pacchetto nel file di Azure, il percorso del pacchetto sarà `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Se si configura il pacchetto in un file separato, è anche necessario specificare un percorso UNC per il file di configurazione (`.dtsConfig`) nei **percorso di configurazione**. Ad esempio, se si memorizza la configurazione nel file di Azure, il relativo percorso di configurazione saranno `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se si seleziona **File System (progetto)** come posizione per il pacchetto, è necessario specificare il pacchetto per l'esecuzione, fornendo un percorso UNC per il file di progetto (`.ispac`) nella **percorso progetto** e un pacchetto di file ( `.dtsx`) dal progetto nel **nome pacchetto**. Ad esempio, se si archivia il progetto nel file di Azure, il relativo percorso di progetto sarà `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Successivamente, è necessario specificare le credenziali per accedere ai file di configurazione/pacchetto/progetto. Se i valori immessi in precedenza per le credenziali di esecuzione pacchetti (vedere sopra), è possibile riutilizzarli controllando il **identico a quello delle credenziali di esecuzione del pacchetto** casella di controllo. In caso contrario, è necessario immettere i valori per le credenziali di accesso pacchetto (**Domain**/**Username**/**Password**). Ad esempio, se si archivia il progetto o pacchetto/configurazione nel file di Azure, il **dominio** viene `Azure`; il **Username** è `<storage account name>`; e il **Password**è `<storage account key>`. In alternativa, è possibile usare i segreti archiviati in Azure il key Vault come i relativi valori (vedere sopra). Queste credenziali verranno utilizzate per accedere ai pacchetti e pacchetti figlio nell'attività Esegui pacchetto, tutto dal proprio percorso/dello stesso progetto, nonché le configurazioni, incluse quelle specificate nei pacchetti. 
   
   Se è stata usata la **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** protezione livello durante la creazione del pacchetto tramite SQL Server Data Tools (SSDT), è necessario immettere il valore la password nel **password di crittografia**. In alternativa, è possibile usare un segreto archiviato in Azure il key Vault come relativo valore (vedere sopra). Se è stata usata la **EncryptSensitiveWithUserKey** livello di protezione, è necessario immettere nuovamente i valori riservati nei file di configurazione o scegliere il **parametri SSIS** /  **Gestioni connessioni**/**esegue l'override di proprietà** schede (vedere sotto). Se è stata usata la **EncryptAllWithUserKey** livello di protezione, non è supportata, pertanto è necessario riconfigurare il pacchetto per l'utilizzo di altro livello di protezione tramite SSDT o `dtutil` utilità della riga di comando. 
   
   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzata** se invece si vuole immettere una registrazione personalizzata. Se si desidera registrare le esecuzioni dei pacchetti di là usando i provider di log standard che possono essere specificati nel pacchetto, è necessario specificare la cartella dei log, fornendo il percorso UNC nella **percorso di registrazione**. Ad esempio, se si archiviano i log in file di Azure, il percorso di registrazione sarà `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Verrà creata in questo percorso per ogni esecuzione di pacchetti singole e denominata in base all'ID, in cui i file di log verranno generati ogni cinque minuti di esecuzione dell'attività Esegui pacchetto SSIS una sottocartella. 
   
   Infine, devi anche specificare le credenziali per accedere alla cartella del log. Se i valori immessi in precedenza per le credenziali di accesso pacchetto (vedere sopra), è possibile riutilizzarli controllando il **identico a quello del pacchetto le credenziali di accesso** casella di controllo. In caso contrario, è necessario immettere i valori per le credenziali di accesso di registrazione (**Domain**/**Username**/**Password**). Ad esempio, se si archiviano i log in file di Azure, il **dominio** viene `Azure`; il **Username** è `<storage account name>`; e il **Password** è `<storage account key>`. In alternativa, è possibile usare i segreti archiviati in Azure il key Vault come i relativi valori (vedere sopra). Queste credenziali verranno utilizzate per archiviare i log. 
   
   Per tutti i percorsi UNC indicato in precedenza, il nome completo del file deve contenere meno di 260 caratteri e il nome della directory deve contenere meno di 248 caratteri.

5. Nel **parametri SSIS** scheda per l'attività Esegui pacchetto SSIS, se il runtime di integrazione Azure-SSIS è in esecuzione, **SSISDB** sia selezionato come percorso del pacchetto e il **manualmente le voci** casella di controllo nella **impostazioni** scheda viene deselezionata, verranno visualizzati i parametri esistenti SSIS nel progetto o pacchetto selezionato da SSISDB per poter assegnare loro valori. In caso contrario, è possibile immetterli uno per uno per assegnare manualmente i valori. Assicurarsi che siano presenti e che siano stati immessi correttamente perché l'esecuzione del pacchetto riesca. 
   
   Se è stata usata la **EncryptSensitiveWithUserKey** livello di protezione durante la creazione del pacchetto tramite SSDT e **File System (pacchetto)** /**File System (progetto)** sia selezionata come percorso del pacchetto, è anche necessario immettere di nuovo i parametri sensibili per assegnare loro valori nei file di configurazione o in questa scheda. 
   
   Quando si assegna valori ai parametri, è possibile aggiungere contenuto dinamico tramite le espressioni, funzioni, variabili di sistema di Azure Data factory e variabili/parametri di pipeline di Azure Data factory. In alternativa, è possibile usare i segreti archiviati in Azure il key Vault come i relativi valori (vedere sopra).

   ![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Nel **gestioni connessioni** scheda per l'attività Esegui pacchetto SSIS, se il runtime di integrazione Azure-SSIS è in esecuzione, **SSISDB** sia selezionato come percorso del pacchetto e il **manualmente le voci**casella di controllo nella **impostazioni** scheda viene deselezionata, verranno visualizzate le gestioni connessioni esistenti nel progetto o pacchetto selezionato da SSISDB per poter assegnare valori alle relative proprietà. In caso contrario, è possibile immetterli singolarmente per assegnare valori alle relative proprietà manualmente: assicurarsi che esistano e siano state immesse correttamente per l'esecuzione del pacchetto abbia esito positivo. 
   
   Se è stata usata la **EncryptSensitiveWithUserKey** livello di protezione durante la creazione del pacchetto tramite SSDT e **File System (pacchetto)** /**File System (progetto)** sia selezionata come percorso del pacchetto, è anche necessario immettere nuovamente le proprietà di gestione connessione riservate per assegnare loro valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano i valori per le proprietà della gestione connessione, è possibile aggiungere contenuto dinamico tramite le espressioni, funzioni, variabili di sistema di Azure Data factory e variabili/parametri di pipeline di Azure Data factory. In alternativa, è possibile usare i segreti archiviati in Azure il key Vault come i relativi valori (vedere sopra).

   ![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Nel **esegue l'override di proprietà** scheda per l'attività Esegui pacchetto SSIS, è possibile immettere i percorsi delle proprietà esistenti nel pacchetto selezionato singolarmente per assegnare loro valori manualmente, assicurarsi che esistano e siano correttamente immesso per l'esecuzione del pacchetto abbia esito positivo, ad esempio, per sostituire il valore della variabile utente, immettere il relativo percorso nel formato seguente: `\Package.Variables[User::<variable name>].Value`. 
   
   Se è stata usata la **EncryptSensitiveWithUserKey** livello di protezione durante la creazione del pacchetto tramite SSDT e **File System (pacchetto)** /**File System (progetto)** sia selezionata come percorso del pacchetto, è anche necessario immettere nuovamente le proprietà sensibili per assegnare loro valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano i valori per le proprietà, è possibile aggiungere contenuto dinamico tramite le espressioni, funzioni, variabili di sistema di Azure Data factory e variabili/parametri di pipeline di Azure Data factory.

   ![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   I valori assegnati nel file di configurazione e attivata la *parametri SSIS* scheda può essere sottoposto a override usando la **gestioni connessioni**/**esegue l'override di proprietà** tabulazioni, mentre quelle assegnate nella **gestioni connessioni** della scheda può anche eseguire l'override usando la **esegue l'override di proprietà** scheda.

8. Per convalidare la configurazione della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>** .

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

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione di attività della pipeline e utilizzare l'ID per controllare i log di esecuzione più completi e i messaggi di errore in SQL Server Management Studio (SSMS).

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
