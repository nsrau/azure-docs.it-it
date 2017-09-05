---
title: Esempio di script di Azure PowerShell - Eseguire il peering di due reti virtuali | Microsoft Docs
description: Esempio di script di Azure PowerShell - Eseguire il peering di due reti virtuali
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 51c0b98727e148671cfd7ab2b31ffd1c705d8a4e
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---

# <a name="peer-two-virtual-networks"></a>Eseguire il peering di due reti virtuali

Questo script consente di creare e connettere due reti virtuali nella stessa area usando la rete di Azure. Dopo aver eseguito lo script, verrà creato un peering tra due reti virtuali.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Eseguire il peering di due reti")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Consente di creare una rete virtuale e una subnet di Azure. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Consente di creare un peering tra due reti virtuali.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
