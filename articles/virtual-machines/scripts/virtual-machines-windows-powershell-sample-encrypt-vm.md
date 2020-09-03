---
title: Esempio di script di Azure PowerShell - Crittografare una VM Windows
description: Esempio di script di Azure PowerShell - Crittografare una VM Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 102187d52ec86a7a87223975ae3d1f6bbe9097d7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078776"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Crittografare una macchina virtuale Windows con Azure PowerShell

Questo script crea un Azure Key Vault sicuro, le chiavi di crittografia, un'entità servizio di Azure Active Directory e una macchina virtuale (VM) Windows. La VM viene quindi crittografata usando la chiave di crittografia del Key Vault e le credenziali dell'entità servizio.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | Crea un Azure Key Vault per archiviare i dati protetti, ad esempio le chiavi di crittografia. |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | Crea una chiave di crittografia in Key Vault. |
| [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | Crea un'entità servizio di Azure Active Directory per autenticare in modo sicuro e controllare l'accesso alle chiavi di crittografia. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Imposta le autorizzazioni in Key Vault per concedere l'accesso dell'entità servizio alle chiavi di crittografia. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando apre anche la porta 80 e imposta le credenziali amministrative. |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | Ottiene le informazioni necessarie in Key Vault |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | Abilita la crittografia in una VM usando le credenziali dell'entità servizio e la chiave di crittografia. |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Mostra lo stato del processo di crittografia della VM. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
