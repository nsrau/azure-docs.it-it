---
title: Come pianificare Azure-SSIS Integration Runtime
description: Questo articolo descrive come pianificare l'avvio e arresto di Azure-SSIS Integration Runtime con Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 8d7d4c8d7e01c6a4bfa644b84f03f8a2ea5bfd06
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928862"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Come avviare e arrestare Azure-SSIS Integration Runtime in base a una pianificazione
Questo articolo descrive come pianificare l'avvio e l'arresto di Azure-SSIS Integration Runtime (IR) con Azure Data Factory (ADF). Azure-SSIS IR è una risorsa dell'ambiente di calcolo di ADF dedicata all'esecuzione di pacchetti di SQL Server Integration Services (SSIS). All'esecuzione di Azure-SSIS IR è associato un costo. Di conseguenza, è in genere opportuno eseguire il runtime di integrazione solo se si devono eseguire pacchetti SSIS in Azure e arrestarlo quando non è più necessario. Per [avviare o arrestare manualmente il runtime di integrazione](manage-azure-ssis-integration-runtime.md), è possibile usare l'interfaccia utente o l'app di ADF oppure Azure PowerShell.

In alternativa, per avviare o arrestare il runtime di integrazione in base a una pianificazione è possibile creare attività Web nelle pipeline di ADF, ad esempio avviando il runtime di integrazione la mattina prima dell'esecuzione dei carichi di lavoro ETL giornalieri e arrestandolo nel pomeriggio dopo aver completato tale attività.  È anche possibile concatenare un'attività di esecuzione di un pacchetto SSIS tra due attività Web che avviano e arrestano il runtime di integrazione, in modo che l'avvio o l'arresto avvenga su richiesta, in modalità JIT prima o dopo l'esecuzione del pacchetto. Per altre informazioni sull'attività di esecuzione di un pacchetto SSIS, vedere l'articolo [Eseguire un pacchetto SSIS tramite l'attività di esecuzione del pacchetto SSIS in Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti
Se non è stato ancora effettuato il provisioning di Azure-SSIS Integration Runtime, eseguire questa operazione seguendo le istruzioni visualizzate nell'[esercitazione](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Creare e pianificare le pipeline ADF che avviano e/o arrestano Azure-SSIS IR
Questa sezione illustra come usare le attività Web nelle pipeline ADF per avviare o arrestare Azure-SSIS IR in base a una pianificazione o per avviarlo e arrestarlo su richiesta. Verrà descritta la procedura dettagliata per creare tre pipeline: 

1. La prima pipeline contiene un'attività Web che avvia Azure-SSIS IR. 
2. La seconda pipeline contiene un'attività Web che arresta Azure-SSIS IR.
3. La terza pipeline contiene un'attività di esecuzione di un pacchetto SSIS concatenata tra due attività Web che avviano e arrestano Azure-SSIS IR. 

Dopo aver creato e testato le pipeline, è possibile creare un trigger di pianificazione e associarlo a una pipeline. Con il trigger di pianificazione viene definita una pianificazione per l'esecuzione della pipeline associata. 

È possibile, ad esempio, creare due trigger, il primo con esecuzione pianificata ogni giorno alle ore 6:00 e associato alla prima pipeline, mentre il secondo con esecuzione pianificata ogni giorno alle ore 18:00 e associato alla seconda pipeline.  In questo modo, è disponibile ogni giorno un intervallo di tempo compreso tra le 6:00 e le 18:00 in cui il runtime di integrazione è in esecuzione, pronto ad eseguire i carichi di lavoro ETL giornalieri.  

Se si crea un terzo trigger programmato per essere eseguito ogni giorno a mezzanotte e associato alla terza pipeline, tale pipeline verrà eseguita ogni giorno a mezzanotte avviando il runtime di integrazione appena prima dell'esecuzione del pacchetto, eseguendo successivamente il pacchetto e arrestando il runtime di integrazione subito dopo l'esecuzione del pacchetto, per impedire l'esecuzione del runtime a vuoto.

### <a name="create-your-adf"></a>Creare un'istanza di ADF

1. Accedere al [portale di Azure](https://portal.azure.com/).    
2. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** nel campo **Nome**. 
      
   ![Pagina Nuova data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Il nome di ADF deve essere univoco a livello globale. Se viene visualizzato l'errore seguente, modificare il nome di ADF, ad esempio nomeutenteMyAzureSsisDataFactory, e riprovare. Per informazioni sulle regole di denominazione degli elementi ADF, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selezionare la **sottoscrizione** di Azure in cui si vuole creare ADF. 
5. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
   - Selezionare **Crea nuovo**e immettere il nome del nuovo gruppo di risorse.   
         
   Per informazioni sui gruppi di risorse, vedere l'articolo [Uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).
   
6. Per **Versione** selezionare **V2**.
7. Per **Località** selezionare una delle località supportate per la creazione di ADF dall'elenco a discesa.
8. Selezionare **Aggiungi al dashboard**.     
9. Fare clic su **Create**(Crea).
10. Nel dashboard di Azure viene visualizzato il riquadro seguente con lo stato: **deploying Data Factory**. 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Al termine della procedura di creazione, è possibile visualizzare la pagina di ADF come illustrato di seguito.
   
    ![Home page di Data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Fare clic su **Crea e monitora** per avviare l'interfaccia utente o l'app di ADF in una scheda separata.

### <a name="create-your-pipelines"></a>Creare le pipeline

1. Nella pagina **Attività iniziali** selezionare **Crea pipeline**. 

   ![Pagina delle attività iniziali](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Nella casella degli strumenti **Attività** espandere il menu **Generale** e trascinare un'attività **Web** nell'area di progettazione della pipeline. Nella scheda **Generale** della finestra delle proprietà dell'attività modificare il nome dell'attività in **startMyIR**. Passare alla scheda **Impostazioni** e seguire questa procedura.

    1. Per **URL**, immettere l'URL seguente per l'API REST che avvia Azure-SSIS IR, sostituendo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`e `{integrationRuntimeName}` con i valori effettivi per il runtime di integrazione: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` in alternativa, è anche possibile copiare & incollare l'ID risorsa del runtime di integrazione dalla relativa pagina di monitoraggio nell'interfaccia utente o nell'app ADF per sostituire la parte seguente dell'URL precedente: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID risorsa del runtime di integrazione SSIS di ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. In **Metodo** selezionare **POST**. 
    3. In **Corpo** immettere `{"message":"Start my IR"}`. 
    4. Per **l'autenticazione**, selezionare **MSI** per usare l'identità gestita per il file ADF. per ulteriori informazioni, vedere l'articolo [identità gestita per data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) .
    5. In **Risorsa** immettere `https://management.azure.com/`.
    
       ![Attività Web di ADF per SSIS IR in base a una pianificazione](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clonare la prima pipeline per crearne un'altra, modificando il nome dell'attività in **stopMyIR** e sostituendo le proprietà seguenti.

    1. In **URL** immettere l'URL seguente per l'API REST che arresta Azure-SSIS IR, sostituendo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` e `{integrationRuntimeName}` con i valori effettivi del runtime di integrazione: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. In **Corpo** immettere `{"message":"Stop my IR"}`. 

4. Creare una terza pipeline, trascinare un'attività **Execute SSIS Package** (Esegui pacchetto SSIS) dalla casella degli strumenti **Attività** nell'area di progettazione delle pipeline e configurare tale attività seguendo le istruzioni riportate nell'articolo [Eseguire un pacchetto SSIS tramite l'attività di esecuzione del pacchetto SSIS in Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).  In alternativa, è possibile usare un'attività **Stored procedure** e configurarla seguendo le istruzioni riportate nell'articolo [Eseguire un pacchetto SSIS con l'attività stored procedure in Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md).  Successivamente, concatenare l'attività di esecuzione del pacchetto SSIS o Stored procedure tra due attività Web che avviano o arrestano il runtime di integrazione, in modo analogo alle attività Web della prima o della seconda pipeline.

   ![Attività Web di ADF per SSIS IR su richiesta](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Assegnare all'identità gestita per ADF il ruolo **Collaboratore**, in modo che le attività Web nelle pipeline possano chiamare l'API REST per avviare o arrestare le istanze di Azure-SSIS IR di cui è stato effettuato il provisioning.  Nella pagina relativa ad ADF nel portale di Azure fare clic su **Controllo di accesso (IAM)** e quindi su **+ Aggiungi un'assegnazione di ruolo**. Nel riquadro **Aggiungi un'assegnazione di ruolo** eseguire le azioni seguenti.

    1. Per **Ruolo**, selezionare **Collaboratore**. 
    2. In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. 
    3. In **Seleziona** cercare il nome di ADF e selezionarlo. 
    4. Fare clic su **Salva**
    
   ![Assegnazione del ruolo all'identità gestita di ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Convalidare ADF e le impostazioni di tutte le pipeline facendo clic su **Convalida tuttto/Convalida** sulla barra degli strumenti della factory o della pipeline. Chiudere **Factory/Pipeline Validation Output** (Output di convalida della factory/pipeline) facendo clic sul pulsante **>>** .  

   ![Convalidare la pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Eseguire i test delle pipeline

1. Selezionare **Esecuzione dei test** sulla barra degli strumenti di ogni pipeline e visualizzare la finestra **Output** nel riquadro inferiore. 

   ![Esecuzione dei test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Per testare la terza pipeline, avviare SQL Server Management Studio (SSMS). Nella finestra **Connetti al server** eseguire le operazioni seguenti. 

    1. In **Nome server** immettere **&lt;nome server di database SQL di Azure&gt;.database.windows.net**.
    2. Selezionare **Opzioni >>** .
    3. In **Connetti al database** selezionare **SSISDB**.
    4. Selezionare **Connessione**. 
    5. Espandere **Cataloghi di Integration Services** -> **SSISDB** -> Cartella -> **Progetti** -> Progetto SSIS -> **Pacchetti**. 
    6. Fare clic con il pulsante destro del mouse sul pacchetto SSIS specifico da eseguire e selezionare **Report** -> **Report standard** -> **Tutte le esecuzioni**. 
    7. Verificare che il pacchetto sia stato eseguito. 

   ![Verificare l'esecuzione del pacchetto SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Pianificare le pipeline

Ora che le pipeline funzionano come previsto, è possibile creare trigger per eseguire le pipeline con frequenze specificate. Per informazioni dettagliate sull'associazione dei trigger alle pipeline, vedere l'articolo [Attivare la pipeline in base a una pianificazione](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Sulla barra degli strumenti della pipeline fare clic su **Trigger** e selezionare **New/Edit** (Nuovo/Modifica). 

   ![Creazione o modifica di un trigger](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Nel riquadro **Add Triggers** (Aggiungi trigger) fare clic su **+ Nuovo**.

   ![Aggiunta di un nuovo trigger](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Nel riquadro **Nuovo trigger** eseguire le operazioni seguenti: 

    1. In **Nome** immettere un nome per il trigger. Nell'esempio seguente il nome del trigger è **Run daily**. 
    2. In **Tipo** selezionare **Pianificazione**. 
    3. In **Data di inizio (UTC)** immettere una data e un'ora di inizio in formato UTC. 
    4. In **Ricorrenza** immettere una frequenza di esecuzione per il trigger. Nell'esempio seguente la frequenza specificata è una volta al **giorno**. 
    5. In **Fine** selezionare **No End** (Nessuna data di fine) oppure immettere una data e un'ora di fine dopo aver selezionato **On Date** (In data). 
    6. Selezionare **Attivato** per attivare il trigger subito dopo aver pubblicato tutte le impostazioni di ADF. 
    7. Selezionare **Avanti**.

   ![Creazione o modifica di un trigger](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Nella pagina **Trigger Run Parameters** (Parametri esecuzione trigger) rivedere ogni avviso e selezionare **Fine**. 
5. Pubblicare tutte le impostazioni di ADF selezionando **Pubblica tutti** sulla barra degli strumenti della factory. 

   ![Pubblica tutti](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorare le pipeline e i trigger nel portale di Azure

1. Per monitorare le esecuzioni di trigger e pipeline, usare la scheda **Monitoraggio** a sinistra dell'interfaccia utente o dell'app di ADF. Per informazioni dettagliate sulla procedura, vedere l'articolo [Monitorare la pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

   ![Esecuzioni di pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Per visualizzare le esecuzioni di attività associate a un'esecuzione della pipeline, selezionare il primo collegamento, **View Activity Runs** (Visualizza le esecuzioni di attività), nella colonna **Azioni**. Per la terza pipeline verranno visualizzate tre esecuzioni di attività, una per ogni attività concatenata nella pipeline (attività Web per avviare il runtime di integrazione, attività Stored procedure per eseguire il pacchetto e attività Web per arrestare il runtime di integrazione). Per visualizzare nuovamente le esecuzioni delle pipeline, fare clic sul collegamento **Pipeline** in alto.

   ![Esecuzioni attività](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Per visualizzare le esecuzioni dei trigger, selezionare **Trigger Runs** (Esecuzioni di trigger) sotto **Pipeline Runs** (Esecuzioni di pipeline) dall'elenco a discesa in alto. 

   ![Esecuzioni di trigger](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorare le pipeline e i trigger con PowerShell

Per monitorare le pipeline e i trigger, usare gli script come negli esempi seguenti.

1. Visualizzare lo stato di esecuzione di una pipeline.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Ottenere informazioni su un trigger.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Visualizzare lo stato di esecuzione di un trigger.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Creare e pianificare il runbook di Automazione di Azure che avvia o arresta Azure-SSIS IR

Questa sezione illustra come creare il runbook di Automazione di Azure che esegue lo script di PowerShell per avviare o arrestare Azure-SSIS IR in base a una pianificazione.  Questa procedura è utile quando si vogliono eseguire script aggiuntivi prima o dopo l'avvio o l'arresto del runtime di integrazione per attività di pre- o post-elaborazione.

### <a name="create-your-azure-automation-account"></a>Creare l'account di Automazione di Azure

Se non si ha già un account di Automazione di Azure, crearne uno seguendo le istruzioni riportate in questa procedura. Per informazioni dettagliate sulla procedura, vedere l'articolo [Creare un account di Automazione di Azure](../automation/automation-quickstart-create-account.md). Nell'ambito di questa procedura viene creato un account **RunAs di Azure** (un'entità servizio in Azure Active Directory) a cui viene assegnato il ruolo **Collaboratore** nella sottoscrizione di Azure. Verificare che si tratti della stessa sottoscrizione che contiene ADF con Azure-SSIS IR. Automazione di Azure userà questo account per eseguire l'autenticazione in Azure Resource Manager e intervenire sulle risorse. 

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente o l'app di ADF è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Accedere al [portale di Azure](https://portal.azure.com/).    
3. Selezionare **Nuovo** nel menu a sinistra, **Monitoraggio e gestione** e quindi **Automazione**. 

   ![Nuovo -> Monitoraggio e gestione -> Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Nel riquadro **Aggiungi account di Automazione** eseguire le operazioni seguenti.

    1. In **Nome** immettere un nome per l'account di Automazione di Azure. 
    2. In **Sottoscrizione** selezionare la sottoscrizione che contiene ADF con Azure-SSIS IR. 
    3. In **Gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo di risorse oppure **Usa esistente** per selezionarne uno esistente. 
    4. In **Località** selezionare una località per l'account di Automazione di Azure. 
    5. In **Crea un account RunAs di Azure** fare clic su **Sì**. Verrà creata un'entità servizio in Azure Active Directory, a cui viene assegnato il ruolo **Collaboratore** nella sottoscrizione di Azure.
    6. Selezionare **Aggiungi al dashboard** per visualizzare l'account in modo permanente nel dashboard di Azure. 
    7. Selezionare **Create** (Crea). 

   ![Nuovo -> Monitoraggio e gestione -> Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Lo stato della distribuzione dell'account di Automazione di Azure verrà visualizzato nel dashboard di Azure e nelle notifiche. 
    
   ![Distribuzione dell'automazione](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Dopo la creazione dell'account di Automazione di Azure, verrà visualizzata la relativa home page. 

   ![Home page di Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importare moduli di ADF

1. Selezionare **moduli** nella sezione **risorse condivise** nel menu a sinistra e verificare se **AZ. DataFactory** + **AZ. profile** nell'elenco dei moduli.

   ![Verificare i moduli necessari](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se non si dispone di **AZ. DataFactory**, passare alla PowerShell Gallery per il [modulo AZ. DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), selezionare **Distribuisci in automazione di Azure**, selezionare l'account di automazione di Azure e quindi fare clic su **OK**. Tornare alla sezione visualizzare i **moduli** nella sezione **risorse condivise** nel menu a sinistra e attendere fino a visualizzare **lo stato** del modulo **AZ. DataFactory** modificato in **disponibile**.

    ![Verificare il modulo di Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se non si dispone di **AZ. profile**, passare alla PowerShell Gallery per il [modulo AZ. profile](https://www.powershellgallery.com/packages/Az.profile/), selezionare **Distribuisci in automazione di Azure**, selezionare l'account di automazione di Azure e quindi fare clic su **OK**. Tornare a Visualizza **moduli** nella sezione **risorse condivise** nel menu a sinistra e attendere fino a visualizzare **lo stato** del modulo **AZ. profile** modificato in **disponibile**.

    ![Verificare il modulo Profilo](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Creare il runbook di PowerShell

La sezione seguente descrive la procedura per creare un runbook di PowerShell. Lo script associato al runbook avvia o arresta Azure-SSIS IR in base al comando specificato per il parametro **OPERATION**. Questa sezione non riporta i dettagli completi per la creazione di un runbook. Per altre informazioni, vedere [Creare un runbook](../automation/automation-quickstart-create-runbook.md).

1. Passare alla scheda **Runbook** e selezionare **+ Aggiungi runbook** nella barra degli strumenti. 

   ![Pulsante Aggiungi runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selezionare **Crea un nuovo runbook** ed eseguire le operazioni seguenti: 

    1. In **Nome** immettere **StartStopAzureSsisRuntime**.
    2. In **Tipo di runbook** selezionare **PowerShell**.
    3. Selezionare **Create** (Crea).
    
   ![Pulsante Aggiungi runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copiare e incollare lo script di PowerShell seguente nella finestra di script del runbook. Salvare e quindi pubblicare il runbook usando i pulsanti **Salva** e **Pubblica** sulla barra degli strumenti. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Modificare il runbook PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Testare il runbook selezionando il pulsante **Avvia** sulla barra degli strumenti. 

   ![Pulsante di avvio del runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Nel riquadro **Avvia Runbook** eseguire le operazioni seguenti: 

    1. In **RESOURCE GROUP NAME** immettere il nome del gruppo di risorse che contiene ADF con Azure-SSIS IR. 
    2. In **DATA FACTORY NAME** immettere il nome di ADF con Azure-SSIS IR. 
    3. In **AZURESSISNAME** immettere il nome di Azure-SSIS IR. 
    4. In **OPERATION** immettere **START**. 
    5. Selezionare **OK**.  

   ![Finestra Avvia runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Nella finestra del processo selezionare il riquadro **Output**. Nella finestra di output attendere che venga visualizzato il messaggio **##### Completed #####** dopo il messaggio **##### Starting #####** . L'avvio di Azure-SSIS IR richiede circa 20 minuti. Chiudere la finestra **Processo** e visualizzare nuovamente la finestra **Runbook**.

   ![Runtime di integrazione SSIS di Azure: avviato](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Ripetere i due passaggi precedenti usando **STOP** come valore per **OPERATION**. Avviare nuovamente il runbook facendo clic sul pulsante **Avvia** sulla barra degli strumenti. Immettere i nomi del gruppo di risorse, di ADF e di Azure-SSIS IR. In **OPERATION** immettere **STOP**. Nella finestra di output attendere che venga visualizzato il messaggio **##### Completed #####** dopo il messaggio **##### Stopping #####** . L'arresto di Azure-SSIS IR non richiede tutto il tempo necessario per l'avvio. Chiudere la finestra **Processo** e visualizzare nuovamente la finestra **Runbook**.

8. È anche possibile attivare la runbook tramite un webhook che può essere creato selezionando la voce di menu **webhook** o in base a una pianificazione che è possibile creare selezionando la voce di menu **pianificazioni** come specificato di seguito.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Creare pianificazioni per consentire al runbook di avviare o arrestare Azure-SSIS IR

Nella sezione precedente è stato creato un runbook di Automazione di Azure capace di avviare o arrestare Azure-SSIS IR. In questa sezione verranno create due pianificazioni per il runbook. Quando si configura la prima pianificazione, è necessario specificare **START** per **OPERATION**. Analogamente, quando si configura la seconda pianificazione, è necessario specificare **STOP** per **OPERATION**. Per informazioni dettagliate sulla procedura di creazione delle pianificazioni, vedere l'articolo [Creazione di una pianificazione](../automation/shared-resources/schedules.md#creating-a-schedule).

1. Nella finestra **Runbook** selezionare **Pianificazioni** e quindi **+ Aggiungi pianificazione** sulla barra degli strumenti. 

   ![Runtime di integrazione SSIS di Azure: avviato](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. Nel riquadro **Pianifica runbook** eseguire le operazioni seguenti: 

    1. Selezionare **Collegare una pianificazione al runbook**. 
    2. Selezionare quindi **Crea una nuova pianificazione**.
    3. Nel riquadro **Nuova pianificazione** immettere **Start IR daily** in **Nome**. 
    4. In **Inizia** immettere un'ora di qualche minuto successiva all'ora corrente. 
    5. In **Ricorrenza** selezionare **Ricorrente**. 
    6. In **Ricorre ogni** immettere **1** e selezionare **Giorno**. 
    7. Selezionare **Create** (Crea). 

   ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Passare alla scheda **parametri e impostazioni di esecuzione** . specificare il gruppo di risorse, il file ADF e i nomi Azure-SSIS IR. In **OPERATION** immettere **START** e fare clic su **OK**. Fare nuovamente clic su **OK** per visualizzare la pianificazione nella pagina **Pianificazioni** del runbook. 

   ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Ripetere i due passaggi precedenti per creare una pianificazione denominata **Stop IR daily**. Immettere un'ora che sia di almeno 30 minuti successiva all'ora specificata per la pianificazione **Start IR daily**. In **OPERATION** immettere **STOP** e fare clic su **OK**. Fare nuovamente clic su **OK** per visualizzare la pianificazione nella pagina **Pianificazioni** del runbook. 

5. Nella finestra **Runbook** selezionare **Processi** nel menu a sinistra. Vengono visualizzati i processi creati dalle pianificazioni, con i relativi stati e la data e l'ora specificate. È possibile visualizzare i dettagli di un processo, ad esempio il relativo output, in modo simile ai dati visualizzati dopo il test del runbook. 

   ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Dopo aver completato il test, disabilitare le pianificazioni modificandole. Selezionare **Pianificazioni** nel menu a sinistra, **Start IR daily/Stop IR daily** e quindi **No** per **Abilitato**. 

## <a name="next-steps"></a>Passaggi successivi
Vedere il post di blog seguente:
-   [Modernize and extend your ETL/ELT workflows with SSIS activities in ADF pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370) (Modernizzare ed estendere i flussi di lavoro ETL/ELT con attività SSIS nelle pipeline di Azure Data Factory)

Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
