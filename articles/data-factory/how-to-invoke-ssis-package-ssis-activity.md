---
title: Eseguire un pacchetto SSIS con l'attività Esegui pacchetto SSIS-Azure
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline Azure Data Factory usando l'attività Esegui pacchetto SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/14/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ddb7cd06934c85243717dd2a34dc99bae582b6fa
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122983"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline Azure Data Factory usando l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Creare un runtime di integrazione SSIS di Azure (IR) se non è già stato fatto seguendo le istruzioni dettagliate riportate nell' [esercitazione: provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Eseguire un pacchetto nel portale di Azure
In questa sezione si usa l'interfaccia utente Data Factory o l'app per creare una pipeline Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS
In questo passaggio si usa l'interfaccia utente o l'app Data Factory per creare una pipeline. È necessario aggiungere un'attività Esegui pacchetto SSIS alla pipeline e configurarla per l'esecuzione del pacchetto SSIS. 

1. Nel Data Factory panoramica o home page nel portale di Azure selezionare il riquadro **autore & monitoraggio** per avviare l'interfaccia utente o l'app data factory in una scheda separata. 

   ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Nella casella degli strumenti **attività** espandere **generale**. Trascinare quindi un'attività **Esegui pacchetto SSIS** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività Esegui pacchetto SSIS nell'area di progettazione](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Nella scheda **generale** per l'attività Esegui pacchetto SSIS specificare un nome e una descrizione per l'attività. Impostare i valori di **timeout** e **ripetizione** facoltativi.

   ![Impostare le proprietà nella scheda Generale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Nella scheda **Impostazioni** per l'attività Esegui pacchetto SSIS selezionare un Azure-SSIS IR in cui si desidera eseguire il pacchetto. Se il pacchetto usa l'autenticazione di Windows per accedere agli archivi dati (ad esempio, SQL Server o condivisioni file locali o File di Azure), selezionare la casella di controllo **autenticazione di Windows** . Immettere i valori per le credenziali di esecuzione del pacchetto nelle caselle **dominio**, **nome utente**e **password** . 

    In alternativa, è possibile usare i segreti archiviati in Azure Key Vault come valori. A tale scopo, selezionare la casella di controllo **Azure Key Vault** accanto alla credenziale pertinente. Selezionare o modificare il servizio collegato di Key Vault esistente o crearne uno nuovo. Quindi selezionare il nome o la versione del segreto per il valore della credenziale.

    Quando si crea o si modifica il servizio collegato di Key Vault, è possibile selezionare o modificare l'insieme di credenziali delle chiavi esistente o crearne uno nuovo. Assicurarsi di concedere l'accesso Data Factory identità gestita all'insieme di credenziali delle chiavi, se non è già stato fatto. È anche possibile immettere i segreti direttamente nel formato seguente: `<Key vault linked service name>/<secret name>/<secret version>`. Se il pacchetto richiede l'esecuzione del runtime a 32 bit, selezionare la casella di controllo **Runtime a 32 bit** .

   Per **percorso pacchetto**selezionare **SSISDB**, **file System (pacchetto)** , **file System (progetto)** o **pacchetto incorporato**. Se si seleziona **SSISDB** come percorso del pacchetto, che viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR con il catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita, specificare il pacchetto da eseguire distribuito in SSISDB. 

    Se la Azure-SSIS IR è in esecuzione e la casella di controllo **voci manuali** è deselezionata, cercare e selezionare le cartelle, i progetti, i pacchetti o gli ambienti esistenti da SSISDB. Selezionare **Aggiorna** per recuperare le cartelle, i progetti, i pacchetti o gli ambienti appena aggiunti da SSISDB in modo che siano disponibili per l'esplorazione e la selezione. Per cercare o selezionare gli ambienti per le esecuzioni del pacchetto, è necessario configurare i progetti in anticipo per aggiungere tali ambienti come riferimenti dalle stesse cartelle in SSISDB. Per altre informazioni, vedere [creare ed eseguire il mapping di ambienti SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. 

   ![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se il Azure-SSIS IR non è in esecuzione o se è selezionata la casella di controllo **voci manuali** , immettere i percorsi del pacchetto e dell'ambiente da SSISDB direttamente nei formati seguenti: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se si seleziona **file System (pacchetto)** come percorso del pacchetto, che viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR senza SSISDB, specificare il pacchetto da eseguire fornendo un percorso di Universal Naming Convention (UNC) al file del pacchetto (@no __t_1_) nella casella **percorso pacchetto** .`.dtsx` Se, ad esempio, si archivia il pacchetto in File di Azure, il percorso del pacchetto è `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Se il pacchetto viene configurato in un file separato, è necessario specificare anche un percorso UNC per il file di configurazione (`.dtsConfig`) nella casella **percorso configurazione** . Se, ad esempio, si archivia la configurazione in File di Azure, il percorso di configurazione è `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se si seleziona **file System (progetto)** come percorso del pacchetto, specificare il pacchetto da eseguire fornendo un percorso UNC al file di progetto (`.ispac`) nella casella **percorso progetto** e un file di pacchetto (`.dtsx`) dal progetto nel **nome del pacchetto** dialogo. Se, ad esempio, si archivia il progetto in File di Azure, il percorso del progetto è `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Specificare quindi le credenziali per accedere al progetto, al pacchetto o ai file di configurazione. Se in precedenza sono stati immessi i valori per le credenziali di esecuzione del pacchetto (vedere precedente), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di esecuzione del pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso al pacchetto nelle caselle **dominio**, **nome utente**e **password** . Se, ad esempio, si archivia il progetto, il pacchetto o la configurazione in File di Azure, il dominio viene `Azure`, il nome utente viene `<storage account name>`e la password viene `<storage account key>`. 

   In alternativa, è possibile usare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedente). Queste credenziali vengono utilizzate per accedere al pacchetto e ai pacchetti figlio nell'attività Esegui pacchetto, il tutto dal proprio percorso o dallo stesso progetto, nonché le configurazioni, che includono quelle specificate nei pacchetti. 

   Se si seleziona **pacchetto incorporato** come percorso del pacchetto, trascinare il pacchetto per eseguirlo o **caricarlo** da una cartella di file nella casella specificata. Il pacchetto verrà automaticamente compresso e incorporato nel payload dell'attività. Una volta incorporato, è possibile **scaricare** il pacchetto in un secondo momento per la modifica. È anche possibile **parametrizzare** il pacchetto incorporato assegnando il pacchetto a un parametro della pipeline che può essere usato in più attività, ottimizzando quindi le dimensioni del payload della pipeline. Se il pacchetto incorporato non è completamente crittografato e si rileva l'utilizzo dell'attività Esegui pacchetto, l' **attività Esegui pacchetto** verrà selezionata automaticamente e i pacchetti figlio pertinenti con i relativi riferimenti file System verranno aggiunti automaticamente per incorporarli. Se non è possibile rilevare l'utilizzo dell'attività Esegui pacchetto, è necessario selezionare manualmente la casella di controllo **attività Esegui pacchetto** e aggiungere i pacchetti figlio pertinenti con i relativi file System riferimenti uno alla volta per incorporarli. Se i pacchetti figlio usano SQL Server riferimenti, verificare che il SQL Server sia accessibile dal Azure-SSIS IR.  L'utilizzo di riferimenti di progetto per i pacchetti figlio non è attualmente supportato.
   
   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Se è stato usato il livello di protezione **EncryptAllWithPassword** o **EncryptSensitiveWithPassword** al momento della creazione del pacchetto tramite SQL Server Data Tools, immettere il valore della password nella casella **password di crittografia** . In alternativa, è possibile usare un segreto archiviato nell'insieme di credenziali delle chiavi come valore (vedere precedente). Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , immettere nuovamente i valori sensibili nei file di configurazione o nelle schede **parametri SSIS**, **gestioni connessioni**o **override proprietà** (vedere più avanti). 

   Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato. È necessario riconfigurare il pacchetto in modo da utilizzare un altro livello di protezione tramite SQL Server Data Tools o l'utilità da riga di comando `dtutil`. 
   
   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **personalizzata** se si desidera immettere invece il nome di registrazione personalizzato. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, specificare la cartella dei log specificando il percorso UNC nella casella **percorso di registrazione** . Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione è `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Viene creata una sottocartella in questo percorso per ogni singola esecuzione del pacchetto e denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS, in cui i file di log vengono generati ogni cinque minuti. 
   
   Infine, specificare le credenziali per accedere alla cartella dei log. Se in precedenza sono stati immessi i valori per le credenziali di accesso al pacchetto (vedere precedente), è possibile riutilizzarli selezionando la stessa casella di controllo **credenziali di accesso al pacchetto** . In caso contrario, immettere i valori per le credenziali di accesso alla registrazione nelle caselle **dominio**, **nome utente**e **password** . Se ad esempio si archiviano i log in File di Azure, il dominio viene `Azure`, il nome utente viene `<storage account name>`e la password è `<storage account key>`. 

    In alternativa, è possibile usare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedente). Queste credenziali vengono usate per archiviare i log. 
   
   Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere composto da meno di 260 caratteri. Il nome della directory deve essere composto da meno di 248 caratteri.

1. Nella scheda **parametri SSIS** per l'attività Esegui pacchetto SSIS, se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo **voci manuali** nella scheda **Impostazioni** è deselezionata, SSIS esistente vengono visualizzati i parametri del progetto o del pacchetto selezionato da SSISDB per poter assegnare loro i valori. In caso contrario, è possibile immetterli uno alla volta per assegnare manualmente i valori. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SQL Server Data Tools e il **file System (pacchetto)** o il **file System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente i parametri sensibili a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori ai parametri, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline. In alternativa, è possibile usare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedente).

   ![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Nella scheda **gestioni connessioni** per l'attività Esegui pacchetto SSIS, se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo **voci manuali** nella scheda **Impostazioni** è deselezionata, la casella di controllo esistente verranno visualizzate le gestioni connessioni del progetto o del pacchetto selezionato da SSISDB per assegnare valori alle relative proprietà. In caso contrario, è possibile immetterli uno alla volta per assegnare manualmente i valori alle rispettive proprietà. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SQL Server Data Tools e il **file System (pacchetto)** o il **file System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente Proprietà della gestione connessione sensibile a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà di gestione connessione, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline. In alternativa, è possibile usare i segreti archiviati nell'insieme di credenziali delle chiavi come valori (vedere precedente).

   ![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Nella scheda **override proprietà** per l'attività Esegui pacchetto SSIS immettere i percorsi delle proprietà esistenti nel pacchetto selezionato uno alla volta per assegnare i valori manualmente. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. Ad esempio, per eseguire l'override del valore della variabile utente, immettere il percorso nel formato seguente: `\Package.Variables[User::<variable name>].Value`. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SQL Server Data Tools e il **file System (pacchetto)** o il **file System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente Proprietà riservate a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, Data Factory variabili di sistema e Data Factory parametri o variabili della pipeline.

   ![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   È possibile eseguire l'override dei valori assegnati nei file di configurazione e nella scheda **parametri SSIS** utilizzando le schede **gestioni connessioni** o **sostituzioni proprietà** . È anche possibile eseguire l'override dei valori assegnati nella scheda **gestioni connessioni** usando la scheda **sostituzioni proprietà** .

1. Per convalidare la configurazione della pipeline, selezionare **convalida** sulla barra degli strumenti. Per chiudere il **report di convalida della pipeline**, selezionare **>>** .

1. Per pubblicare la pipeline in Data Factory, selezionare **pubblica tutti**. 

### <a name="run-the-pipeline"></a>Eseguire la pipeline
In questo passaggio viene attivata un'esecuzione della pipeline. 

1. Per attivare un'esecuzione della pipeline, selezionare **trigger** sulla barra degli strumenti e selezionare **Attiva ora**. 

   ![Trigger now (Attiva adesso)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**. 

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni, ad esempio l'ora di **inizio dell'esecuzione** . Per aggiornare la visualizzazione, selezionare **Aggiorna**.

   ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Viene visualizzata una sola esecuzione di attività perché la pipeline ha una sola attività. Si tratta dell'attività Esegui pacchetto SSIS.

   ![Esecuzioni attività](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Eseguire la query seguente sul database SSISDB nel server SQL per verificare che il pacchetto sia stato eseguito. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione dell'attività della pipeline e usare l'ID per verificare i log di esecuzione e i messaggi di errore più completi in SQL Server Management Studio.

   ![Ottenere l'ID di esecuzione.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger

È anche possibile creare un trigger pianificato per la pipeline in modo che la pipeline venga eseguita in base a una pianificazione, ad esempio ogni ora o ogni giorno. Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Eseguire un pacchetto con PowerShell
In questa sezione si usa Azure PowerShell per creare una pipeline Data Factory con un'attività Esegui pacchetto SSIS che esegue il pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni dettagliate riportate in [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Creare una data factory con Azure-SSIS IR
È possibile usare un data factory esistente per cui è già stato eseguito il provisioning di Azure-SSIS IR o creare un nuovo data factory con Azure-SSIS IR. Seguire le istruzioni dettagliate riportate nell' [esercitazione: distribuire pacchetti SSIS in Azure tramite PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Creare una pipeline con un'attività di esecuzione del pacchetto SSIS 
In questo passaggio si crea una pipeline con un'attività di esecuzione del pacchetto SSIS. L'attività esegue il pacchetto SSIS. 

1. Creare un file JSON denominato *RunSSISPackagePipeline. JSON* nella cartella *C:\ADF\RunSSISPackage* con contenuto simile all'esempio seguente.

   > [!IMPORTANT]
   > Prima di salvare il file, sostituire i nomi degli oggetti, le descrizioni e i percorsi, i valori di proprietà o parametri, le password e altri valori delle variabili. 
    
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

   Per eseguire i pacchetti archiviati in file System, condivisioni file o File di Azure, immettere i valori per le proprietà del pacchetto e del percorso del log come indicato di seguito:

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

   Per eseguire pacchetti all'interno di progetti archiviati in file System, condivisioni file o File di Azure, immettere i valori per la proprietà Percorso pacchetto come indicato di seguito:

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

   Per eseguire i pacchetti incorporati, immettere i valori per la proprietà Location del pacchetto nel modo seguente:

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

2. In Azure PowerShell passare alla cartella *C:\ADF\RunSSISPackage*

3. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il cmdlet **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Ecco l'output di esempio:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Eseguire la pipeline
Usare il cmdlet **Invoke-AzDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare o incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

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

È anche possibile monitorare la pipeline usando il portale di Azure. Per istruzioni dettagliate, vedere [Monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Pianificare la pipeline con un trigger
Nel passaggio precedente è stata eseguita la pipeline su richiesta. È anche possibile creare un trigger di pianificazione per eseguire la pipeline in base a una pianificazione, ad esempio ogni ora o ogni giorno.

1. Creare un file JSON denominato MyFile *. JSON* nella cartella *C:\ADF\RunSSISPackage* con il contenuto seguente: 
        
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
    
1. In Azure PowerShell passare alla cartella *C:\ADF\RunSSISPackage*
1. Eseguire il cmdlet **set-AzDataFactoryV2Trigger** , che crea il trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Verificare che il trigger sia stato avviato eseguendo il cmdlet **Get-AzDataFactoryV2Trigger** . 

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
- [Modernizza ed Estendi i tuoi flussi di lavoro ETL/ELT con attività SSIS in pipeline di Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
