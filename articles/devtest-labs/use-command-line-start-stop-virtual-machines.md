---
title: Usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali Azure DevTest Labs
description: Informazioni su come usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5a42658f2b83f101271f158c9af70833601b56d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476417"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Usare gli strumenti da riga di comando per avviare e arrestare Azure DevTest Labs macchine virtuali
Questo articolo illustra come usare Azure PowerShell o l'interfaccia della riga di comando di Azure per avviare o arrestare le macchine virtuali in un Lab in Azure DevTest Labs. È possibile creare script PowerShell/CLI per automatizzare queste operazioni. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica
Azure DevTest Labs è un modo per creare ambienti di sviluppo/test veloci, semplici e snelli. Consente di gestire i costi, effettuare rapidamente il provisioning di macchine virtuali e ridurre al minimo gli sprechi.  Nel portale di Azure sono disponibili funzionalità predefinite che consentono di configurare le macchine virtuali in un Lab in modo che vengano avviate e arrestate automaticamente in momenti specifici. 

Tuttavia, in alcuni scenari potrebbe essere necessario automatizzare l'avvio e l'arresto di macchine virtuali da script PowerShell/CLI. Offre una certa flessibilità con l'avvio e l'arresto di singoli computer in qualsiasi momento anziché in momenti specifici. Di seguito sono riportate alcune situazioni in cui l'esecuzione di queste attività tramite script può risultare utile.

- Quando si usa un'applicazione a 3 livelli come parte di un ambiente di test, è necessario avviare i livelli in una sequenza. 
- Spegnere una macchina virtuale quando viene raggiunto un criterio personalizzato per risparmiare denaro. 
- Usarlo come attività all'interno di un flusso di lavoro di integrazione continua/recapito continuo per iniziare dall'inizio del flusso, usare le VM come macchine di compilazione, computer di test o infrastruttura, quindi arrestare le VM al termine del processo. Un esempio è costituito dalla factory di immagini personalizzate con Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Lo script seguente usa il Azure PowerShell AZ Module. 

Lo script di PowerShell seguente avvia una macchina virtuale in un Lab. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) è lo stato attivo principale per questo script. Il parametro **resourceId** è l'ID di risorsa completo per la macchina virtuale nel Lab. Il parametro **Action** è il punto in cui vengono impostate le opzioni di **avvio** o di **arresto** a seconda di ciò che è necessario.

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
L' [interfaccia](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) della riga di comando di Azure è un altro modo per automatizzare l'avvio e l'arresto delle macchine virtuali di DevTest Labs. L'interfaccia della riga di comando di Azure può essere [installata](/cli/azure/install-azure-cli?view=azure-cli-latest) in sistemi operativi diversi. Lo script seguente fornisce i comandi per l'avvio e l'arresto di una macchina virtuale in un Lab. 

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
Vedere l'articolo seguente per l'uso del portale di Azure per eseguire queste operazioni: [riavviare una macchina virtuale](devtest-lab-restart-vm.md).
