---
title: Usare gli strumenti della riga di comando per avviare e arrestare le macchine virtuali Azure DevTest LabsUse command-line tools to start and stop VMs Azure DevTest Labs
description: Informazioni su come usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali in Azure DevTest Labs.Learn how to use command-line tools to start and stop virtual machines in Azure DevTest Labs.
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
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169245"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali di Azure DevTest LabsUse command-line tools to start and stop Azure DevTest Labs virtual machines
Questo articolo illustra come usare Azure PowerShell o l'interfaccia della riga di comando di Azure per avviare o arrestare le macchine virtuali in un lab in Azure DevTest Labs.This article shows you how to use Azure PowerShell or Azure CLI to start or stop virtual machines in a lab in Azure DevTest Labs. È possibile creare script PowerShell/CLI per automatizzare queste operazioni. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica
Azure DevTest Labs è un modo per creare ambienti di sviluppo/test veloci, semplici e snelli. Consente di gestire i costi, eseguire rapidamente il provisioning delle macchine virtuali e ridurre al minimo gli sprechi.  Nel portale di Azure sono disponibili funzionalità predefinite che consentono di configurare le macchine virtuali in un lab per l'avvio e l'arresto automatico in orari specifici. 

Tuttavia, in alcuni scenari, è possibile automatizzare l'avvio e l'arresto delle macchine virtuali dagli script PowerShell/CLI. Ti dà una certa flessibilità con l'avvio e l'arresto di singole macchine in qualsiasi momento, invece che in momenti specifici. Ecco alcune delle situazioni in cui l'esecuzione di queste attività tramite script potrebbe essere utile.

- Quando si usa un'applicazione a 3 livelli come parte di un ambiente di test, i livelli devono essere avviati in sequenza. 
- Disattivare una macchina virtuale quando viene soddisfatto un criterio personalizzato per risparmiare denaro. 
- Utilizzarlo come attività all'interno di un flusso di lavoro CI/CD per iniziare all'inizio del flusso, usare le macchine virtuali come macchine di compilazione, computer di test o infrastruttura, quindi arrestare le macchine virtuali al termine del processo. Un esempio potrebbe essere la factory di immagini personalizzata con Azure DevTest Labs.An example of this would be the custom image factory with Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Lo script seguente usa il modulo Azure PowerShell Az.The following script uses the Azure PowerShell Az module. 

Lo script di PowerShell seguente avvia una macchina virtuale in un lab. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) è lo stato attivo principale per questo script. Il parametro **ResourceId** è l'ID risorsa completo per la macchina virtuale nel lab. Il parametro **Action** consente di impostare le opzioni **Avvia** o **Arresta** in base a ciò che è necessario.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
[L'interfaccia della riga](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) di comando di Azure è un altro modo per automatizzare l'avvio e l'arresto delle macchine virtuali DevTest Labs.The Azure CLI is another way to automate the starting and stopping of DevTest Labs VMs. L'interfaccia della riga di comando di Azure può essere [installata](/cli/azure/install-azure-cli?view=azure-cli-latest) in sistemi operativi diversi. Lo script seguente fornisce comandi per l'avvio e l'arresto di una macchina virtuale in un lab. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per l'uso del portale di Azure per eseguire queste operazioni: [Riavviare una macchina virtuale.](devtest-lab-restart-vm.md)
