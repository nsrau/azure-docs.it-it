---
title: Spostare una risorsa di una macchina virtuale Windows in AzureMove a Windows VM resource in Azure
description: Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure nel modello di distribuzione di Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 0c3f5541405d1fd983bbf988b99d2b4e10d8908c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865652"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure
Questo articolo illustra come spostare una macchina virtuale di Windows tra gruppi di risorse o sottoscrizioni. Lo spostamento tra sottoscrizioni può essere comodo se in origine è stata creata una VM in una sottoscrizione personale e ora si vuole spostarla alla sottoscrizione dell'azienda per continuare il lavoro. Non è necessario avviare la macchina virtuale per spostarla e deve continuare a essere eseguita durante lo spostamento.

> [!IMPORTANT]
>Nell'ambito dello spostamento vengono creati nuovi ID risorsa. Dopo aver spostato la macchina virtuale, sarà necessario aggiornare strumenti e script in modo che usino i nuovi ID risorsa.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Usare PowerShell per spostare una VM

Per spostare una macchina virtuale in un altro gruppo di risorse, è necessario assicurarsi di spostare anche tutte le risorse dipendenti. Per ottenere un elenco con l'ID risorsa di ognuna di queste risorse, usare il cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

È possibile usare l'output del comando precedente per creare un elenco separato da virgole di ID di risorsa per [Spostare-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) per spostare ogni risorsa nella destinazione.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Per spostare le risorse in una diversa sottoscrizione, includere il parametro **DestinationSubscriptionId** .

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Quando viene richiesto di confermare che si vogliono spostare le risorse specificate, immettere **Y** per confermare.

## <a name="next-steps"></a>Passaggi successivi
È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
