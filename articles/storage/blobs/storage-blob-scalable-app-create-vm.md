---
title: Creare una VM e un account di archiviazione per un'applicazione scalabile in Azure | Microsoft Docs
description: Informazioni su come distribuire una VM da usare per l'esecuzione di un'applicazione scalabile che usa un archivio BLOB di Azure
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: bd235193c5eee5587cfff6dac84c3378ecb44ce1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246552"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Creare una macchina virtuale e un account di archiviazione per un'applicazione scalabile

Questa è la prima di una serie di esercitazioni. Questa esercitazione illustra come distribuire un'applicazione che carica e scarica grandi quantità di dati casuali con un account di archiviazione di Azure. Al termine si avrà un'applicazione console in esecuzione in una macchina virtuale usata per il caricamento e il download di grandi quantità di dati in un account di archiviazione.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare una macchina virtuale
> * Configurare un'estensione di script personalizzata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Az di Azure PowerShell versione 0.7 o successive. Eseguire ` Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
 
L'esempio carica 50 file di grandi dimensioni in un contenitore BLOB in un account di archiviazione di Azure. Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti. Creare un account di archiviazione nel gruppo di risorse creato usando il comando [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount).

Nel comando seguente sostituire il segnaposto `<blob_storage_account>` con il nome globalmente univoco dell'account di archiviazione BLOB.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una configurazione di macchina virtuale. Questa configurazione include le impostazioni utilizzate quando si distribuisce la macchina virtuale, ad esempio l'immagine della macchina virtuale, la dimensione e la configurazione di autenticazione. Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale.

Creare la macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Distribuire la configurazione

Per questa esercitazione, è necessario che nella macchina virtuale siano installati alcuni prerequisiti. L'estensione di script personalizzata viene usata per eseguire uno script di PowerShell che completa le attività seguenti:

> [!div class="checklist"]
> * Installazione di .NET Core 2.0
> * Installazione di chocolatey
> * Installazione di GIT
> * Clonazione del repository di esempio
> * Ripristino dei pacchetti NuGet
> * Creazione di 50 file da 1 GB con dati casuali

Eseguire il cmdlet seguente per finalizzare la configurazione della macchina virtuale. Il completamento di questo passaggio richiede 5-15 minuti.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie si è appreso come creare un account di archiviazione, distribuire una macchina virtuale e configurarla con i prerequisiti necessari, ad esempio come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare una macchina virtuale
> * Configurare un'estensione di script personalizzata

Passare alla seconda parte della serie per caricare grandi quantità di dati in un account di archiviazione usando parallelismo e ripetizione esponenziale dei tentativi.

> [!div class="nextstepaction"]
> [Caricare grandi quantità di dati casuali in parallelo in Archiviazione di Azure](storage-blob-scalable-app-upload-files.md)
