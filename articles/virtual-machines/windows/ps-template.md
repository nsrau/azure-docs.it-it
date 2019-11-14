---
title: Creare una macchina virtuale Windows da un modello in Azure
description: Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065557"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Creare una macchina virtuale Windows usando un modello di Resource Manager

Informazioni su come creare una macchina virtuale Windows usando un modello di Azure Resource Manager e Azure PowerShell da Azure cloud Shell. Il modello usato in questo articolo distribuisce una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con una singola subnet. Per la creazione di una macchina virtuale Linux, vedere [come creare una macchina virtuale Linux con modelli di Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

La creazione di una macchina virtuale di Azure include in genere due passaggi:

- Creare un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale.
- Creare una macchina virtuale.

L'esempio seguente crea una macchina virtuale da un [modello di avvio rapido di Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Di seguito è riportata una copia del modello:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Per eseguire lo script di PowerShell, selezionare **prova** per aprire Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

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

Se si sceglie di installare e usare PowerShell localmente anziché da Azure cloud Shell, per questa esercitazione è necessario il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installare il modulo di Azure PowerShell). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Nell'esempio precedente è stato specificato un modello archiviato in GitHub. È anche possibile scaricare o creare un modello e specificare il percorso locale con il parametro `--template-file`.

Altre risorse:

- Per informazioni su come sviluppare modelli di Resource Manager, vedere [Documentazione di Resource Manager in Azure](/azure/azure-resource-manager/).
- Per visualizzare gli schemi delle macchine virtuali di Azure, vedere informazioni di [riferimento sui modelli di Azure](/azure/templates/microsoft.compute/allversions).
- Per visualizzare altri esempi di modelli di macchina virtuale, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

L'ultimo comando di PowerShell dello script precedente Mostra il nome della macchina virtuale. Per connettersi alla macchina virtuale, vedere [come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](./connect-logon.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Informazioni su come creare e gestire una macchina virtuale sono disponibili in [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sulla creazione di modelli, vedere la sintassi e le proprietà JSON dei tipi di risorse distribuiti:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
