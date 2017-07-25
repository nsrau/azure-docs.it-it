---
title: 'Esempio di script di Azure PowerShell: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 5ae87c485f06f9eec6b64e8a072e334c73b56a47
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---

# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Copiare lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con PowerShell

Questo script crea una copia di uno snapshot nella stessa sottoscrizione stessa o in una sottoscrizione diversa. Usare questo script per spostare uno snapshot in un'altra sottoscrizione per la conservazione dei dati. L'archiviazione di snapshot in una sottoscrizione diversa impedisce l'eliminazione accidentale di snapshot nella sottoscrizione principale. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/storage/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copia snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare uno snapshot nella sottoscrizione di destinazione usando l'ID dello snapshot di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Crea la configurazione di snapshot usata per la creazione di snapshot. Include l'ID risorsa dello snapshot padre e il percorso che è identico a quello dello snapshot padre.  |
| [New-AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crea uno snapshot accettando come parametri la configurazione dello snapshot, il nome dello snapshot e il nome del gruppo di risorse. |


## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale da uno snapshot](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
