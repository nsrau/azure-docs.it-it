---
title: Spostare una macchina virtuale di Linux in Azure | Microsoft Docs
description: Spostare una VM di Linux in un'altra sottoscrizione o in un altro gruppo di risorse di Azure nel modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Spostare una VM di Linux in un'altra sottoscrizione o in un altro gruppo di risorse
Questo articolo illustra come spostare una VM di Linux tra gruppi di risorse o sottoscrizioni. Lo spostamento di una VM tra sottoscrizioni può essere comodo se è stata creata una VM in una sottoscrizione personale e ora si desidera spostarla alla sottoscrizione dell'azienda.

> [!IMPORTANT]
>Non è possibile spostare Managed Disks in questa fase. 
>
>Nell'ambito dello spostamento vengono creati nuovi ID risorsa. Una volta spostata la VM, è necessario aggiornare strumenti e script in modo che usino i nuovi ID risorsa. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Usare l'interfaccia della riga di comando di Azure per spostare una VM


Prima di poter spostare la macchina virtuale usando l'interfaccia CLI, è necessario assicurarsi che le sottoscrizioni di origine e di destinazione esistono all'interno dello stesso tenant. Per verificare che entrambe le sottoscrizioni contengono lo stesso ID tenant, utilizzare [mostra account az](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se gli ID tenant per le sottoscrizioni di origine e di destinazione non sono uguali, per spostare le risorse in un nuovo tenant è necessario contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Per spostare correttamente una VM, è necessario spostare la VM e tutte le relative risorse di supporto. Utilizzare il [elenco risorse az](/cli/azure/resource#az_resource_list) comando per elencare tutte le risorse in un gruppo di risorse e i relativi ID. Può essere utile inviare tramite pipe l'output di questo comando in un file, in modo da poter copiare e incollare gli ID in comandi successivi.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Per spostare una macchina virtuale e le relative risorse in un altro gruppo di risorse, usare [dello spostamento delle risorse az](/cli/azure/resource#az_resource_move). L'esempio seguente mostra come spostare una VM e la maggior parte delle risorse comuni richieste. Utilizzare il **-ID** parametro e passare un elenco delimitato da virgole (senza spazi) di ID per le risorse da spostare.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Se si desidera spostare la macchina virtuale e le relative risorse per una sottoscrizione diversa, aggiungere il **-destinazione subscriptionId** parametro per specificare la sottoscrizione di destinazione.

Se viene chiesto di confermare che si desidera spostare la risorsa specificata. Digitare **Y** per confermare lo spostamento delle risorse.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passaggi successivi
È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../resource-group-move-resources.md).    

