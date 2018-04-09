---
title: Esempi di Azure PowerShell - Collegare e usare dischi dati | Microsoft Docs
description: Esempi di Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ef6fedc7f701bedcb4409da5da21725a78acf22f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>Collegare e usare dischi dati con un set di scalabilità di macchine virtuali tramite PowerShell
Questo script crea un set di scalabilità di macchine virtuali e collega e prepara dischi dati.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Crea il set di scalabilità di macchine virtuali e tutte le risorse di supporto, tra cui la rete virtuale, il servizio di bilanciamento del carico e le regole NAT. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Ottiene informazioni su un set di scalabilità di macchine virtuali. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Aggiunge un'estensione per VM per Script personalizzato per l'installazione di un'applicazione Web di base. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aggiorna il modello del set di scalabilità di macchine virtuali per l'applicazione dell'estensione di VM. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../powershell-samples.md).