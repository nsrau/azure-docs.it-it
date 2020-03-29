---
title: Avviare computer usando runbook di automazione in Azure DevTest LabsStart machines using Automation runbooks in Azure DevTest Labs
description: Informazioni su come avviare macchine virtuali in un lab in Lab di Azure DevTest usando i runbook di Automazione di Azure.Learn how to start virtual machines in a lab in Azure DevTest Labs by using Azure Automation runbooks.
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
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166318"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Avviare le macchine virtuali in un lab nell'ordine usando i runbook di Automazione di AzureStart virtual machines in a lab in order by using Azure Automation runbooks
La funzionalità di [avvio automatico](devtest-lab-set-lab-policy.md#set-autostart) di DevTest Labs consente di configurare le macchine virtuali per l'avvio automatico a un'ora specificata. Tuttavia, questa funzionalità non supporta l'avvio dei computer in un ordine specifico. Esistono diversi scenari in cui questo tipo di automazione sarebbe utile.  Uno scenario è quello in cui una macchina virtuale Jumpbox all'interno di un lab deve essere avviata prima, prima delle altre macchine virtuali, poiché Jumpbox viene usato come punto di accesso alle altre macchine virtuali.  Questo articolo illustra come configurare un account di Automazione di Azure con un runbook di PowerShell che esegue uno script. Lo script usa i tag nelle macchine virtuali nel lab per consentire di controllare l'ordine di avvio senza dover modificare lo script.

## <a name="setup"></a>Configurazione
In questo esempio, le macchine virtuali nel lab devono avere il tag **StartupOrder** aggiunto con il valore appropriato (0,1,2 e così via). Designare qualsiasi macchina che non deve essere avviata come -1.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure
Creare un account di Automazione di Azure seguendo le istruzioni riportate in [questo articolo.](../automation/automation-create-standalone-account.md) Scegliere l'opzione **Account RunAs** durante la creazione dell'account. Dopo aver creato l'account di automazione, aprire la pagina **Moduli** e selezionare **Aggiorna moduli** di Azure nella barra dei menu. I moduli predefiniti sono versioni precedenti e senza l'aggiornamento lo script potrebbe non funzionare.

## <a name="add-a-runbook"></a>Aggiungere un runbook
A questo punto, per aggiungere un runbook all'account di automazione, selezionare **Runbooks** nel menu a sinistra. Selezionare **Aggiungi un runbook** nel menu e seguire le istruzioni per [creare un runbook di PowerShell.](../automation/automation-first-runbook-textual-powershell.md)

## <a name="powershell-script"></a>Script di PowerShell
Lo script seguente accetta il nome della sottoscrizione, il nome del lab come parametri. Il flusso dello script consiste nell'ottenere tutte le macchine virtuali nel lab e quindi analizzare le informazioni sui tag per creare un elenco dei nomi delle macchine virtuali e il relativo ordine di avvio. Lo script esamina le macchine virtuali nell'ordine e avvia le macchine virtuali. Se sono presenti più macchine virtuali in un numero di ordine specifico, vengono avviate in modo asincrono tramite processi di PowerShell.If there are multiple VMs in a specific order number, they are started asynchronously using PowerShell jobs. Per le macchine virtuali che non dispongono di un tag, impostare il valore di avvio come l'ultimo (10), verranno avviate per ultime, per impostazione predefinita.  Se il lab non vuole che la macchina virtuale venga avviata automaticamente, impostare il valore del tag su 11 e verrà ignorato.

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
Per fare in modo che questo script venga eseguito ogni giorno, [creare una pianificazione](../automation/shared-resources/schedules.md#creating-a-schedule) nell'account di automazione. Una volta creata la pianificazione, [collegarla al runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

In una situazione su larga scala in cui sono presenti più sottoscrizioni con più lab, archiviare le informazioni sui parametri in un file per lab diversi e passare il file allo script anziché ai singoli parametri. Lo script deve essere modificato, ma l'esecuzione principale sarebbe la stessa. Mentre in questo esempio viene usata l'automazione di Azure per eseguire lo script di PowerShell, esistono altre opzioni, ad esempio l'uso di un'attività in una pipeline di compilazione/rilascio.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per altre informazioni su Automazione di Azure: [introduzione ad Automazione di Azure.See](../automation/automation-intro.md)the following article to learn more about Azure Automation: An introduction to Azure Automation .
