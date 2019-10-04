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
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000649"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS tramite l'attività Esegui pacchetto SSIS in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto SQL Server Integration Services (SSIS) nella pipeline Azure Data Factory (ADF) usando l'attività Esegui pacchetto SSIS. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non è disponibile, creare un Azure-SSIS Integration Runtime seguendo le istruzioni dettagliate riportate in [Esercitazione: Provisioning di Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

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

4. Nella scheda **Impostazioni** per l'attività Esegui pacchetto SSIS selezionare un Azure-SSIS IR in cui si desidera eseguire il pacchetto. Se il pacchetto usa l'autenticazione di Windows per accedere agli archivi dati, ad esempio le condivisioni file o i server SQL in locale, File di Azure e così via, selezionare la casella di controllo **autenticazione di Windows** e immettere i valori per le credenziali di esecuzione del pacchetto (**dominio** ). Password nomeutente/). / In alternativa, è possibile usare i segreti archiviati nel Azure Key Vault (AKV) come valori. A tale scopo, fare clic sulla casella di controllo **Azure Key Vault** accanto alla credenziale pertinente, selezionare/modificare il servizio collegato AKV esistente o crearne uno nuovo, quindi selezionare il nome o la versione del segreto per il valore della credenziale.  Quando si crea o si modifica il servizio collegato AKV, è possibile selezionare/modificare il AKV esistente o crearne uno nuovo, ma concedere ad ADF l'accesso alle identità gestite al AKV, se non è già stato fatto. È anche possibile immettere i segreti direttamente nel formato seguente: `<AKV linked service name>/<secret name>/<secret version>`. Se per l'esecuzione del pacchetto è necessario il runtime a 32 bit, selezionare la casella di controllo **Runtime a 32 bit**. 

   Per **percorso pacchetto**selezionare **SSISDB**, **file System (pacchetto)** o **file System (progetto)** . Se si seleziona **SSISDB** come percorso del pacchetto, che viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR con il catalogo SSIS (SSISDB) ospitato dal server/istanza gestita del database SQL di Azure, è necessario specificare il pacchetto da eseguire che è stato distribuito in SSISDB. Se la Azure-SSIS IR è in esecuzione e la casella di controllo **voci manuali** è deselezionata, è possibile cercare e selezionare le cartelle, i progetti, i pacchetti e gli ambienti esistenti da SSISDB. Fare clic sul pulsante **Aggiorna** per recuperare i nuovi progetti/cartelle/pacchetti/ambienti aggiunti da SSISDB, in modo che siano disponibili per l'esplorazione e la selezione. Per esplorare/selezionare gli ambienti per le esecuzioni del pacchetto, è necessario configurare i progetti in anticipo per aggiungere tali ambienti come riferimenti dalle stesse cartelle in SSISDB. Per altre informazioni, vedere [Creazione/mapping di ambienti SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzata** se invece si vuole immettere una registrazione personalizzata. 

   ![Impostare le proprietà nella scheda Impostazioni - Modalità automatica](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se la Azure-SSIS IR non è in esecuzione o se è selezionata la casella di controllo **voci manuali** , è possibile immettere i percorsi del pacchetto e dell'ambiente da SSISDB `<folder name>/<project name>/<package name>.dtsx` direttamente `<folder name>/<environment name>`nei formati seguenti: e.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se si seleziona **file System (pacchetto)** come percorso del pacchetto, che viene selezionato automaticamente se è stato effettuato il provisioning del Azure-SSIS IR senza SSISDB, è necessario specificare il pacchetto da eseguire fornendo un percorso di Universal Naming Convention (UNC) al file del pacchetto`.dtsx`() nel **percorso del pacchetto**. Se, ad esempio, si archivia il pacchetto in File di Azure, il percorso del pacchetto `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`sarà. 
   
   Se il pacchetto viene configurato in un file separato, è necessario specificare anche un percorso UNC del file di configurazione (`.dtsConfig`) nel percorso di **configurazione**. Se ad esempio si archivia la configurazione in File di Azure, il percorso di configurazione sarà `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se si seleziona **file System (Project)** come percorso del pacchetto, è necessario specificare il pacchetto da eseguire specificando un percorso UNC per il file di progetto (`.ispac`) nel **percorso del progetto** e un file di pacchetto`.dtsx`() dal progetto nel  **Nome del pacchetto**. Se ad esempio si archivia il progetto in File di Azure, il percorso del progetto sarà `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Impostare le proprietà nella scheda Impostazioni - Modalità manuale](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Successivamente, è necessario specificare le credenziali per accedere ai file di progetto/pacchetto/configurazione. Se sono stati immessi in precedenza i valori per le credenziali di esecuzione del pacchetto (vedere sopra), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di esecuzione del pacchetto** . In caso contrario, è necessario immettere i valori per le credenziali di accesso al pacchetto (**password** **nome utente**/**dominio**/). Se ad esempio si archivia il progetto, il pacchetto o la configurazione in file di Azure, il dominio `Azure`è; il **nome utente** è `<storage account name>`e la **password** è. `<storage account key>` In alternativa, è possibile usare i segreti archiviati in AKV come valori (vedere sopra). Queste credenziali verranno utilizzate per accedere al pacchetto e ai pacchetti figlio nell'attività Esegui pacchetto, il tutto dal proprio percorso/lo stesso progetto, nonché le configurazioni, inclusi quelli specificati nei pacchetti. 
   
   Se è stato usato il livello di protezione**EncryptSensitiveWithPassword** di **EncryptAllWithPassword**/durante la creazione del pacchetto tramite SQL Server Data Tools (SSDT), è necessario immettere il valore per la password nella **crittografia password**. In alternativa, è possibile usare un segreto archiviato in AKV come valore (vedere sopra). Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** , è necessario immettere di nuovo i valori sensibili nei file di configurazione o nella/proprietà**gestione connessione** **parametri SSIS**/ **Esegue l'override** delle schede (vedere di seguito). Se è stato usato il livello di protezione **EncryptAllWithUserKey** , non è supportato, pertanto è necessario riconfigurare il pacchetto per l'uso di un altro livello di protezione tramite `dtutil` SSDT o l'utilità della riga di comando. 
   
   Per **Livello di registrazione** selezionare un ambito predefinito di registrazione per l'esecuzione del pacchetto. Selezionare la casella di controllo **Personalizzata** se invece si vuole immettere una registrazione personalizzata. Se si desidera registrare le esecuzioni dei pacchetti oltre a usare i provider di log standard che possono essere specificati nel pacchetto, è necessario specificare la cartella dei log specificando il relativo percorso UNC nel **percorso di registrazione**. Se ad esempio si archiviano i log in File di Azure, il percorso di registrazione sarà `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Una sottocartella verrà creata in questo percorso per ogni singola esecuzione del pacchetto e denominata dopo l'ID esecuzione dell'attività Esegui pacchetto SSIS, in cui i file di log verranno generati ogni cinque minuti. 
   
   Infine, è necessario specificare anche le credenziali per accedere alla cartella dei log. Se sono stati immessi in precedenza i valori per le credenziali di accesso al pacchetto (vedere sopra), è possibile riutilizzarli selezionando la casella di controllo **corrispondente alle credenziali di accesso al pacchetto** . In caso contrario, è necessario immettere i valori per le credenziali di accesso alla registrazione (**dominio**/**nomeutente**/ **).** Se ad esempio si archiviano i log in file di Azure, il **dominio** è `Azure`, il **nome utente** è `<storage account name>`e la **password** è `<storage account key>`. In alternativa, è possibile usare i segreti archiviati in AKV come valori (vedere sopra). Queste credenziali verranno usate per archiviare i log. 
   
   Per tutti i percorsi UNC indicati in precedenza, il nome completo del file deve essere inferiore a 260 caratteri e il nome della directory deve essere composto da meno di 248 caratteri.

5. Nella scheda **parametri SSIS** per l'attività Esegui pacchetto SSIS, se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo **voci manuali** nella scheda **Impostazioni** è deselezionata, SSIS esistente verranno visualizzati i parametri nel progetto/pacchetto selezionato da SSISDB per poter assegnare loro i valori. In caso contrario, è possibile immetterli uno per uno per assegnare manualmente i valori. Assicurarsi che siano presenti e che siano stati immessi correttamente perché l'esecuzione del pacchetto riesca. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e file System **(Package)** /file**System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente i parametri sensibili a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori ai parametri, è possibile aggiungere contenuto dinamico tramite espressioni, funzioni, variabili di sistema ADF e parametri/variabili della pipeline di ADF. In alternativa, è possibile usare i segreti archiviati in AKV come valori (vedere sopra).

   ![Impostare le proprietà nella scheda Parametri per SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Nella scheda **gestioni connessioni** per l'attività Esegui pacchetto SSIS, se il Azure-SSIS IR è in esecuzione, **SSISDB** viene selezionato come percorso del pacchetto e la casella di controllo **voci manuali** nella scheda **Impostazioni** è deselezionata, l'oggetto esistente verranno visualizzate le gestioni connessioni del progetto/pacchetto selezionato da SSISDB per assegnare i valori alle relative proprietà. In caso contrario, è possibile immetterli uno alla volta per assegnare i valori alle rispettive proprietà manualmente. Assicurarsi che esistano e siano stati immessi correttamente affinché l'esecuzione del pacchetto abbia esito positivo. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e file System **(Package)** /file**System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente Proprietà della gestione connessione sensibile a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà di gestione connessione, è possibile aggiungere contenuto dinamico usando espressioni, funzioni, variabili di sistema ADF e parametri/variabili della pipeline ADF. In alternativa, è possibile usare i segreti archiviati in AKV come valori (vedere sopra).

   ![Impostare le proprietà nella scheda Gestori connessioni](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Nella scheda **override proprietà** per l'attività Esegui pacchetto SSIS è possibile immettere i percorsi delle proprietà esistenti nel pacchetto selezionato uno alla volta per assegnarvi i valori manualmente. Assicurarsi che esistano e siano stati immessi correttamente per il pacchetto l'esecuzione ha esito positivo, ad esempio per eseguire l'override del valore della variabile utente, immettere il percorso nel `\Package.Variables[User::<variable name>].Value`formato seguente:. 
   
   Se è stato usato il livello di protezione **EncryptSensitiveWithUserKey** durante la creazione del pacchetto tramite SSDT e file System **(Package)** /file**System (progetto)** è selezionato come percorso del pacchetto, è necessario immettere nuovamente Proprietà riservate a cui assegnare i valori nei file di configurazione o in questa scheda. 
   
   Quando si assegnano valori alle proprietà, è possibile aggiungere contenuto dinamico tramite espressioni, funzioni, variabili di sistema ADF e parametri/variabili della pipeline di ADF.

   ![Impostare le proprietà nella scheda Override proprietà](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   È possibile eseguire l'override dei valori assegnati nei file di configurazione e nella scheda *parametri SSIS* utilizzando le schede delle**Proprietà** delle **gestioni**/connessioni, mentre quelle assegnate nella scheda **gestioni connessioni** possono eseguire inoltre l'override usando la scheda **override delle proprietà** .

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

4. È anche possibile ottenere l'ID di esecuzione SSISDB dall'output dell'esecuzione dell'attività della pipeline e usare l'ID per verificare i log di esecuzione e i messaggi di errore più completi in SQL Server Management Studio (SSMS).

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
   > Sostituire i nomi degli oggetti/descrizioni/percorsi, i valori di proprietà/parametri, le password e altri valori delle variabili prima di salvare il file. 

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

   Per eseguire i pacchetti archiviati in file System/condivisioni file/File di Azure, è possibile immettere i valori per le proprietà del percorso del pacchetto o del log come indicato di seguito.

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

   Per eseguire pacchetti all'interno di progetti archiviati in file System/condivisioni file/File di Azure, è possibile immettere i valori per la proprietà Percorso pacchetto come indicato di seguito.

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

2. In Azure PowerShell passare alla cartella `C:\ADF\RunSSISPackage`.

3. Per creare la pipeline **RunSSISPackagePipeline**, eseguire il cmdlet **set-AzDataFactoryV2Pipeline** .

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
Usare il cmdlet **Invoke-AzDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

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
3. Eseguire il cmdlet **set-AzDataFactoryV2Trigger** , che crea il trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Verificare che il trigger sia stato avviato eseguendo il cmdlet **Get-AzDataFactoryV2Trigger** . 

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
-   [Modernize and extend your ETL/ELT workflows with SSIS activities in ADF pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370) (Modernizzare ed estendere i flussi di lavoro ETL/ELT con attività SSIS nelle pipeline di Azure Data Factory)
