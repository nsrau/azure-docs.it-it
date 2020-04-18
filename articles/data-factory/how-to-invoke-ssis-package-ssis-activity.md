---
title: Eseguire un pacchetto SSIS con l'attività Esegui pacchetto SSISRun an SSIS package with the Execute SSIS Package activity
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline di Azure Data Factory usando l'attività Esegui pacchetto SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f505313b37d5289a5af10c40ede7f376eab4841d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605948"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline di Azure Data Factory usando l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Creare un runtime di integrazione Azure-SSIS se non ne è già presente seguendo le istruzioni dettagliate nell'esercitazione: Provisioning del runtime di integrazione [Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Eseguire un pacchetto nel portale di Azure
In questa sezione si usa l'interfaccia utente di Data Factory o l'app per creare una pipeline di Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS
In questo passaggio si usa l'interfaccia utente o l'app di Data Factory per creare una pipeline. È necessario aggiungere un'attività Esegui pacchetto SSIS alla pipeline e configurarla per l'esecuzione del pacchetto SSIS. 

1. Nella panoramica di Data Factory o nella home page nel portale di Azure selezionare il riquadro **Monitor & autore** per avviare l'interfaccia utente o l'app di Data Factory in una scheda separata. 

   ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Nella casella degli strumenti **Attività** espandere **Generale**. Trascinare quindi **un'attività Esegui pacchetto SSIS** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività Esegui pacchetto SSIS nell'area di progettazione](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Nella scheda **Generale** dell'attività Esegui pacchetto SSIS specificare un nome e una descrizione per l'attività. Impostare i valori di **Timeout** e **Riprova** facoltativi.

   ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Nella scheda **Impostazioni** per l'attività Esegui pacchetto SSIS selezionare un runtime di gestione Azure-SSIS in cui si vuole eseguire il pacchetto. Se il pacchetto usa l'autenticazione di Windows per accedere agli archivi dati, ad esempio server SQL o condivisioni file locali o file di Azure, selezionare la casella di controllo **Autenticazione di Windows.** Immettere i valori per le credenziali di esecuzione del pacchetto nelle caselle **Dominio**, **Nome utente**e **Password.** 

    In alternativa, è possibile usare i segreti archiviati nell'insieme di credenziali delle chiavi di Azure come valori. A tale scopo, selezionare la casella di controllo **PORTA-URE CHIAVE** accanto alla credenziale pertinente. Selezionare o modificare il servizio collegato all'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Selezionare quindi il nome o la versione segreta per il valore delle credenziali.

    Quando si crea o si modifica il servizio collegato all'insieme di credenziali delle chiavi, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere a Data Factory l'accesso all'identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È inoltre possibile immettere i segreti `<Key vault linked service name>/<secret name>/<secret version>`direttamente nel seguente formato: . Se il pacchetto necessita di runtime a 32 bit per l'esecuzione, selezionare la casella di controllo Runtime a **32 bit.**

   Per **Percorso pacchetto**, selezionare **SSISDB**, **File System (pacchetto)**, **File System (Progetto)** o **Embedded Package**. Se si seleziona **SSISDB** come percorso del pacchetto, che viene selezionato automaticamente se è stato eseguito il provisioning del runtime di accesso Azure-SSIS con il catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita, specificare il pacchetto per l'esecuzione che è stato distribuito in SSISDB. 

    Se il sistema di funzionalità di gestione delle applicazioni Azure-SSIS è in esecuzione e la casella di controllo **Voci manuali** è deselezionata, individuare e selezionare le cartelle, i progetti, i pacchetti o gli ambienti esistenti da SSISDB. Selezionare **Aggiorna** per recuperare le cartelle, i progetti, i pacchetti o gli ambienti appena aggiunti da SSISDB in modo che siano disponibili per l'esplorazione e la selezione. Per esplorare o selezionare gli ambienti per le esecuzioni dei pacchetti, è necessario configurare i progetti in anticipo per aggiungere tali ambienti come riferimenti dalle stesse cartelle in SSISDB. Per ulteriori informazioni, consultate [Creare e mappare ambienti SSIS.](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)

   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzato** se si desidera immettere il nome di registrazione personalizzato. 

   ![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se il pacchetto iR Azure-SSIS non è in esecuzione o la casella di controllo **Voci manuali** è `<folder name>/<project name>/<package name>.dtsx` selezionata, immettere i percorsi del pacchetto e dell'ambiente da SSISDB direttamente nei formati seguenti: e `<folder name>/<environment name>`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se si seleziona **File system (pacchetto)** come percorso del pacchetto, che viene selezionato automaticamente se è stato eseguito il provisioning del runtime di accesso Azure-SSIS senza SSISDB, specificare il pacchetto da eseguire fornendo un percorso UNC (Universal Naming Convention) al file del pacchetto (`.dtsx`) nella casella Percorso **pacchetto.** Ad esempio, se si archivia il pacchetto `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`in File di Azure, il percorso del pacchetto è . 
   
   Se si configura il pacchetto in un file separato, è inoltre necessario`.dtsConfig`fornire un percorso UNC al file di configurazione ( ) nella casella **Percorso di configurazione.** Ad esempio, se si archivia la configurazione `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`in File di Azure, il relativo percorso di configurazione è .

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se si seleziona **File system (Progetto)** come percorso del pacchetto, specificare il pacchetto`.ispac`da eseguire specificando un percorso UNC al file di progetto ( ) nella casella **Percorso progetto** e un file di pacchetto (`.dtsx`) dal progetto nella casella Nome **pacchetto.** Ad esempio, se si archivia il progetto `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`in File di Azure, il percorso del progetto è .

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Specificare quindi le credenziali per accedere ai file di progetto, pacchetto o di configurazione. Se in precedenza sono stati immessi i valori per le credenziali di esecuzione del pacchetto (vedere in precedenza), è possibile riutilizzarle selezionando la casella di controllo Come credenziali di **esecuzione del pacchetto.** In caso contrario, immettere i valori per le credenziali di accesso al pacchetto nelle caselle **Dominio**, **Nome utente**e **Password** . Ad esempio, se si archivia il progetto, il pacchetto `Azure`o la `<storage account name>`configurazione in `<storage account key>`File di Azure, il dominio è , il nome utente è e la password è . 

   In alternativa, è possibile utilizzare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedenza). Queste credenziali vengono usate per accedere al pacchetto e ai pacchetti figlio nell'attività Esegui pacchetto, tutte dal proprio percorso o dallo stesso progetto, nonché dalle configurazioni, che includono quelle specificate nei pacchetti. 

   Se si seleziona **Pacchetto incorporato** come percorso del pacchetto, trascinare e rilasciare il pacchetto per eseguirlo o **caricarlo** da una cartella di file nella casella fornita. Il pacchetto verrà compresso automaticamente e incorporato nel payload dell'attività. Una volta incorporato, puoi **scaricare** il pacchetto in un secondo momento per la modifica. È anche possibile **parametrizzare** il pacchetto incorporato assegnandolo a un parametro della pipeline che può essere utilizzato in più attività, ottimizzando quindi le dimensioni del payload della pipeline. Se il pacchetto incorporato non è tutto crittografato e rileviamo l'uso dell'attività Esegui pacchetto in esso, la casella di controllo **Esegui attività pacchetto** verrà selezionata automaticamente e i pacchetti figlio pertinenti con i relativi riferimenti al file system verranno aggiunti automaticamente per incorporarli. Se non è possibile rilevare l'utilizzo dell'attività Esegui pacchetto, è necessario selezionare manualmente la casella di controllo **Esegui attività pacchetto** e aggiungere i pacchetti figlio pertinenti con i relativi riferimenti al file system uno per uno per incorporarli. Se i pacchetti figlio usano riferimenti a SQL Server, verificare che SQL Server sia accessibile dal componente di accesso Azure-SSIS.  L'uso dei riferimenti al progetto per i pacchetti figlio non è attualmente supportato.
   
   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Se al momento della creazione del pacchetto tramite SQL Server Data ToolsÈ stato utilizzato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword,** immettere il valore della password nella casella Password di **crittografia.** In alternativa, è possibile utilizzare un segreto memorizzato nell'insieme di credenziali delle chiavi come valore (vedere precedenza). Se è stato utilizzato il livello di protezione **EncryptSensitiveWithUserKey,** immettere nuovamente i valori sensibili nei file di configurazione o nelle schede **Parametri SSIS**, **Gestioni connessioni**o **Sostituzioni proprietà** (vedere più avanti). 

   Se è stato utilizzato il livello di protezione **EncryptAllWithUserKey,** non è supportato. È necessario riconfigurare il pacchetto per utilizzare un altro `dtutil` livello di protezione tramite SQL Server Data ToolsSQL Server Data Tools o l'utilità della riga di comando. 
   
   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzato** se si desidera immettere il nome di registrazione personalizzato. Se si desidera registrare le esecuzioni del pacchetto oltre l'utilizzo dei provider di log standard che possono essere specificati nel pacchetto, specificare la cartella di log specificando il relativo percorso UNC nella casella **Percorso registrazione.** Ad esempio, se si archiviano i log `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`in File di Azure, il percorso di registrazione è . Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto e denominata in base all'ID di esecuzione dell'attività Esegui pacchetto SSIS, in cui vengono generati file di log ogni cinque minuti. 
   
   Infine, specificare le credenziali per accedere alla cartella di registro. Se in precedenza sono stati immessi i valori per le credenziali di accesso al pacchetto (vedere in precedenza), è possibile riutilizzarle selezionando la casella di controllo Come le credenziali di accesso al **pacchetto.** In caso contrario, immettere i valori per le credenziali di accesso alla registrazione nelle caselle **Dominio**, **Nome utente**e **Password.** Ad esempio, se si archiviano i `Azure`log in `<storage account name>`File di Azure, il dominio è , il nome utente è e la password è `<storage account key>`. 

    In alternativa, è possibile utilizzare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedenza). Queste credenziali vengono utilizzate per archiviare i log. 
   
   Per tutti i percorsi UNC menzionati in precedenza, il nome completo del file deve essere inferiore a 260 caratteri. Il nome della directory deve essere inferiore a 248 caratteri.

1. Nella scheda **Parametri SSIS** per l'attività Esegui pacchetto SSIS, se il pacchetto Azure-SSIS è in esecuzione, **SSISDB** è selezionato come percorso del pacchetto e la casella di controllo **Voci manuali** nella scheda **Impostazioni** è deselezionata, i parametri SSIS esistenti nel progetto o nel pacchetto selezionato da SSISDB vengono visualizzati per assegnare loro i valori. In caso contrario, è possibile immetterli uno alla volta per assegnare loro i valori manualmente. Assicurarsi che esistano e che siano stati immessi correttamente per la corretta esecuzione del pacchetto. 
   
   Se è stato utilizzato il livello di protezione **EncryptSensitiveWithUserKey** quando è stato creato il pacchetto tramite SQL Server Data Tools e **il file system (pacchetto)** o **file system (progetto)** è selezionato come percorso del pacchetto, è inoltre necessario immettere nuovamente i parametri sensibili per assegnarli nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori ai parametri, è possibile aggiungere contenuto dinamico utilizzando espressioni, funzioni, variabili di sistema Data Factory e parametri o variabili della pipeline di Data Factory. In alternativa, è possibile utilizzare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedenza).

   ![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Nella scheda **Gestioni connessioni** per l'attività Esegui pacchetto SSIS, se il pacchetto Azure-SSIS è in esecuzione, **SSISDB** è selezionato come percorso del pacchetto e la casella di controllo **Voci manuali** nella scheda **Impostazioni** è deselezionata, le gestioni connessioni esistenti nel progetto o nel pacchetto selezionato da SSISDB vengono visualizzate per assegnare valori alle relative proprietà. In caso contrario, è possibile immetterli uno alla volta per assegnare manualmente i valori alle relative proprietà. Assicurarsi che esistano e che siano stati immessi correttamente per la corretta esecuzione del pacchetto. 
   
   Se è stato utilizzato il livello di protezione **EncryptSensitiveWithUserKey** quando è stato creato il pacchetto tramite SQL Server Data Tools e **il file system (pacchetto)** o **file system (progetto)** è selezionato come percorso del pacchetto, è inoltre necessario immettere nuovamente le proprietà della gestione connessione riservate per assegnare loro valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà della gestione connessione, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, variabili di sistema data Factory e parametri o variabili della pipeline di Data Factory.When you assign values to your connection manager properties, you can add dynamic content by using expressions, functions, Data Factory system variables, and Data Factory pipeline parameters or variables. In alternativa, è possibile utilizzare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedenza).

   ![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Nella scheda **Sostituzioni proprietà** per l'attività Esegui pacchetto SSIS immettere i percorsi delle proprietà esistenti nel pacchetto selezionato uno alla volta per assegnare manualmente i valori. Assicurarsi che esistano e che siano stati immessi correttamente per la corretta esecuzione del pacchetto. Ad esempio, per sostituire il valore della variabile utente, `\Package.Variables[User::<variable name>].Value`immettere il percorso nel seguente formato: . 
   
   Se è stato utilizzato il livello di protezione **EncryptSensitiveWithUserKey** quando è stato creato il pacchetto tramite SQL Server Data Tools e **il file system (pacchetto)** o **file system (progetto)** è selezionato come percorso del pacchetto, è inoltre necessario immettere nuovamente le proprietà riservate per assegnarle i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, variabili di sistema Data Factory e parametri o variabili della pipeline di Data Factory.When you assign values to your properties, you can add dynamic content by using expressions, functions, Data Factory system variables, and Data Factory pipeline parameters or variables.

   ![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   I valori assegnati nei file di configurazione e nella scheda **Parametri SSIS** possono essere sostituiti utilizzando le schede **Gestioni connessioni** o **Sostituzioni proprietà.** I valori assegnati nella scheda **Gestioni connessioni** possono anche essere sostituiti utilizzando la scheda **Sostituzioni proprietà.**

1. Per convalidare la configurazione della pipeline, selezionare **Convalida** sulla barra degli strumenti. Per chiudere il report **>>** di convalida della **pipeline**, selezionare .

1. Per pubblicare la pipeline in Data Factory, selezionare **Pubblica tutto**. 

### <a name="run-the-pipeline"></a>Eseguire la pipeline
In questo passaggio viene attivata un'esecuzione della pipeline. 

1. Per attivare un'esecuzione della pipeline, selezionare **Trigger** sulla barra degli strumenti e selezionare **Attiva ora**. 

   ![Trigger now (Attiva adesso)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**. 

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni, ad esempio l'ora di **inizio esecuzione.** Per aggiornare la visualizzazione, selezionare **Aggiorna**.

   ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Viene visualizzata una sola attività eseguita perché la pipeline ha una sola attività. È l'attività Esegui pacchetto SSIS.

   ![Esecuzioni attività](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Eseguire la query seguente sul database SSISDB nel server SQL per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'attività della pipeline eseguita e usare l'ID per controllare i log di esecuzione più completi e i messaggi di errore in SQL Server Management StudioSQL Server Management Studio.You can also get the SSISDB execution ID from the output of the pipeline activity run and use the ID to check more comprehensive execution logs and error messages in SQL Server Management StudioSQL Server Management Studio.

   ![Ottenere l'ID di esecuzione.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger

È inoltre possibile creare un trigger pianificato per la pipeline in modo che la pipeline venga eseguita in base a una pianificazione, ad esempio oraria o giornaliera. Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Eseguire un pacchetto con PowerShell
In questa sezione si usa Azure PowerShell per creare una pipeline di Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni dettagliate riportate in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Creare una data factory con il componente di gestione di Azure-SSISCreate a data factory with Azure-SSIS IR
È possibile usare una data factory esistente per cui è già stato eseguito il provisioning del componente di ricreazione Azure-SSIS oppure creare una nuova factory di dati con il componente di accesso Azure-SSIS.You can either use an existing data factory that already has provisioned AR azure or create a new data factory with Azure-SSIS IR. Seguire le istruzioni dettagliate [nell'esercitazione: distribuire pacchetti SSIS in Azure tramite PowerShell.Follow](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)the step-by-step instructions in the Tutorial: Deploy SSIS packages to Azure via PowerShell .

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS 
In questo passaggio si crea una pipeline con un'attività di esecuzione del pacchetto SSIS. L'attività esegue il pacchetto SSIS. 

1. Creare un file JSON denominato *RunSSISPackagePipeline.json* nella cartella *C:, ADF e RunSSISPackage* con contenuto simile all'esempio seguente.

   > [!IMPORTANT]
   > Sostituire i nomi, le descrizioni e i percorsi degli oggetti, i valori di proprietà o di parametro, le password e altri valori delle variabili prima di salvare il file. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
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
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
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

   Per eseguire pacchetti archiviati in file system, condivisioni file o file di Azure, immettere i valori per le proprietà del pacchetto e del percorso del log come segue:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Per eseguire pacchetti all'interno di progetti archiviati in file system, condivisioni file o file di Azure, immettere i valori per la proprietà percorso del pacchetto come segue:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Per eseguire i pacchetti incorporati, immettere i valori per la proprietà della posizione del pacchetto come segue:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. In Azure PowerShell passare alla cartella *C:.*

3. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il cmdlet **Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Ecco l'output di esempio:Here's the sample output:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Eseguire la pipeline
Utilizzare il cmdlet **Invoke-AzDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare o incollare lo script seguente nella finestra di PowerShell e selezionare INVIO. 

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

È anche possibile monitorare la pipeline usando il portale di Azure.You can also monitor the pipeline by using the Azure portal. Per istruzioni dettagliate, vedere [Monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger
Nel passaggio precedente è stata eseguita la pipeline su richiesta. È inoltre possibile creare un trigger di pianificazione per eseguire la pipeline in base a una pianificazione, ad esempio oraria o giornaliera.

1. Creare un file JSON denominato *MyTrigger.json* nella cartella *C:* 
        
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
    
1. In Azure PowerShell passare alla cartella *C:.*
1. Eseguire il cmdlet **Set-AzDataFactoryV2Trigger,** che crea il trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Verificare che il trigger venga avviato eseguendo il cmdlet **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Eseguire il comando seguente dopo l'ora successiva. Ad esempio, se l'ora corrente è 15:25 UTC, eseguire il comando alle 16 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Eseguire la query seguente sul database SSISDB nel server SQL per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passaggi successivi
Vedere il post di blog seguente:
- [Modernizzare ed estendere i flussi di lavoro ETL/ELT con le attività SSIS nelle pipeline di Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
