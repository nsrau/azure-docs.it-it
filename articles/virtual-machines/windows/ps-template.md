---
title: Creare una macchina virtuale Windows usando un modello di Azure | Documentazione Microsoft
description: Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58c8ca9bb9b4dfd58c801eb4dd1dad95c1969c18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127617"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Creare una macchina virtuale Windows usando un modello di Resource Manager

Informazioni su come creare una macchina virtuale Windows usando un modello di Azure Resource Manager e Azure PowerShell da Azure Cloud shell. Il modello usato in questo articolo consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con una sola subnet. Per la creazione di una macchina virtuale Linux, vedere [come creare una macchina virtuale Linux con modelli Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creazione di una macchina virtuale di Azure in genere include due passaggi:

- Creare un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale.
- Creare una macchina virtuale.

L'esempio seguente crea una macchina virtuale da un [modello di avvio rapido di Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Di seguito è riportata una copia del modello:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Per eseguire lo script di PowerShell, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere **incollare**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Se si sceglie di installare e usare PowerShell in locale invece che da Azure Cloud shell, questa esercitazione richiede il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Nell'esempio precedente è stato specificato un modello archiviato in GitHub. È anche possibile scaricare o creare un modello e specificare il percorso locale con il parametro `--template-file`.

Altre risorse:

- Per informazioni su come sviluppare modelli di Resource Manager, vedere [Documentazione di Resource Manager in Azure](/azure/azure-resource-manager/).
- Per visualizzare gli schemi di macchina virtuale di Azure, vedere [riferimento a un modello di Azure](/azure/templates/microsoft.compute/allversions).
- Per ulteriori esempi di modelli di macchina virtuale, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

L'ultimo comando di PowerShell dello script precedente mostra il nome della macchina virtuale. Per connettersi alla macchina virtuale, vedere [come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](./connect-logon.md).

## <a name="next-steps"></a>Fasi successive

- Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Informazioni su come creare e gestire una macchina virtuale sono disponibili in [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sulla creazione di modelli, vedere la sintassi e le proprietà JSON dei tipi di risorse distribuiti:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
