---
title: "Script di PowerShell: trovare l'insieme di credenziali per l'account di archiviazione"
description: Informazioni su come usare uno script di Azure PowerShell per trovare l'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075699"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Script di PowerShell per trovare l'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione

Questo script consente di trovare l'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione.

## <a name="sample-script"></a>Script di esempio

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Come eseguire lo script

1. Salvare lo script riportato sopra nel computer con un nome a scelta. In questo esempio è stato salvato con il nome *FindRegisteredStorageAccount.ps1*.
2. Eseguire lo script, specificando i parametri seguenti:

    * **-ResourceGroupName**: il gruppo di risorse dell'account di archiviazione
    * **-StorageAccountName**: il nome dell'account di archiviazione
    * **-SubscriptionID**: l'ID della sottoscrizione in cui è presente l'account di archiviazione.

L'esempio seguente prova a trovare l'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione *afsaccount*:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Output

L'output visualizza il percorso completo dell'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione. Di seguito è riportato un output di esempio:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di condivisioni file di Azure dal portale di Azure](../backup-afs.md)
