---
title: Avviare le macchine con i runbook di automazione in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come avviare le macchine virtuali in un lab in Azure DevTest Labs usando i runbook di automazione di Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: d80328943ae818b3bad9c0a275b74968ee33d4b7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789058"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Avviare le macchine virtuali in un lab in ordine usando i runbook di automazione di Azure
Il [autostart](devtest-lab-set-lab-policy.md#set-autostart) funzionalità di DevTest Labs consente di configurare le macchine virtuali per l'avvio automatico in un momento specificato. Tuttavia, questa funzionalità non supporta computer per iniziare in un ordine specifico. Esistono diversi scenari in cui sarebbe utile questo tipo di automazione.  Uno scenario è quello in cui una VM Jumpbox all'interno di un lab deve essere avviata per prima, prima di altre macchine virtuali, come il Jumpbox viene usato come punto di accesso con altre macchine virtuali.  Questo articolo illustra come configurare un account di automazione di Azure con un runbook di PowerShell che esegue uno script. Lo script Usa i tag nelle macchine virtuali nel lab consente di controllare l'ordine di avvio senza dover modificare lo script.

## <a name="setup"></a>Configurazione
In questo esempio, è necessario disporre di tag di macchine virtuali nel lab **StartupOrder** aggiunto con il valore appropriato (0,1,2, ecc.). Specificare tutti i computer che non devono essere avviato come -1.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure
Creare un account di automazione di Azure seguendo le istruzioni [questo articolo](../automation/automation-create-standalone-account.md). Scegliere il **account RunAs** durante la creazione dell'account. Dopo aver creato l'account di automazione, aprire il **moduli** pagina, quindi selezionare **Aggiorna moduli di Azure** nella barra dei menu. I moduli predefiniti sono che diverse versioni precedenti e senza l'aggiornamento dello script potrebbero non funzionare.

## <a name="add-a-runbook"></a>Aggiungere un runbook
A questo punto, per aggiungere un runbook nell'account di automazione, selezionare **runbook** nel menu a sinistra. Selezionare **Aggiungi runbook** nel menu e seguire le istruzioni per [creare un runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script di PowerShell
Lo script seguente accetta il nome della sottoscrizione, il nome del lab come parametri. Il flusso dello script è per ottenere tutte le VM nel lab e quindi analizzare le informazioni di tag per creare un elenco di nomi di macchina virtuale e l'ordine di avvio. Lo script illustra in modo dettagliato le macchine virtuali in ordine e avvia le macchine virtuali. Se sono presenti più macchine virtuali in un dato numero d'ordine, essi vengono avviati in modo asincrono utilizzando i processi di PowerShell. Per tali macchine virtuali che non hanno un tag, il valore di avvio impostato per essere l'ultimo (10), essi verrà avviati ultime, per impostazione predefinita.  Se il lab non desidera che la macchina virtuale per essere avviato automaticamente, impostare il valore di tag a 11 e verrà ignorato.

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
$dtLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzureRmResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

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
Save-AzureRmContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzureRmContext -Path ($profilePath)
    Invoke-AzureRmResourceAction `
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
        $returnStatus = Invoke-AzureRmResourceAction `
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
Affinché questo script ogni giorno, eseguire [creare una pianificazione](../automation/shared-resources/schedules.md#creating-a-schedule) nell'account di automazione. Dopo aver creata la pianificazione, [crea un collegamento al runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

In una situazione su larga scala in cui sono presenti più sottoscrizioni con più Lab, archiviare le informazioni sui parametri in un file per i laboratori diversi e passare il file dello script anziché i singoli parametri. Lo script dovrà essere modificato, ma l'esecuzione di core è la stessa. Mentre in questo esempio Usa l'automazione di Azure per eseguire lo script di PowerShell, esistono altre opzioni, come l'uso di un'attività in una pipeline di compilazione/versione.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per altre informazioni su automazione di Azure: [Introduzione ad automazione di Azure](../automation/automation-intro.md).
