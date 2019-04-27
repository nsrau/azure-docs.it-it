---
title: Spostare una macchina virtuale di Linux in Azure | Microsoft Docs
description: Spostare una VM di Linux in un'altra sottoscrizione o in un altro gruppo di risorse di Azure nel modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: d2d3f36c9b4ee0557f9e060bec762877a94ea637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473936"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Spostare una VM di Linux in un'altra sottoscrizione o in un altro gruppo di risorse
Questo articolo illustra come spostare una macchina virtuale di Linux tra gruppi di risorse o sottoscrizioni. Lo spostamento di una VM tra sottoscrizioni può essere comodo se è stata creata una VM in una sottoscrizione personale e ora si desidera spostarla alla sottoscrizione dell'azienda.

> [!IMPORTANT]
>Non è possibile spostare Azure Managed Disks in questa fase. 
>
>Nell'ambito dello spostamento vengono creati nuovi ID risorsa. Dopo aver spostato la macchina virtuale, sarà necessario aggiornare strumenti e script in modo che usino i nuovi ID risorsa. 
> 
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
È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../resource-group-move-resources.md).    

