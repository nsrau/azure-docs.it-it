---
title: Convalidare la configurazione dell'account di Automazione di Azure
description: Questo articolo descrive come verificare che la configurazione dell'account di Automazione sia impostata correttamente.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 5359a12d5b241eff80203c9e9bf04107ce4d3159
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Testare l'autenticazione con un account RunAs di Automazione di Azure
Al termine della creazione di un account di Automazione, è possibile eseguire un semplice test per verificare di poter completare l'autenticazione in Azure Resource Manager o nella distribuzione classica di Azure con l'account RunAs di Automazione appena creato o aggiornato.    

## <a name="automation-run-as-authentication"></a>Autenticazione con l'account RunAs di Automazione
Usare il codice di esempio seguente per [creare un runbook di PowerShell](automation-creating-importing-runbook.md) per verificare l'autenticazione usando l'account RunAs e anche nei runbook personalizzati per autenticare e gestire le risorse di Resource Manager con l'account di Automazione.   

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

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Si noti che il cmdlet usato per l'autenticazione nel runbook (**Add-AzureRmAccount**) usa il set di parametri *ServicePrincipalCertificate*  ed esegue l'autenticazione usando il certificato dell'entità servizio, non le credenziali.  

Quando si [esegue il runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) per convalidare l'account RunAs, viene creato un [processo del runbook](automation-runbook-execution.md), viene visualizzata la pagina del processo e lo stato del processo è riportato nel riquadro **Riepilogo processi**. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.

Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .  Nella pagina **Output** dovrebbe risultare che l'autenticazione è stata completata e viene restituito un elenco di tutte le risorse in tutti i gruppi di risorse della sottoscrizione.  

Ricordare di rimuovere il blocco di codice che inizia con il commento `#Get all ARM resources from all resource groups` quando si usa di nuovo il codice per i runbook.

## <a name="classic-run-as-authentication"></a>Autenticazione con l'account RunAs classico
Usare il codice di esempio seguente per [creare un runbook di PowerShell](automation-creating-importing-runbook.md) per verificare l'autenticazione usando l'account RunAs classico e anche nei runbook personalizzati per autenticare e gestire le risorse nel modello di distribuzione classica.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Quando si [esegue il runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) per convalidare l'account RunAs, viene creato un [processo del runbook](automation-runbook-execution.md), viene visualizzata la pagina del processo e lo stato del processo è riportato nel riquadro **Riepilogo processi**. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.

Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .  Nella pagina **Output** dovrebbe risultare che l'autenticazione è stata completata e viene restituito un elenco di tutte le VM di Azure distribuite nella sottoscrizione, per nome di VM.  

Ricordare di rimuovere il cmdlet **Get-AzureVM** quando si usa di nuovo il codice per i runbook.

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
