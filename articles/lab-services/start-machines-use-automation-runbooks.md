---
title: Avviare i computer con manuali operativi di automazione in Azure DevTest Labs
description: Informazioni su come avviare le macchine virtuali in un Lab in Azure DevTest Labs usando manuali operativi di automazione di Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: df6d7943a5344b4288dfe369dcce9087b894984f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580581"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Avviare le macchine virtuali in un Lab usando manuali operativi di automazione di Azure
La funzionalità di [avvio](devtest-lab-set-lab-policy.md#set-autostart) automatico di DevTest Labs consente di configurare le macchine virtuali per l'avvio automatico all'ora specificata. Questa funzionalità, tuttavia, non supporta l'avvio di computer in un ordine specifico. Esistono diversi scenari in cui questo tipo di automazione risulta utile.  Uno scenario è la posizione in cui è necessario avviare una macchina virtuale JumpBox all'interno di un Lab prima delle altre VM, perché il JumpBox viene usato come punto di accesso per le altre macchine virtuali.  Questo articolo illustra come configurare un account di automazione di Azure con un Runbook di PowerShell per l'esecuzione di uno script. Lo script usa i tag nelle macchine virtuali nel Lab per consentire di controllare l'ordine di avvio senza dover modificare lo script.

## <a name="setup"></a>Installazione
In questo esempio, le macchine virtuali nel lab devono avere il tag **StartupOrder** aggiunto con il valore appropriato (0, 1, 2 e così via). Designare tutti i computer che non devono essere avviati come-1.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure
Creare un account di automazione di Azure seguendo le istruzioni riportate in [questo articolo](../automation/automation-create-standalone-account.md). Scegliere l'opzione **account RunAs** durante la creazione dell'account. Una volta creato l'account di automazione, aprire la pagina **moduli** e selezionare **Aggiorna moduli di Azure** sulla barra dei menu. I moduli predefiniti sono diverse versioni precedenti e senza l'aggiornamento lo script potrebbe non funzionare.

## <a name="add-a-runbook"></a>Aggiungere un Runbook
A questo punto, per aggiungere un runbook all'account di automazione, selezionare **manuali operativi** nel menu a sinistra. Selezionare **Aggiungi Runbook** nel menu e seguire le istruzioni per [creare un Runbook di PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script PowerShell
Lo script seguente accetta il nome della sottoscrizione, il nome del Lab come parametri. Il flusso dello script consiste nell'ottenere tutte le macchine virtuali nel Lab, quindi analizzare le informazioni sui tag per creare un elenco dei nomi delle macchine virtuali e il relativo ordine di avvio. Lo script esamina le macchine virtuali in ordine e avvia le VM. Se sono presenti più macchine virtuali in un numero di ordine specifico, vengono avviate in modo asincrono usando i processi di PowerShell. Per le macchine virtuali che non dispongono di un tag, impostare valore di avvio su ultimo (10), per impostazione predefinita verranno avviate per ultime.  Se il Lab non desidera che la macchina virtuale venga avviata in modo automatico, impostare il valore di tag su 11 e verrà ignorato.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Creare una pianificazione
Per eseguire questo script ogni giorno, [creare una pianificazione](../automation/shared-resources/schedules.md#create-a-schedule) nell'account di automazione. Una volta creata la pianificazione, [collegarla a Runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

In una situazione di grandi dimensioni in cui sono presenti più sottoscrizioni con più laboratori, archiviare le informazioni sui parametri in un file per laboratori diversi e passare il file allo script anziché ai singoli parametri. È necessario modificare lo script, ma l'esecuzione principale è la stessa. Mentre questo esempio USA automazione di Azure per eseguire lo script di PowerShell, sono disponibili altre opzioni, ad esempio l'uso di un'attività in una pipeline di compilazione/versione.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per altre informazioni su automazione di Azure: [Introduzione ad automazione di Azure](../automation/automation-intro.md).
