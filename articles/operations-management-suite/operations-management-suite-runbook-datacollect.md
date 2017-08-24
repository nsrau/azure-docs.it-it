---
title: Raccolta di dati di Log Analytics con un runbook in Automazione di Azure | Microsoft Docs
description: Esercitazione dettagliata che illustra la creazione di un runbook in Automazione di Azure per la raccolta di dati nel repository OMS per la successiva analisi da parte di Log Analytics.
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Raccogliere dati in Log Analytics con un runbook di Automazione di Azure
È possibile raccogliere una quantità significativa di dati in Log Analytics da una serie di origini, tra cui [origini dati](../log-analytics/log-analytics-data-sources.md) negli agenti e [dati raccolti da Azure](../log-analytics/log-analytics-azure-storage.md).  Esistono tuttavia scenari in cui è necessario raccogliere dati non accessibili tramite queste origini standard.  In questi casi è possibile usare l'[API di raccolta dati HTTP](../log-analytics/log-analytics-data-collector-api.md) per inviare dati a Log Analytics da qualsiasi client dell'API REST.  Un modo comune per eseguire questa raccolta dati è usare un runbook in Automazione di Azure.   

Questa esercitazione illustra il processo di creazione e pianificazione di un runbook in Automazione di Azure per l'invio di dati a Log Analytics.


## <a name="prerequisites"></a>Prerequisiti
Questo scenario richiede la configurazione delle risorse seguenti nella sottoscrizione di Azure.  Per entrambe è possibile usare un account gratuito.

- [Area di lavoro di Log Analytics](../log-analytics/log-analytics-get-started.md).
- [Account di automazione di Azure](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Panoramica dello scenario
Per questa esercitazione si scriverà un runbook che raccoglie informazioni sui processi di automazione.  I runbook in Automazione di Azure vengono implementati con PowerShell, quindi si inizia scrivendo e testando uno script nell'editor di Automazione di Azure.  Dopo aver verificato che vengano raccolte le informazioni necessarie, si invieranno i dati a Log Analytics e si verificherà il tipo di dati personalizzato.  Si creerà infine una pianificazione per avviare il runbook a intervalli regolari.

> [!NOTE]
> È possibile configurare Automazione di Azure per l'invio di informazioni sui processi a Log Analytics senza questo runbook.  Questo scenario viene usato principalmente a supporto dell'esercitazione e si consiglia di inviare i dati a un'area di lavoro di test.  


## <a name="1-install-data-collector-api-module"></a>1. Installare il modulo dell'API di raccolta dati
Ogni [richiesta inviata dall'API di raccolta dati HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) deve essere nel formato corretto e includere un'intestazione dell'autorizzazione.  Questa operazione può essere eseguita nel runbook, ma è possibile ridurre la quantità di codice necessario usando un modulo che semplifica questo processo.  Uno dei moduli che è possibile usare è [OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) in PowerShell Gallery.

Per usare un [modulo](../automation/automation-integration-modules.md) in un runbook, il modulo deve essere installato nell'account di Automazione.  Qualsiasi runbook presente nello stesso account potrà quindi usare le funzioni disponibili nel modulo.  È possibile installare un nuovo modulo selezionando **Asset** > **Moduli** > **Aggiungi un modulo** nell'account di Automazione.  

PowerShell Gallery offre tuttavia un'opzione rapida per distribuire un modulo direttamente nell'account di Automazione, quindi è possibile usare questa opzione per l'esercitazione.  

![Modulo OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Passare a [PowerShell Gallery](https://www.powershellgallery.com/).
2. Cercare **OMSIngestionAPI**.
3. Fare clic sul pulsante **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).
4. Selezionare l'account di Automazione e fare clic su **OK** per installare il modulo.


## <a name="2-create-automation-variables"></a>2. Creare variabili di Automazione
Le [variabili di Automazione](..\automation\automation-variables.md) contengono valori che possono essere usati da tutti i runbook presenti nell'account di Automazione.  Rendono i runbook più flessibili perché consentono di modificare questi valori senza modificare il runbook vero e proprio. Ogni richiesta inviata dall'API di raccolta dati HTTP richiede l'ID e la chiave dell'area di lavoro OMS e gli asset di tipo variabile sono ideali per archiviare queste informazioni.  

![Variabili](media/operations-management-suite-runbook-datacollect/variables.png)

1. Nel portale di Azure passare all'account di Automazione.
2. Selezionare **Variabili** in **Risorse condivise**.
2. Fare clic su **Aggiungi variabile** e creare le due variabili nella tabella seguente.

| Proprietà | Valore ID area di lavoro | Valore chiave area di lavoro |
|:--|:--|:--|
| Nome | WorkspaceId | WorkspaceKey |
| Tipo | String | String |
| Valore | Incollare l'ID dell'area di lavoro di Log Analytics. | Incollare la chiave primaria o secondaria dell'area di lavoro di Log Analytics. |
| Crittografato | No | Sì |



## <a name="3-create-runbook"></a>3. Creare un runbook

Automazione di Azure offre un editor nel portale in cui è possibile modificare e testare il runbook.  È possibile usare l'editor di script [direttamente con PowerShell](../automation/automation-edit-textual-runbook.md) oppure [creare un runbook grafico](../automation/automation-graphical-authoring-intro.md).  Per questa esercitazione si userà uno script di PowerShell. 

![Modificare il runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Passare all'account di Automazione.  
2. Fare clic su **Runbook** > **Aggiungi runbook** > **Crea un nuovo runbook**.
3. Per il nome del runbook digitare **Collect-Automation-jobs**.  Per il tipo di runbook selezionare **PowerShell**.
4. Fare clic su **Crea** per creare il runbook e avviare l'editor.
5. Copiare e incollare il codice seguente nel runbook.  Vedere i commenti nello script per la spiegazione del codice.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Testare il runbook
Automazione di Azure include un ambiente che consente di [testare il runbook](../automation/automation-testing-runbook.md) prima di pubblicarlo.  È possibile esaminare i dati raccolti dal runbook e verificare che il runbook invii i dati a Log Analytics come previsto prima della pubblicazione nell'ambiente di produzione. 
 
![Testare il runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Fare clic su **Salva** per salvare il runbook.
1. Fare clic su **Riquadro di test** per aprire il runbook nell'ambiente di test.
3. Il runbook include parametri, quindi verrà chiesto di inserire i relativi valori.  Immettere il nome del gruppo di risorse e l'account di Automazione dal quale verranno raccolte le informazioni sui processi.
4. Fare clic su **Avvia** per avviare il runbook.
3. Il runbook verrà avviato con lo stato **In coda** prima di passare allo stato **In esecuzione** e  
3. Il runbook visualizzerà un output dettagliato con i processi raccolti in formato JSON.  Se non è elencato alcun processo, probabilmente non sono stati creati processi nell'account di Automazione nell'ultima ora.  Provare ad avviare un runbook nell'account di Automazione e quindi eseguire di nuovo il test.
4. Assicurarsi che l'output non visualizzi eventuali errori nel comando POST per Log Analytics.  Verrà visualizzato un messaggio simile al seguente.

    ![Output del comando POST](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Verificare i record in Log Analytics
Dopo aver completato il test del runbook e aver verificato che l'output sia stato ricevuto correttamente, è possibile verificare che i record siano stati creati usando una [ricerca log in Log Analytics](../log-analytics/log-analytics-log-searches.md).

![Output del log](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Nel portale di Azure selezionare l'area di lavoro di Log Analytics.
2. Fare clic su **Ricerca log**.
3. Digitare il comando `Type=AutomationJob_CL` e fare clic sul pulsante di ricerca. Si noti che il tipo di record include _CL, non specificato nello script.  Tale suffisso viene aggiunto automaticamente al tipo di log per indicare che si tratta di un tipo di log personalizzato.
4. Verranno restituiti uno o più record, a indicare che il runbook funziona come previsto.


## <a name="6-publish-the-runbook"></a>6. Pubblicare il runbook
Dopo aver verificato che il runbook funzioni correttamente, è necessario pubblicarlo per eseguirlo nell'ambiente di produzione.  È possibile continuare a modificare e testare il runbook senza modificare la versione pubblicata.  

![Pubblicare il runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Tornare all'account di Automazione.
2. Fare clic su **Runbook** e selezionare **Collect-Automation-jobs**.
3. Fare clic su **Modifica** e quindi su **Pubblica**.
4. Fare clic su **Sì** quando viene chiesto di confermare l'intenzione di sovrascrivere la versione pubblicata in precedenza.

## <a name="7-set-logging-options"></a>7. Impostare le opzioni di registrazione 
Per l'ambiente di test è stato possibile esaminare l'[output dettagliato](../automation/automation-runbook-output-and-messages.md#message-streams) perché è stata impostata la variabile $VerbosePreference nello script.  Per l'ambiente di produzione è necessario impostare le proprietà di registrazione per il runbook se si intende visualizzare l'output dettagliato.  Per il runbook usato in questa esercitazione, verranno visualizzati i dati JSON inviati a Log Analytics.

![Registrazione e traccia](media/operations-management-suite-runbook-datacollect/logging.png)

1. Nelle proprietà del runbook selezionare **Registrazione e traccia** in **Impostazioni del runbook**.
2. Impostare **Registra record dettagliati** su **Sì**.
3. Fare clic su **Salva**.

## <a name="8-schedule-runbook"></a>8. Pianificare il runbook
Il modo più comune per avviare un runbook che raccoglie dati di monitoraggio è di pianificarne l'esecuzione automatica.  Per eseguire questa operazione, creare una [pianificazione in Automazione di Azure](../automation/automation-schedules.md) e associarla al runbook.

![Pianificare il runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Nelle proprietà del runbook selezionare **Pianificazioni** in **Risorse**.
2. Fare clic su **Aggiungi pianificazione** > **Collegare una pianificazione al runbook** > **Crea una nuova pianificazione**.
5. Immettere i valori seguenti per la pianificazione e fare clic su **Crea**.

| Proprietà | Valore |
|:--|:--|
| Nome | AutomationJobs-Hourly |
| Inizia | Selezionare un orario successivo di almeno 5 minuti all'ora corrente. |
| Ricorrenza | Ricorrente |
| Ricorre ogni | 1 ora |
| Imposta scadenza | No |

Dopo aver creato la pianificazione è necessario impostare i valori dei parametri che verranno usati ogni volta che la pianificazione avvia il runbook.

6. Fare clic su **Configura i parametri e le impostazioni di esecuzione**.
7. Specificare i valori per **ResourceGroupName** e **AutomationAccountName**.
8. Fare clic su **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Verificare che il runbook venga avviato secondo la pianificazione
Ogni volta che un runbook viene avviato, [viene creato un processo](../automation/automation-runbook-execution.md) e l'output viene registrato.  Si tratta in realtà degli stessi processi che vengono raccolti dal runbook.  È possibile verificare che il runbook si avvii come previsto controllando i processi del runbook quando è trascorsa l'ora di inizio della pianificazione.

![Processi](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Nelle proprietà del runbook selezionare **Processi** in **Risorse**.
2. Verrà visualizzato un elenco di processi per ogni volta in cui è stato avviato il runbook.
3. Fare clic su un processo per visualizzarne i dettagli.
4. Fare clic su **Tutti i log** per visualizzare i log e l'output del runbook.
5. Scorrere verso il basso per trovare una voce simile a quella riportata di seguito.<br>![Dettagliato](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Fare clic su questa voce per visualizzare i dati JSON dettagliati che sono stati inviati a Log Analytics.



## <a name="next-steps"></a>Passaggi successivi
- Usare [Progettazione viste](../log-analytics/log-analytics-view-designer.md) per creare una vista con i dati raccolti nel repository di Log Analytics.
- Creare il pacchetto del runbook in una [soluzione di gestione](operations-management-suite-solutions-creating.md) da distribuire ai clienti.
- Altre informazioni su [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Altre informazioni su [Automazione di Azure](https://docs.microsoft.com/azure/automation/).
- Altre informazioni sull'[API di raccolta dati HTTP](../log-analytics/log-analytics-data-collector-api.md).
