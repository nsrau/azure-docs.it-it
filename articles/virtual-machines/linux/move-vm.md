---
title: Spostare una macchina virtuale usando l'interfaccia della riga di comando di AzureMove a VM in using the Azure CLI
description: Spostare una macchina virtuale in un'altra sottoscrizione o gruppo di risorse di Azure usando l'interfaccia della riga di comando di Azure.Move a VM to another Azure subscription or resource group using the Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944594"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Spostare una macchina virtuale in un'altra sottoscrizione o gruppo di risorseMove a VM to another subscription or resource group
Questo articolo illustra come spostare una macchina virtuale (VM) tra gruppi di risorse o sottoscrizioni. Lo spostamento di una VM tra sottoscrizioni può essere comodo se è stata creata una VM in una sottoscrizione personale e ora si desidera spostarla alla sottoscrizione dell'azienda.

> [!IMPORTANT]
>Nell'ambito dello spostamento vengono creati nuovi ID risorsa. Dopo aver spostato la macchina virtuale, sarà necessario aggiornare strumenti e script in modo che usino i nuovi ID risorsa.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Usare l'interfaccia della riga di comando di Azure per spostare una VM


Prima di spostare la macchina virtuale usando l'interfaccia della riga di comando di Azure, verificare che le sottoscrizioni di origine e di destinazione si trovino all'interno dello stesso tenant. Per verificare che entrambe le sottoscrizioni contengano lo stesso ID tenant, usare [az account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se gli ID tenant per le sottoscrizioni di origine e di destinazione non sono uguali, per spostare le risorse in un nuovo tenant è necessario contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Per spostare correttamente una VM, è necessario spostare la VM e tutte le relative risorse di supporto. Usare il comando [az resource list](/cli/azure/resource) per elencare tutte le risorse in un gruppo di risorse e i relativi ID. Può essere utile inviare tramite pipe l'output di questo comando in un file, in modo da poter copiare e incollare gli ID in comandi successivi.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Per spostare una macchina virtuale e le relative risorse in un altro gruppo di risorse, usare [az resource move](/cli/azure/resource). L'esempio seguente mostra come spostare una VM e la maggior parte delle risorse comuni richieste. Usare il parametro **-ids** e poi passare in un elenco delimitato da virgole, senza spazi, degli ID per le risorse da spostare.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Se si desidera spostare la macchina virtuale e le relative risorse in una sottoscrizione diversa, aggiungere il parametro **--destination-subscriptionId** per specificare la sottoscrizione di destinazione.

Quando viene richiesto di confermare che si vogliono spostare le risorse specificate, immettere **Y** per confermare.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passaggi successivi
È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
