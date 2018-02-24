---
title: Spostare una risorsa di macchina virtuale Windows in Azure | Microsoft Docs
description: Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure nel modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: f4b739fd34cc0c85d47b97b7b42a70eb7f5f5ac7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure
Questo articolo illustra come spostare una VM di Windows tra gruppi di risorse o sottoscrizioni. Lo spostamento tra sottoscrizioni può essere comodo se in origine è stata creata una VM in una sottoscrizione personale e ora si vuole spostarla alla sottoscrizione dell'azienda per continuare il lavoro.

> [!IMPORTANT]
>Non è possibile spostare Managed Disks in questa fase. 
>
>Nell'ambito dello spostamento vengono creati nuovi ID risorsa. Una volta spostata la VM, è necessario aggiornare strumenti e script in modo che usino i nuovi ID risorsa. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Usare PowerShell per spostare una VM

Per spostare una macchina virtuale in un altro gruppo di risorse, è necessario assicurarsi di spostare anche tutte le risorse dipendenti. Per usare il cmdlet Move-AzureRMResource, è necessario conoscere il ResourceID per ciascuna risorsa. È possibile ottenere un elenco di ResourceID tramite il cmdlet [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Per spostare una VM, occorre spostare più risorse. È possibile usare l'output di Find-AzureRMResource per creare un elenco separato da virgole di ResourceID e passarlo a [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) per spostare i ResourceID nella destinazione. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Per spostare le risorse in una diversa sottoscrizione, includere il parametro **DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Verrà richiesto di confermare che si vuole spostare le risorse specificate. 

## <a name="next-steps"></a>Passaggi successivi
È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../resource-group-move-resources.md).    

