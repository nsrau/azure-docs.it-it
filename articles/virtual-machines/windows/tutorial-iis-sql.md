---
title: Creare VM che eseguono uno stack SQL&#92;IIS&#92;.NET in Azure| Microsoft Docs
description: 'Esercitazione: Installare uno stack Azure SQL, IIS, .NET in una macchina virtuale Windows.'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Installare uno stack SQL&#92;IIS&#92;.NET in Azure

In questa esercitazione viene installato uno stack SQL&#92;IIS&#92;.NET usando Azure PowerShell. Questo stack è costituito da due VM che eseguono Windows Server 2016, una con IIS e .NET e l'altra con SQL Server.

> [!div class="checklist"]
> * Creare una macchina virtuale usando New-AzVM
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server



## <a name="create-a-iis-vm"></a>Creare una VM IIS 

In questo esempio viene usato il cmdlet [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) in Cloud Shell di PowerShell per creare rapidamente una VM Windows Server 2016 e quindi installare IIS e .NET Framework. Poiché le VM IIS e SQL condividono un gruppo di risorse e una rete virtuale, vengono create le variabili per tali nomi.

Fare clic sul pulsante **Prova** nell'angolo in alto a destra del blocco di codice per avviare Cloud Shell in questa finestra. Verrà chiesto di specificare le credenziali per la macchina virtuale al prompt dei comandi.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Installare IIS e .NET Framework usando l'estensione di script personalizzata.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>VM di Azure SQL

Per creare la VM SQL, viene usata un'immagine preconfigurata di Azure Marketplace di un server SQL. Prima viene creata la VM, quindi l'estensione SQL Server viene installata nella VM. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Usare [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) per aggiungere l'[estensione SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) alla VM SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è installato uno stack SQL&#92;IIS&#92;.NET usando Azure PowerShell. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale usando New-AzVM
> * Installare IIS e .NET Core SDK nella VM
> * Creare una VM che esegue SQL Server
> * Installare l'estensione SQL Server

Passare all'esercitazione successiva per apprendere come proteggere il server Web IIS con i certificati SSL.

> [!div class="nextstepaction"]
> [Proteggere il server Web IIS con i certificati SSL](tutorial-secure-web-server.md)

