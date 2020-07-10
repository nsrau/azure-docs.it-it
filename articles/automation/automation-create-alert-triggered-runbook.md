---
title: Usare un avviso per attivare un runbook di Automazione di Azure
description: Questo articolo descrive come attivare un runbook da eseguire quando viene generato un avviso di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: acf31af6d3ba3d78a6435210fa17562aaddac0a3
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186606"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Usare un avviso per attivare un runbook di Automazione di Azure

È possibile usare [Monitoraggio di Azure](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) per monitorare metriche e log di livello base per la maggior parte dei servizi in Azure. È possibile chiamare i runbook di Automazione di Azure tramite [gruppi di azioni](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) o avvisi classici per automatizzare le attività in base agli avvisi. Questo articolo illustra come configurare ed eseguire un runbook usando gli avvisi.

## <a name="alert-types"></a>Tipi di avviso

È possibile usare i runbook di automazione con tre tipi di avviso:

* Avvisi comuni
* Avvisi dei log attività
* Avvisi di metriche quasi in tempo reale

> [!NOTE]
> Lo schema di avviso comune standardizza attualmente l'esperienza di utilizzo per le notifiche di avviso in Azure. I tre tipi di avviso attualmente disponibili in Azure (metrica, log e log attività) hanno avuto storicamente modelli di posta elettronica, schemi webhook e così via. Per altre informazioni, vedere [Schema di avviso comune](../azure-monitor/platform/alerts-common-schema.md)

Quando un avviso chiama un runbook, la chiamata effettiva è una richiesta HTTP POST al webhook. Il corpo della richiesta POST contiene un oggetto in formato JSON con proprietà utili relative all'avviso. La tabella seguente elenca i collegamenti allo schema del payload per ogni tipo di avviso:

|Avviso  |Descrizione|Schema del payload  |
|---------|---------|---------|
|[Avviso comune](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Lo schema di avviso comune che standardizza attualmente l'esperienza di utilizzo per le notifiche di avviso in Azure.|Schema del payload di avviso comune|
|[Avviso del log attività](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Invia una notifica quando qualsiasi nuovo evento nel log attività di Azure soddisfa condizioni specifiche. Ad esempio, quando si verifica un'operazione `Delete VM` in **myProductionResourceGroup** o quando viene visualizzato un nuovo evento di integrità dei servizi di Azure con uno stato Attivo.| [Schema payload avviso log attività](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Avvisi delle metriche quasi in tempo reale](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Invia una notifica più velocemente rispetto agli avvisi delle metriche quando una o più metriche a livello di piattaforma soddisfano le condizioni specificate. Ad esempio, quando il valore per **% CPU** in una macchina virtuale è maggiore di 90 e il valore per **Rete in ingresso** è maggiore di 500 MB per gli ultimi 5 minuti.| [Schema payload avvisi metriche quasi in tempo reale](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Poiché i dati forniti da ogni tipo di avviso sono diversi, ogni tipo di avviso deve essere gestito in modo diverso. Nella sezione seguente viene illustrato come creare un runbook per gestire i diversi tipi di avvisi.

## <a name="create-a-runbook-to-handle-alerts"></a>Creare un runbook per gestire gli avvisi

Per usare l'Automazione di Azure con gli avvisi, è necessario un runbook con la logica per gestire il payload JSON dell'avviso passato al runbook. Il seguente runbook di esempio deve essere chiamato da un avviso di Azure.

Come descritto nella sezione precedente, ogni tipo di avviso ha uno schema diverso. Lo script accetta i dati del webhook da un avviso nel parametro di input runbook `WebhookData`. Lo script valuta quindi il payload JSON per determinare il tipo di avviso usato.

Questo esempio usa un avviso generato da una macchina virtuale. Recupera i dati della macchina virtuale dal payload e usa le informazioni per arrestare la macchina virtuale. La connessione deve essere configurata nell'account di Automazione in cui è eseguito il runbook. Quando si usano gli avvisi per attivare i runbook, è importante controllare lo stato dell'avviso nel runbook che viene attivato. Il runbook viene attivato ogni volta che lo stato dell'avviso cambia. Gli avvisi hanno più stati; i due più comuni sono Attivato e Risolto. Cercare lo stato nella logica del runbook per assicurarsi che il runbook non venga eseguito più volte. L'esempio in questo articolo illustra come cercare solo gli avvisi con stato Attivato.

Il runbook usa l'[account RunAs](./manage-runas-account.md) `AzureRunAsConnection` dell'asset di connessione per l'autenticazione in Azure per eseguire l'azione di gestione sulla macchina virtuale.

Usare questo esempio per creare un runbook denominato **Stop AzureVmInResponsetoVMAlert**. È possibile modificare lo script di PowerShell e usarlo con molte risorse diverse.

1. Accedere all'account di Automazione di Azure.
2. In **Automazione processi** selezionare **Runbook**.
3. Nella parte superiore dell'elenco di runbook, selezionare **+ Crea un runbook**.
4. Nella pagina **Aggiungi Runbook** immettere **Stop-AzureVmInResponsetoVMAlert** per il nome runbook. Per il tipo di runbook selezionare **PowerShell**. Scegliere quindi **Create** (Crea).  
5. Copiare l'esempio di PowerShell seguente nella pagina **Modifica**.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Selezionare **Pubblica** per salvare e pubblicare il runbook.

## <a name="create-the-alert"></a>Creare l'avviso

Gli avvisi usano i gruppi di azioni, ovvero raccolte di azioni attivate dall'avviso. I runbook sono solo una delle molte azioni disponibili con i gruppi di azioni.

1. Nell'account di Automazione selezionare **Avvisi** in **Monitoraggio**.
1. Fare clic su **+ Nuova regola di avviso**.
1. Fare clic su **Seleziona** in **Risorsa**. Nella pagina **Selezionare una risorsa** selezionare la VM di cui inviare un avviso e fare clic su **Operazione completata**.
1. Fare clic su **Aggiungi condizione** in **Condizione**. Selezionare il segnale che si vuole usare, ad esempio **Percentuale CPU**, quindi fare clic su **Operazione completata**.
1. Nella pagina **Configura logica dei segnali** immettere il **valore soglia** in **Logica avvisi**, quindi fare clic su **Operazione completata**.
1. Da **Gruppi di azioni**, selezionare **Crea nuovo**.
1. Nella pagina **Aggiungi gruppo di azioni** assegnare un nome e un nome sintetico al gruppo di azioni.
1. Assegnare un nome all'azione. Selezionare **Runbook di Automazione** come tipo di azione.
1. Selezionare **Modifica dettagli**. Alla pagina **Configura runbook** selezionare **Utente** in **Origine runbook**.  
1. Selezionare la **sottoscrizione** e l'**account di Automazione**, poi il runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Selezionare **Sì** per **Abilita lo schema di avviso comune.** .
1. Per creare il gruppo di azione, selezionare **OK**.

    ![Pagina Aggiungi gruppo di azioni](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    È possibile usare questo gruppo di azione negli [avvisi dei log attività](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e negli [avvisi quasi in tempo reale](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) che vengono creati.

1. In **Dettagli avviso**aggiungere un nome e una descrizione della regola di avviso e fare clic su **Crea regola di avviso**.

## <a name="next-steps"></a>Passaggi successivi

* Per avviare un runbook usando un webhook, vedere [Avviare un runbook da un webhook](automation-webhooks.md).
* Per le diverse modalità di avvio di un runbook, vedere [Avviare un runbook](./start-runbooks.md).
* Per creare un avviso del log attività, vedere [Creare avvisi del log attività](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Per informazioni su come creare un avviso quasi in tempo reale, vedere [Creare una regola di avviso con il portale di Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
