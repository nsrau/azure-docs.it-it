---
title: Eliminare i BLOB di archiviazione per i log di flusso del gruppo di sicurezza di rete in Controllo rete di AzureDelete storage blobs for network network group flow logs in Azure Network Watcher - Documenti Microsoft
description: Questo articolo illustra come eliminare i BLOB di archiviazione del log di flusso del gruppo di sicurezza di rete che non rientrano nel periodo dei criteri di conservazione in Watcher rete di Azure.This article explains how to delete the network security group flow log storage blobs that are outside their retention policy period in Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6d535bcc2e0831baae658796f76c8087d74c6a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587210"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Eliminare i BLOB di archiviazione del log di flusso del gruppo di sicurezza di rete in Network WatcherDelete network security group flow log storage blobs in Network Watcher

Attualmente si verifica un problema per cui [i log dei flussi del gruppo di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md) per Network Watcher non vengono eliminati automaticamente dall'archiviazione BLOB in base alle impostazioni dei criteri di conservazione. È ora necessario eseguire uno script di PowerShell per eliminare manualmente i log di flusso dall'account di archiviazione, come descritto in questo articolo.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>Eseguire lo script di PowerShell per eliminare i log di flusso del gruppo di sicurezza di reteRun PowerShell script to delete NSG flow logs
 
Copiare e salvare lo script seguente in un percorso, ad esempio la directory di lavoro corrente. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. Immettere i seguenti parametri nello script in base alle esigenze:
   - **SubscriptionId** [Obbligatorio]: ID sottoscrizione da cui si desidera eliminare i BLOB del log di flusso del gruppo di sicurezza di rete.
   - **Percorso** [Obbligatorio]: stringa di _posizione_ dell'area dei gruppi di sicurezza di rete per cui si desidera eliminare i BLOB del log di flusso del gruppo di sicurezza di rete. È possibile visualizzare queste informazioni nel portale di Azure o in [GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23).
   - **Conferma** [Facoltativo]: passare il flag di conferma se si vuole confermare manualmente l'eliminazione di ogni BLOB di archiviazione.

1. Eseguire lo script salvato come illustrato nell'esempio seguente, in cui il file di script è stato salvato come **Delete-NsgFlowLogsBlobs.ps1**:
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>Passaggi successivi
- I clienti possono automatizzare l'esecuzione dello script usando le app per la logica di Azure o [l'automazione di AzureCustomers](https://azure.microsoft.com/services/automation/) can automate running the script by using [Azure Logic Apps](../logic-apps/logic-apps-overview.md) or Azure Automation
- Per altre informazioni sulla registrazione dei gruppi di sicurezza di rete, vedere Log di Monitoraggio di Azure per i gruppi di sicurezza di [rete.](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

