---
title: Come pianificare un runtime di integrazione SSIS di Azure | Microsoft Docs
description: Questo articolo descrive come pianificare l'avvio e l'arresto di un runtime di integrazione SSIS di Azure usando Automazione di Azure e Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: 814ef63f317c2c0c9081579c16a12a908c05ff74
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Come pianificare l'avvio e l'arresto di un runtime di integrazione SSIS di Azure 
Eseguire un runtime di integrazione SSIS (SQL Server Integration Services) di Azure ha un costo. Di conseguenza, è opportuno eseguire il runtime di integrazione solo quando si devono eseguire pacchetti SSIS in Azure ed è consigliabile arrestarlo quando non è necessario. Per [avviare o arrestare manualmente un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md), è possibile usare l'interfaccia utente di Data Factory o Azure PowerShell. Questo articolo descrive come pianificare l'avvio e l'arresto di un runtime di integrazione SSIS di Azure usando Automazione di Azure e Azure Data Factory. Di seguito sono riportate le procedure generali descritte in questo articolo:

1. **Creare e testare un runbook di Automazione di Azure**. Questa procedura consente di creare un runbook PowerShell con lo script necessario per avviare o arrestare un runtime di integrazione SSIS di Azure. Successivamente, il runbook viene testato in scenari sia START che STOP per verificare che il runtime di integrazione venga avviato e arrestato. 
2. **Creare due pianificazioni per il runbook**. Per la prima pianificazione il runbook viene configurato con START come operazione, mentre per la seconda pianificazione viene configurato con STOP come operazione. Per entrambe le pianificazioni è necessario specificare la frequenza di esecuzione del runbook. Ad esempio, può essere opportuno eseguire la prima pianificazione ogni giorno alle 8 e la seconda ogni giorno alle 23. Quando si esegue il primo runbook il runtime di integrazione SSIS di Azure viene avviato, mentre quando si esegue il secondo runbook il runtime viene interrotto. 
3. **Creare due webhook per il runbook**, uno per l'operazione START e l'altro per l'operazione STOP. Gli URL di questi webhook vengono usati quando si configurano attività Web in una pipeline di Data Factory. 
4. **Creare una pipeline di Data Factory**. La pipeline che si crea è costituita da quattro attività. La prima attività **Web** richiama il primo webhook per l'avvio del runtime di integrazione SSIS di Azure. L'attività **Attesa** attende l'avvio del runtime di integrazione SSIS di Azure per 30 minuti (1800 secondi). L'attività **Stored procedure** esegue uno script SQL per eseguire il pacchetto SSIS. La seconda attività **Web** arresta il runtime di integrazione SSIS di Azure. Per altre informazioni sulla chiamata di un pacchetto SSIS da una pipeline di Data Factory usando l'attività Stored procedure, vedere [Chiamare un pacchetto SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Successivamente, è necessario creare un trigger per pianificare l'esecuzione della pipeline con una frequenza specificata.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Chiamare pacchetti SSIS usando l'attività stored procedure nella versione 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>prerequisiti
Se non è stato ancora eseguito il provisioning di un runtime di integrazione SSIS di Azure, eseguire questa operazione seguendo le istruzioni visualizzate nell'[esercitazione](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Creare e testare un runbook di Automazione di Azure
In questa sezione si segue questa procedura: 

1. Creare un account di Automazione di Azure.
2. Creare un runbook PowerShell nell'account di Automazione di Azure. Lo script PowerShell associato al runbook avvia o arresta un runtime di integrazione SSIS di Azure in base al comando specificato per il parametro OPERATION. 
3. Testare il runbook in scenari sia START che STOP per verificarne il funzionamento. 

### <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure
Se non si ha un account di Automazione di Azure, crearne uno seguendo le istruzioni riportate in questa procedura. Per informazioni dettagliate sulla procedura, vedere [Creare un account di Automazione di Azure](../automation/automation-quickstart-create-account.md). Nell'ambito di questa procedura viene creato un account **RunAs di Azure** (un'entità servizio in Azure Active Directory) e aggiunto al ruolo **Collaboratore** della sottoscrizione di Azure. Assicurarsi che si tratti della stessa sottoscrizione che include la data factory con il runtime di integrazione SSIS di Azure. Automazione di Azure usa questo account per eseguire l'autenticazione in Azure Resource Manager e intervenire sulle risorse. 

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Accedere al [Portale di Azure](https://portal.azure.com/).    
3. Selezionare **Nuovo** nel menu a sinistra, **Monitoraggio e gestione** e quindi **Automazione**. 

    ![Nuovo -> Monitoraggio e gestione -> Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. Nella finestra **Aggiungi account di Automazione** eseguire questa procedura: 

    1. Specificare un **nome** per l'account di Automazione. 
    2. Selezionare la **sottoscrizione** che include la data factory con il runtime di integrazione SSIS di Azure. 
    3. In **Gruppo di risorse** selezionare **Crea nuovo** per creare un nuovo gruppo di risorse oppure **Usa esistente** per selezionare un gruppo di risorse esistente. 
    4. Selezionare una **località** per l'account di Automazione. 
    5. Verificare che **Crea un account RunAs di Azure** sia impostato su **Sì**. In Azure Active Directory viene creata un'entità servizio e aggiunta al ruolo **Collaboratore** della sottoscrizione di Azure.
    6. Selezionare **Aggiungi al dashboard** in modo da visualizzare l'entità nel dashboard del portale. 
    7. Selezionare **Create**. 

        ![Nuovo -> Monitoraggio e gestione -> Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Lo **stato della distribuzione** verrà visualizzato nel dashboard e nelle notifiche. 
    
    ![Distribuzione dell'automazione](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. La home page dell'account di Automazione viene visualizzata dopo la creazione dell'account. 

    ![Home page di Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importare i moduli di Data Factory

1. Selezionare **Moduli** nella sezione **RISORSE CONDIVISE** del menu a sinistra e verificare se nell'elenco dei moduli sono presenti **AzureRM.Profile** e **AzureRM.DataFactoryV2**. Se non sono presenti, selezionare **Esplora raccolta** sulla barra degli strumenti.

    ![Home page di Automazione](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. Nella finestra **Esplora raccolta** digitare **AzureRM.Profile** nella finestra di ricerca e premere **INVIO**. Selezionare **AzureRM.Profile** nell'elenco e quindi fare clic su **Importa** sulla barra degli strumenti. 

    ![Selezionare AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. Nella finestra **Importa** selezionare l'opzione **Accetto di aggiornare tutti i moduli di Azure** e fare clic su **OK**.  

    ![Importare AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Chiudere le finestre per visualizzare nuovamente la finestra **Moduli**. Nell'elenco dovrebbe essere visibile lo stato dell'importazione. Selezionare **Aggiorna** per aggiornare l'elenco. Attendere fino a quando non viene visualizzato **Disponibile** nella colonna **STATO**.

    ![Stato dell'importazione](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Ripetere la procedura per importare il modulo **AzureRM.DataFactoryV2**. Prima di continuare, verificare che lo stato di questo modulo sia impostato su **Disponibile**. 

    ![Stato dell'importazione finale](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### <a name="create-a-powershell-runbook"></a>Creare un runbook di PowerShell
La procedura seguente descrive le operazioni necessarie per creare un runbook PowerShell. Lo script associato al runbook avvia o arresta un runtime di integrazione SSIS di Azure in base al comando specificato per il parametro **OPERATION**. Questa sezione non riporta tutti i dettagli per la creazione di un runbook. Per altre informazioni, vedere [Creare un runbook](../automation/automation-quickstart-create-runbook.md).

1. Passare alla scheda **Runbook** e selezionare **+ Aggiungi runbook** nella barra degli strumenti. 

    ![Pulsante Aggiungi runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Selezionare **Crea un nuovo runbook** e seguire questa procedura: 

    1. In **Nome** digitare **StartStopAzureSsisRuntime**.
    2. In **Tipo di runbook** selezionare **PowerShell**.
    3. Selezionare **Create**.
    
        ![Pulsante Aggiungi runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Copiare e incollare lo script seguente nella finestra di script del runbook. Salvare e pubblicare il runbook usando i pulsanti **Salva** e **Pubblica** sulla barra degli strumenti. 

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
        Add-AzureRmAccount `
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
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Modificare il runbook PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Testare il runbook selezionando il pulsante **Avvia** sulla barra degli strumenti. 

    ![Pulsante di avvio del runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. Nella finestra **Avvia runbook** seguire questa procedura: 

    1. In **RESOURCE GROUP NAME** immettere il nome del gruppo di risorse con la data factory che contiene il runtime di integrazione SSIS di Azure. 
    2. In **DATA FACTORY NAME** immettere il nome della data factory che contiene il runtime di integrazione SSIS di Azure. 
    3. In**AZURESSISNAME** immettere il nome del runtime di integrazione SSIS di Azure. 
    4. In **OPERATION** immettere **START**. 
    5. Selezionare **OK**.  

        ![Finestra Avvia runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. Nella finestra del processo selezionare il riquadro **Output**. Nella finestra di output del processo attendere fino a quando non viene visualizzato il messaggio **##### Completed #####** dopo il messaggio **##### Starting #####**. L'avvio di un runtime di integrazione SSIS di Azure richiede circa 20 minuti. Chiudere la finestra **Processo** e tornare alla finestra **Runbook**.

    ![Runtime di integrazione SSIS di Azure: avviato](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Ripetere i due passaggi precedenti usando **STOP** come valore per il parametro **OPERATION**. Avviare nuovamente il runbook selezionando il pulsante **Avvia** sulla barra degli strumenti. Specificare il nome del gruppo di risorse, il nome della data factory e il nome del runtime di integrazione SSIS di Azure. In **OPERATION** immettere **STOP**. 

    Nella finestra di output del processo attendere fino a quando non viene visualizzato il messaggio **##### Completed #####** dopo il messaggio **##### Stopping #####**. L'arresto di un runtime di integrazione SSIS di Azure non richiede tutto il tempo necessario per l'avvio dello stesso runtime. Chiudere la finestra **Processo** e visualizzare nuovamente la finestra **Runbook**.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Creare pianificazioni per consentire al runbook di avviare o arrestare il runtime di integrazione SSIS di Azure
Nella sezione precedente è stato creato un runbook di Automazione di Azure capace di avviare o arrestare un runtime di integrazione SSIS di Azure. In questa sezione vengono create due pianificazioni per il runbook. Quando si configura la prima pianificazione, è necessario specificare START per il parametro OPERATION. Analogamente, quando si configura la seconda pianificazione, è necessario specificare STOP per il parametro OPERATION. Per informazioni dettagliate sulla procedura di creazione delle pianificazioni, vedere [Creazione di una pianificazione](../automation/automation-schedules.md#creating-a-schedule).

1. Nella finestra **Runbook** selezionare **Pianificazioni** e quindi **+ Aggiungi pianificazione** sulla barra degli strumenti. 

    ![Runtime di integrazione SSIS di Azure: avviato](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. Nella finestra **Pianifica runbook** seguire questa procedura: 

    1. Selezionare **Collegare una pianificazione al runbook**. 
    2. Selezionare quindi **Crea una nuova pianificazione**.
    3. Nella finestra **Nuova pianificazione** digitare **Start IR daily** per **Nome**. 
    4. Nella sezione **Inizia**, relativa alla data e all'ora, specificare un valore di pochi minuti successivo alla data e all'ora correnti. 
    5. In **Ricorrenza** selezionare **Ricorrente**. 
    6. Nella sezione **Ricorre ogni** selezionare **Giorno**. 
    7. Selezionare **Create**. 

        ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Passare alla scheda **Parametri e impostazioni di esecuzione**. Specificare il nome del gruppo di risorse, il nome della data factory e il nome del runtime di integrazione SSIS di Azure. In **OPERATION** immettere **START**. Selezionare **OK**. Selezionare nuovamente **OK** per visualizzare la pianificazione nella pagina **Pianificazioni** del runbook. 

    ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Ripetere i due passaggi precedenti per creare una pianificazione denominata **Stop IR daily**. Questa volta specificare un valore che sia almeno 30 minuti successivo alla data e all'ora indicate per la pianificazione **Start IR daily**. In **OPERATION** specificare **STOP**. 
5. Nella finestra **Runbook** selezionare **Processi** nel menu a sinistra. Vengono visualizzati i processi creati dalle pianificazioni, con i relativi stati e la data e l'ora specificate. È possibile visualizzare i dettagli del processo, ad esempio l'output simile a quello visualizzato durante il test del runbook. 

    ![Pianificazione per l'avvio del runtime di integrazione SSIS di Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Dopo aver completato il test, disabilitare le pianificazioni modificandole e selezionando **NO** per **Abilitate**. Selezionare **Pianificazioni** nel menu a sinistra, **Start IR daily/Stop IR daily** e quindi **NO** per **Abilitate**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Creare webhook per avviare e arrestare il runtime di integrazione SSIS di Azure
Per creare due webhook per il runbook, seguire le istruzioni riportate in [Creazione di un webhook](../automation/automation-webhooks.md#creating-a-webhook). Per il primo webhook specificare START come OPERATION e per il secondo specificare STOP. Salvare gli URL dei due webhook in un punto, ad esempio in un file di testo o in un blocco appunti di OneNote. Questi URL vengono usati quando si configurano attività Web in una pipeline di Data Factory. L'immagine seguente illustra un esempio di creazione di un webhook che avvia il runtime di integrazione SSIS di Azure:

1. Nella finestra **Runbook** selezionare **Webhook** nel menu a sinistra e quindi **+ Aggiungi webhook** sulla barra degli strumenti. 

    ![Webhook -> Aggiungi webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. Nella finestra **Aggiungi webhook** selezionare **Creare un nuovo webhook** ed eseguire le operazioni seguenti: 

    1. In **Nome** immettere **StartAzureSsisIR**. 
    2. Verificare che **Abilitato** sia impostato su **Sì**. 
    3. Copiare l'**URL** e salvarlo in un punto. Questo passaggio è importante. L'URL, infatti, non verrà visualizzato successivamente. 
    4. Selezionare **OK**. 

        ![Finestra del nuovo webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Passare alla scheda **Parametri e impostazioni di esecuzione**. Specificare il nome del gruppo di risorse, il nome della data factory e il nome del runtime di integrazione SSIS di Azure. In **OPERATION** immettere **START**. Fare clic su **OK**. Fare quindi clic su **Crea**. 

    ![Webhook: parametri e impostazioni di esecuzione](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Ripetere i tre passaggi precedenti per creare un altro webhook denominato **StopAzureSsisIR**. Non dimenticare di copiare l'URL. Quando si specificano i parametri e le impostazioni di esecuzione, immettere **STOP** per **OPERATION**. 

È necessario avere due URL, uno per il webhook **StartAzureSsisIR** e l'altro per il webhook **StopAzureSsisIR**. È possibile inviare una richiesta POST HTTP a questi URL per avviare o arrestare il runtime di integrazione SSIS di Azure. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Creare e pianificare una pipeline di Data Factory che avvia o interrompe il runtime di integrazione
Questa sezione illustra come usare un'attività Web per richiamare i webhook creati nella sezione precedente.

La pipeline che si crea è costituita da quattro attività. 

1. La prima attività **Web** richiama il primo webhook per l'avvio del runtime di integrazione SSIS di Azure. 
2. L'attività **Attesa** attende l'avvio del runtime di integrazione SSIS di Azure per 30 minuti (1800 secondi). 
3. L'attività **Stored procedure** esegue uno script SQL per eseguire il pacchetto SSIS. La seconda attività **Web** arresta il runtime di integrazione SSIS di Azure. Per altre informazioni sulla chiamata di un pacchetto SSIS da una pipeline di Data Factory usando l'attività Stored procedure, vedere [Chiamare un pacchetto SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). 
4. La seconda attività **Web** richiama il webhook per l'arresto del runtime di integrazione SSIS di Azure. 

Dopo aver creato e testato la pipeline, è necessario creare un trigger di pianificazione e associarlo alla pipeline. Questo trigger definisce una pianificazione per la pipeline. Si supponga di creare un trigger la cui esecuzione è pianificata ogni giorno alle 23. Il trigger esegue la pipeline ogni giorno alle 23. La pipeline avvia il runtime di integrazione SSIS di Azure, esegue il pacchetto SSIS e quindi arresta il runtime. 

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Accedere al [Portale di Azure](https://portal.azure.com/).    
2. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Nella pagina **Nuova data factory** immettere **MyAzureSsisDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteMyAzureSsisDataFactory, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco vengono mostrate solo le località supportate per la creazione di data factory.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Fare clic su **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

### <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Pagina delle attività iniziali](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Web** nell'area di progettazione della pipeline. Nella scheda **Generale** della finestra **Proprietà** modificare il nome dell'attività in **StartIR**.

   ![Prima attività Web: scheda Generale](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Passare alla scheda **Impostazioni** nella finestra **Proprietà** ed eseguire le operazioni seguenti: 

    1. In **URL** incollare l'URL del webhook che avvia il runtime di integrazione SSIS di Azure. 
    2. In **Metodo** selezionare **POST**. 
    3. In **Corpo** immettere `{"message":"hello world"}`. 
   
        ![Prima attività Web: scheda Impostazioni](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)
4. Nella casella degli strumenti **Attività** espandere **Iteration & Conditionals** (Iterazione e istruzioni condizionali) e trascinare l'attività **Attesa** nell'area di progettazione della pipeline. Nella scheda **Generale** modificare il nome dell'attività in **WaitFor30Minutes**. 
5. Passare alla scheda **Impostazioni** nella finestra **Proprietà**. In **Wait time in seconds** (Tempo di attesa in secondi) immettere **1800**. 
6. Collegare le attività **Web** e **Attesa**. A tale scopo, iniziare il trascinamento partendo dal quadrato verde collegato all'attività Web fino ad arrivare all'attività Attesa. 

    ![Collegare Web e Attesa](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-wait.png)
5. Trascinare l'attività Stored procedure dalla sezione **Generale** della casella degli strumenti **Attività**. Assegnare all'attività il nome **RunSSISPackage**. 
6. Passare alla scheda **Account SQL** nella finestra **Proprietà**. 
7. In **Servizio collegato** fare clic su **+ Nuovo**.
8. Nella finestra **New Linked Service** (Nuovo servizio collegato) eseguire le operazioni seguenti: 

    1. Selezionare **Database SQL di Azure** per **Tipo**.
    2. Selezionare il server SQL di Azure che ospita il database **SSISDB** per il campo **Nome server**. Il processo di provisioning del runtime di integrazione SSIS di Azure crea un catalogo SSIS (database SSISDB) nel server SQL di Azure specificato.
    3. Selezionare **SSISDB** per **Nome database**.
    4. Per **Nome utente** immettere il nome dell'utente che ha accesso al database.
    5. Per **Password** immettere la password dell'utente. 
    6. Testare la connessione al database facendo clic sul pulsante **Test connessione**.
    7. Salvare il servizio collegato facendo clic sul pulsante **Salva**.
1. Nella finestra **Proprietà** passare alla scheda **Stored procedure** dalla scheda **SQL Account** (Account SQL) e seguire questa procedura: 

    1. In **Nome stored procedure** selezionare l'opzione **Modifica** e immettere **sp_executesql**. 
    2. Fare clic su **+ Nuovo** nella sezione **Parametri stored procedure**. 
    3. Per il **nome** del parametro, immettere **stmt**. 
    4. Per il **tipo** del parametro, immettere **Stringa**. 
    5. Per il **valore** del parametro, immettere la query SQL seguente.

        Nella query SQL specificare i valori corretti per i parametri **folder_name**, **project_name** e **package_name**. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END   
        ```
10. Collegare l'attività **Attesa** all'attività **Stored procedure**. 

    ![Collegare le attività Attesa e Stored procedure](./media/how-to-schedule-azure-ssis-integration-runtime/connect-wait-sproc.png)
11. Trascinare l'attività **Web** a destra dell'attività **Stored procedure**. Assegnare all'attività il nome **StopIR**. 
12. Passare alla scheda **Impostazioni** nella finestra **Proprietà** ed eseguire le operazioni seguenti: 

    1. In **URL** incollare l'URL del webhook che arresta il runtime di integrazione SSIS di Azure. 
    2. In **Metodo** selezionare **POST**. 
    3. In **Corpo** immettere `{"message":"hello world"}`.  
4. Collegare l'attività **Stored procedure** all'ultima attività **Web**.

    ![Pipeline completa](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
5. Per convalidare le impostazioni della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Chiudere il **report di convalida della pipeline** facendo clic sul pulsante **>>**. 

    ![Convalidare la pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Eseguire test della pipeline

1. Fare clic su **Esecuzione dei test** sulla barra degli strumenti della pipeline. L'output verrà visualizzato nella finestra **Output** nel riquadro in basso. 

    ![Esecuzione dei test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Nella pagina **Runbook** dell'account di Automazione di Azure è possibile verificare che i processi per l'avvio e l'arresto del runtime di integrazione SSIS di Azure siano stati eseguiti. 

    ![Processi del runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Avviare SQL Server Management Studio. Nella finestra **Connetti al server** eseguire le operazioni seguenti: 

    1. In **Nome server** specificare **&lt;database SQL di Azure&gt;.database.windows.net**.
    2. Selezionare **Opzioni >>**.
    3. In **Connetti al database** selezionare **SSISDB**.
    4. Selezionare **Connessione**. 
    5. Espandere **Cataloghi di Integration Services** -> **SSISDB** -> Cartella -> **Progetti** -> Progetto SSIS -> **Pacchetti**. 
    6. Fare clic con il pulsante destro del mouse sul pacchetto SSIS e selezionare **Report** -> **Report standard** -> **Tutte le esecuzioni**. 
    7. Verificare che il pacchetto SSIS sia stato eseguito. 

        ![Verificare l'esecuzione del pacchetto SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Pianificare la pipeline 
Ora che la pipeline funziona come previsto, è possibile creare un trigger per l'esecuzione della pipeline con una frequenza specificata. Per informazioni dettagliate sull'associazione di un trigger di pianificazione a una pipeline, vedere [Attivare la pipeline in base a una pianificazione](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Sulla barra degli strumenti della pipeline fare clic su **Trigger** e selezionare **New/Edit** (Nuovo/Modifica). 

    ![Creazione o modifica di un trigger](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. Nella finestra **Add Triggers** (Aggiungi trigger) selezionare **+ Nuovo**.

    ![Aggiunta di un nuovo trigger](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. Nella finestra **Nuovo trigger** eseguire le operazioni seguenti: 

    1. In **Nome** specificare un nome per il trigger. Nell'esempio seguente il nome del trigger è **Run daily**. 
    2. In **Tipo** selezionare **Pianificazione**. 
    3. In **Data di inizio** selezionare una data e un'ora di inizio. 
    4. In **Ricorrenza** specificare la frequenza di esecuzione del trigger. Nell'esempio seguente la frequenza è una volta al giorno. 
    5. In **Fine** è possibile specificare la data e l'ora selezionando l'opzione **On Date** (In data). 
    6. Selezionare **Attivato**. Il trigger viene attivato immediatamente dopo aver pubblicato la soluzione in Data Factory. 
    7. Selezionare **Avanti**.

        ![Creazione o modifica di un trigger](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Nella pagina **Trigger Run Parameters** (Parametri esecuzione trigger) rivedere l'avviso e fare clic su **Fine**. 
5. Pubblicare la soluzione in Data Factory facendo clic su **Pubblica tutti** nel riquadro a sinistra. 

    ![Pubblica tutti](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Per monitorare le esecuzioni di trigger e pipeline, usare la scheda **Monitoraggio** a sinistra. Per informazioni dettagliate sulla procedura, vedere [Monitorare la pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Esecuzioni di pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul primo collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Verranno visualizzate le quattro esecuzioni di attività associate a ogni attività della pipeline (prima attività Web, attività Attesa, attività Stored procedure e seconda attività Web). Per visualizzare nuovamente le esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto.

    ![Esecuzioni attività](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. È anche possibile visualizzare le esecuzioni di trigger selezionando **Trigger runs** (Esecuzioni di trigger) nell'elenco a discesa accanto all'opzione **Pipeline Runs** (Esecuzioni di pipeline) in alto. 

    ![Esecuzioni di trigger](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti della documentazione relativa a SSIS: 

- [Distribuire, eseguire e monitorare un pacchetto SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connettersi al catalogo SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pianificare l'esecuzione di pacchetti in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connettersi a origini dati locali con autenticazione di Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)