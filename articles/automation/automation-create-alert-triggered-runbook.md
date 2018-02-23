---
title: Rispondere agli avvisi di Azure con un runbook di automazione | Microsoft Docs
description: Informazioni su come attivare un runbook da eseguire quando vengono generati avvisi di Azure.
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Rispondere agli avvisi di Azure con un runbook di automazione

Attualmente [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) offre log e metriche di livello base per la maggior parte dei servizi in Microsoft Azure. I runbook di automazione di Azure possono essere chiamati tramite [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) o dagli avvisi classici per automatizzare le attività in base agli avvisi. Questo articolo illustra come configurare ed eseguire un runbook in base a questi avvisi.

## <a name="alert-types"></a>Tipi di avviso

I runbook sono azioni supportate per tutti i tre tipi di avvisi. Quando un avviso chiama il runbook, la chiamata effettiva è una richiesta HTTP POST al webhook. Il corpo della richiesta POST contiene un oggetto in formato JSON con proprietà utili relative all'avviso. La tabella seguente contiene collegamenti allo schema del payload per ogni tipo di avviso:

|Avviso  |Descrizione|Schema del payload  |
|---------|---------|---------|
|[Avviso di metrica classico](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Ricevere una notifica quando una metrica a livello di piattaforma soddisfa una condizione specifica (ad esempio, la % della CPU in una macchina virtuale è superiore a 90 negli ultimi 5 minuti).| [Schema del payload](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Avviso del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Ricevere una notifica quando un nuovo evento nel Log attività di Azure corrisponde a condizioni specifiche (ad esempio, quando un'operazione di eliminazione di macchina virtuale si verifica in myProductionResourceGroup o quando viene generato un nuovo evento di integrità del servizio con stato "Attivo").| [Schema del payload](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Avvisi delle metriche quasi in tempo reale](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Ricevere una notifica più velocemente rispetto agli avvisi metrica quando una o più metriche a livello di piattaforma soddisfano le condizioni specificate, ad esempio se la percentuale di CPU su una macchina virtuale è maggiore del 90% e la rete in ingresso è maggiore di 500 MB per gli ultimi 5 minuti.| [Schema del payload](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Poiché i dati forniti da ogni avviso sono diversi, ogni avviso deve essere gestito in modo diverso. Nella sezione successiva viene illustrato come creare un runbook per gestire questi diversi tipi di avvisi.

## <a name="create-a-runbook-to-handle-alerts"></a>Creare un runbook per gestire gli avvisi

Per usare l'automazione con gli avvisi, è necessario un runbook con la logica per gestire il payload JSON dell'avviso passato al runbook. Il seguente runbook di esempio deve essere chiamato da un avviso di Azure. Come descritto nella sezione precedente, ogni tipo di avviso ha uno schema diverso. Lo script accetta i dati del webhook nel parametro di input del runbook `WebhookData` da un avviso e valuta il payload JSON per determinare il tipo di avviso usato. Nell'esempio seguente verrebbe usato un avviso generato da una macchina virtuale. Recupera i dati della macchina virtuale dal payload e usa tali informazioni per arrestare la macchina virtuale. La connessione deve essere configurata nell'account di Automazione usato per l'esecuzione del runbook.

Il runbook usa l'[account RunAs](automation-create-runas-account.md) **AzureRunAsConnection** per l'autenticazione in Azure per eseguire l'azione di gestione sulla macchina virtuale.

È possibile modificare lo script di PowerShell seguente per l'uso con molte risorse diverse.

Usare l'esempio seguente e creare un runbook denominato **Stop-AzureVmInResponsetoVMAlert** con lo script di PowerShell di esempio.

1. Aprire l'account di Automazione.
1. Fare clic su **Runbook** in **AUTOMAZIONE PROCESSI**. Viene visualizzato l'elenco di runbook.
1. Fare clic sul pulsante **Aggiungi runbook** all'inizio dell'elenco. Nella pagina **Aggiungi runbook** selezionare **Creazione rapida**.
1. Immettere "Stop-AzureVmInResponsetoVMAlert" per il **Nome** del runbook e selezionare **PowerShell** per **Tipo di runbook**. Fare clic su **Crea**.
1. Copiare l'esempio di PowerShell seguente nel riquadro di modifica. Fare clic su **Pubblica** per salvare e pubblicare il runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

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
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Creare un gruppo di azioni

Un gruppo di azioni è una raccolta di azioni che vengono eseguite in base a un avviso. I runbook sono solo una delle molte azioni disponibili con i gruppi di azioni.

1. Nel portale selezionare **Monitoraggio**.

1. Selezionare **Gruppi di azioni** in **Impostazioni**.

1. Selezionare **Aggiungi gruppo di azione** e compilare i campi.

1. Immettere un nome nella casella Nome gruppo di azione e un nome nella casella Nome breve gruppo di azione. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

1. Nella casella Sottoscrizione viene inserita automaticamente la sottoscrizione corrente. Il gruppo di azione verrà salvato in questa sottoscrizione.
Selezionare il gruppo di risorse in cui verrà salvato il gruppo di azioni.

Per questo esempio vengono create due azioni, un'azione del runbook e un'azione di notifica.

### <a name="runbook-action"></a>Azione del runbook

La procedura seguente crea un'azione del runbook all'interno del gruppo di azioni.

1. In **Azioni** assegnare un nome all'azione.

1. Selezionare **Runbook di Automazione** in **Tipo di azione**.

1. In **Dettagli** selezionare **Modifica dettagli**.

1. Nella pagina **Configura runbook** selezionare **Utente** in **Origine runbook**.

1. Scegliere **Sottoscrizione** e **Account di Automazione**, infine selezionare il runbook creato nel passaggio precedente denominato "Stop-AzureVmInResponsetoVMAlert".

1. Al termine, fare clic su **OK**.

### <a name="notification-action"></a>Azione di notifica

La procedura seguente crea un'azione di notifica all'interno del gruppo di azioni.

1. In **Azioni** assegnare un nome all'azione.

1. Selezionare **Posta elettronica** in **Tipo di azione**.

1. In **Dettagli** selezionare **Modifica dettagli**.

1. Nella pagina **Posta elettronica** immettere l'indirizzo di posta elettronica a cui inviare le notifiche e fare clic su **OK**. L'aggiunta di un indirizzo di posta elettronica e del runbook come azione sono utili perché si riceve notifica dell'avvio del runbook. Il gruppo di azioni dovrebbe essere simile all'immagine seguente:

   ![Pagina Aggiungi gruppo di azioni](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Fare clic su **OK** per creare il gruppo di azioni.

Con il gruppo di azioni creato, è possibile creare [avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) o [avvisi quasi in tempo reale](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) e usare il gruppo di azioni creato.

## <a name="classic-alert"></a>Avviso classico

Gli avvisi classici sono basati sulle metriche e non usano gruppi di azioni, ma su di essi vengono basate le azioni del runbook. Per creare un avviso classico, usare questa procedura:

1. Selezionare **+ Aggiungi avviso per la metrica**.

1. Denominare l'avviso per la metrica "myVMCPUAlert" e immettere una breve descrizione dell'avviso.

1. Impostare Condizione per l'avviso della metrica su "Maggiore di", impostare Soglia su "10" e Periodo su "Negli ultimi 5 minuti".

1. In **Intervieni** selezionare **Esegui un runbook da questo avviso**.

1. Nella pagina **Configura runbook** selezionare **Utente** in **Origine runbook**. Scegliere l'account di Automazione e selezionare il runbook **Stop-AzureVmInResponsetoVMAlert**. Fare clic su **OK** e quindi fare di nuovo clic su **OK** per salvare la regola di avviso.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'avvio di runbook di automazione con webhook, vedere [Avviare un runbook da un webhook](automation-webhooks.md)
* Per informazioni dettagliate sulle diverse modalità disponibili per l'avvio dei runbook, vedere [Avvio di un runbook](automation-starting-a-runbook.md).
* Per informazioni su come creare un avviso del log attività, vedere [Creare avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* Per informazioni su come creare un avviso quasi in tempo reale, vedere [Creare una regola di avviso con il portale di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).