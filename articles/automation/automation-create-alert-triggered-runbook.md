---
title: Usare un avviso per attivare un runbook di Automazione di Azure | Microsoft Docs
description: Informazioni su come attivare un runbook da eseguire quando viene generato un avviso di Azure.
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9ea51a85110bb8169dce0a445549e2590c51207e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Usare un avviso per attivare un runbook di Automazione di Azure

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) per monitorare metriche e log di livello base per la maggior parte dei servizi in Azure. È possibile chiamare i runbook di Automazione di Azure tramite [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) o avvisi classici per automatizzare le attività in base agli avvisi. Questo articolo illustra come configurare ed eseguire un runbook usando gli avvisi.

## <a name="alert-types"></a>Tipi di avviso

È possibile usare i runbook di automazione con tre tipi di avviso:
* Avvisi delle metriche classici
* Avvisi dei log attività
* Avvisi delle metriche quasi in tempo reale

Quando un avviso chiama un runbook, la chiamata effettiva è una richiesta HTTP POST al webhook. Il corpo della richiesta POST contiene un oggetto in formato JSON con proprietà utili relative all'avviso. La tabella seguente elenca i collegamenti allo schema del payload per ogni tipo di avviso:

|Avviso  |DESCRIZIONE|Schema del payload  |
|---------|---------|---------|
|[Avviso di metrica classico](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Invia una notifica quando qualsiasi metrica a livello di piattaforma soddisfa una condizione specifica. Ad esempio, quando il valore per **% CPU** in una macchina virtuale è maggiore di **90** per gli ultimi 5 minuti.| [Schema payload avviso metrica classico](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Avviso del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Invia una notifica quando qualsiasi nuovo evento nel log attività di Azure soddisfa condizioni specifiche. Ad esempio, quando si verifica un'operazione `Delete VM` in **myProductionResourceGroup** o quando viene visualizzato un nuovo evento di integrità dei servizi di Azure con uno stato **Attivo**.| [Schema payload avviso log attività](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Avvisi delle metriche quasi in tempo reale](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Invia una notifica più velocemente rispetto agli avvisi delle metriche quando una o più metriche a livello di piattaforma soddisfano le condizioni specificate. Ad esempio, quando il valore per **% CPU** in una macchina virtuale è maggiore di **90**e il valore per **Rete in ingresso** è maggiore di **500 MB** per gli ultimi 5 minuti.| [Schema payload avvisi metriche quasi in tempo reale](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Poiché i dati forniti da ogni tipo di avviso sono diversi, ogni tipo di avviso deve essere gestito in modo diverso. Nella sezione seguente viene illustrato come creare un runbook per gestire i diversi tipi di avvisi.

## <a name="create-a-runbook-to-handle-alerts"></a>Creare un runbook per gestire gli avvisi

Per usare l'Automazione di Azure con gli avvisi, è necessario un runbook con la logica per gestire il payload JSON dell'avviso passato al runbook. Il seguente runbook di esempio deve essere chiamato da un avviso di Azure. 

Come descritto nella sezione precedente, ogni tipo di avviso ha uno schema diverso. Lo script accetta i dati webhook nel parametro di input runbook `WebhookData` da un avviso. Quindi, lo script valuta il payload JSON per determinare il tipo di avviso usato. 

Questo esempio usa un avviso generato da una macchina virtuale. Recupera i dati della macchina virtuale dal payload e usa le informazioni per arrestare la macchina virtuale. La connessione deve essere configurata nell'account di Automazione in cui è eseguito il runbook.

Il runbook usa l'[account RunAs](automation-create-runas-account.md) **AzureRunAsConnection** per l'autenticazione in Azure per eseguire l'azione di gestione sulla macchina virtuale.

Usare questo esempio per creare un runbook denominato **Stop AzureVmInResponsetoVMAlert**. È possibile modificare lo script di PowerShell e usarlo con molte risorse diverse.

1. Accedere all'account di Automazione di Azure.
1. In **AUTOMAZIONE PROCESSI** selezionare **Runbook**.
1. Nella parte superiore dell'elenco di runbook, selezionare **Aggiungi runbook**. 
1. Nella pagina **Aggiungi runbook** selezionare **Creazione rapida**.
1. Immettere **Stop-AzureVmInResponsetoVMAlert** come nome del runbook. Per il tipo di runbook selezionare **PowerShell**. Scegliere quindi **Create** (Crea).  
1. Copiare l'esempio di PowerShell seguente nel riquadro **Modifica**. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Selezionare **Pubblica** per salvare e pubblicare il runbook.

## <a name="create-an-action-group"></a>Creare un gruppo di azioni

Un gruppo di azioni è una raccolta di azioni che vengono attivate da un avviso. I runbook sono solo una delle molte azioni disponibili con i gruppi di azioni.

1. Nel portale di Azure selezionare **Monitoraggio** (Monitoraggio) > **IMPOSTAZIONI** > **Gruppi di azioni**.
1. Selezionare **Aggiungi gruppo di azioni**, quindi immettere le informazioni obbligatorie:  
    1. Nella casella **Nome gruppo di azione** immettere un nome.
    1. Nella casella **Nome breve** immettere un nome. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo di azione.
    1. Nella casella **Sottoscrizione** viene inserita automaticamente la sottoscrizione corrente. Si tratta della sottoscrizione in cui viene salvato il gruppo di azione.
    1. Selezionare il gruppo di risorse in cui viene salvato il gruppo di azioni.

Per questo esempio vengono create due azioni, un'azione del runbook e un'azione di notifica.

### <a name="runbook-action"></a>Azione del runbook

Per creare un'azione del runbook nel gruppo di azioni:

1. In **Actions** (Azioni) inserire un nome per l'azione in **NOME AZIONE**. Per **TIPO DI AZIONE** selezionare **Runbook di Automazione**.
1. In **DETTAGLI** selezionare **Modifica dettagli**.  
1. Alla pagina **Configura runbook** selezionare **Utente** in **Origine runbook**.  
1. Selezionare la **sottoscrizione** e l'**account di Automazione**, poi il runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Al termine dell'operazione, scegliere **OK**.

### <a name="notification-action"></a>Azione di notifica

Per creare un'azione di notifica nel gruppo di azioni:

1. In **Actions** (Azioni) inserire un nome per l'azione in **NOME AZIONE**. Per **TIPO DI AZIONE**selezionare **Email** (Posta elettronica).  
1. In **DETTAGLI** selezionare **Modifica dettagli**.  
1. Nella pagina **Email** immettere l'indirizzo di posta elettronica da usare per la notifica e quindi selezionare **OK**. È utile aggiungere un indirizzo di posta elettronica come azione oltre al runbook. All'avvio del runbook, si riceve una comunicazione.  

    Il gruppo di azioni dovrebbe essere simile all'immagine seguente:

   ![Pagina Aggiungi gruppo di azioni](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Per creare il gruppo di azione, selezionare **OK**.

È possibile usare questo gruppo di azione negli [avvisi dei log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e negli [avvisi quasi in tempo reale](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) che vengono creati.

## <a name="classic-alert"></a>Avviso classico

Gli avvisi classici sono basati sulle metriche e non usano gruppi di azioni, ma è possibile configurare le azioni del runbook in base a tali avvisi. 

Per creare un avviso classico:

1. Selezionare **Aggiungi avviso per la metrica**.
1. Assegnare all'avviso per la metrica il nome **myVMCPUAlert**. Immettere una breve descrizione per l'avviso.
1. Per la condizione di avviso per la metrica, selezionare **Maggiore di**. Per il valore **Threshold** (Soglia), selezionare **10**. Per il valore **Periodo**, selezionare **Over the last five minutes** (Negli ultimi cinque minuti).
1. In **Intervieni** selezionare **Esegui runbook da questo avviso**.
1. Alla pagina **Configura runbook** selezionare **Utente** in **Origine runbook**. Scegliere l'account di Automazione e selezionare il runbook **Stop-AzureVmInResponsetoVMAlert**. Selezionare **OK**.
1. Per salvare la regola di avviso, selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'avvio di runbook di Automazione con un webhook, vedere [Avviare un runbook da un webhook](automation-webhooks.md).
* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per informazioni su come creare un avviso del log attività, vedere [Creare avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Per informazioni su come creare un avviso quasi in tempo reale, vedere [Creare una regola di avviso con il portale di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
