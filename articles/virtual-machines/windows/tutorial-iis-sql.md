---
title: 'Esercitazione: Creare macchine virtuali che eseguono uno stack SQL, IIS, .NET in Azure| Microsoft Docs'
description: In questa esercitazione viene descritto come installare lo stack SQL di Azure, IIS e .NET in una macchina virtuale Windows in Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c5fa818a7f0d25a91ea6d272c30384de4c1765b3
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887105"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Esercitazione: Installare lo stack SQL, IIS, .NET in una macchina virtuale Windows con Azure PowerShell

In questa esercitazione verrà installato uno stack SQL, IIS, .NET usando Azure PowerShell. Questo stack è costituito da due VM che eseguono Windows Server 2016, una con IIS e .NET e l'altra con SQL Server.

> [!div class="checklist"]
> * Creare una macchina virtuale 
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo AzureRM.Compute versione 5.7.0 o successive. Eseguire `Get-Module -ListAvailable AzureRM.Compute` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Creare una VM IIS 

In questo esempio viene usato il cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) in Cloud Shell di PowerShell per creare rapidamente una macchina virtuale Windows Server 2016 e quindi installare IIS e .NET Framework. Poiché le VM IIS e SQL condividono un gruppo di risorse e una rete virtuale, vengono create le variabili per tali nomi.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installare IIS e .NET Framework usando l'estensione per script personalizzati con il cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension).

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Creare un'altra subnet

Creare una seconda subnet per la macchina virtuale SQL. Ottenere la rete virtuale con [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Creare una configurazione per la subnet usando [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Aggiornare la rete virtuale con le informazioni sulla nuova subnet con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM di Azure SQL

Per creare la macchina virtuale SQL, usare un'immagine preconfigurata di Azure Marketplace di un server SQL. Prima viene creata la VM, quindi l'estensione SQL Server viene installata nella VM. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Usare [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) per aggiungere l'[estensione SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) alla VM SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è installato uno stack SQL&#92;IIS&#92;.NET usando Azure PowerShell. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale 
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server

Passare all'esercitazione successiva per apprendere come proteggere il server Web IIS con i certificati SSL.

> [!div class="nextstepaction"]
> [Proteggere il server Web IIS con i certificati SSL](tutorial-secure-web-server.md)

