---
title: Azure Hybrid Use Benefit per Windows Server e Client Windows| Microsoft Docs
description: Informazioni su come ottimizzare i vantaggi di Software Assurance per Windows per trasferire le licenze locali in Azure
services: virtual-machines-windows
documentationcenter: 
author: george-moore
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/10/2017
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: dfa3cf27eebd04507c101fc9421f13dfef39c39f
ms.lasthandoff: 03/21/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Azure Hybrid Use Benefit per Windows Server e Client Windows
Per i clienti con Software Assurance, Azure Hybrid Use Benefit consente di usare le licenze di Windows Server e Client di Windows locali e di eseguire le macchine virtuali di Windows in Azure a costi ridotti. Azure Hybrid Use Benefit per Windows Server include Windows Server 2008R2, Windows Server 2012, Windows Server 2012R2 e Windows Server 2016. Azure Hybrid Use Benefit per Client Windows include Windows 10. Per altre informazioni, vedere la [pagina del vantaggio Azure Hybrid Use per la licenza](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

>[!IMPORTANT]
>Azure Hybrid Use Benefit per Client Windows è attualmente in anteprima. Solo i clienti Enterprise con Windows 10 Enterprise E3/E5 per utente o Windows VDA per utente (licenze di sottoscrizione utente o le licenze di sottoscrizione utente per i componenti aggiuntivi) idonei ("Licenze di qualificazione") sono idonei.
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Modalità di utilizzo del vantaggio Azure Hybrid Use
Esistono due modi per distribuire le macchine virtuali Windows con il vantaggio Azure Hybrid Use:

1. Se si dispone di una sottoscrizione Enterprise Agreement, è possibile [distribuire le VM da immagini del Marketplace specifiche](#deploy-a-vm-using-the-azure-marketplace) che sono preconfigurate con il vantaggio Azure Hybrid Use.
2. Se non si dispone della sottoscrizione Enterprise Agreement, è possibile [caricare una macchina virtuale personalizzata](#upload-a-windows-vhd) e [distribuirla usando un modello di Resource Manager](#deploy-a-vm-via-resource-manager) o [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Distribuire una VM usando Azure Marketplace
Per i clienti che dispongono di [sottoscrizioni Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx), le immagini sono disponibili nel Marketplace preconfigurate con il vantaggio Azure Hybrid Use. Queste immagini possono essere distribuite direttamente, ad esempio dal portale di Azure, da modelli di Resource Manager o da Azure PowerShell. Le immagini nel Marketplace sono indicate con il nome `[HUB]` come segue:

![Immagini con vantaggio Azure Hybrid Use in Azure Marketplace](./media/virtual-machines-windows-hybrid-use-benefit/ahub-images-portal.png)

È possibile distribuire queste immagini direttamente dal portale di Azure. Per l'utilizzo in modelli di Resource Manager e con Azure PowerShell, visualizzare l'elenco delle immagini come segue:

Per Windows Server:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

Per Client Windows:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

Se non si dispone di una sottoscrizione Enterprise Agreement, continuare a leggere per istruzioni su come caricare una VM personalizzata e distribuirla con il vantaggio Azure Hybrid Use.


## <a name="upload-a-windows-vhd"></a>Caricare un disco rigido virtuale Windows
Per distribuire una macchina virtuale Windows in Azure è prima necessario creare un disco rigido virtuale contenente la build di base di Windows. Questo disco rigido virtuale deve essere correttamente preparato con Sysprep prima di caricarlo in Azure. Sono disponibili [altre informazioni sui requisiti dei dischi rigidi virtuali e sul processo Sysprep](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Eseguire il backup della VM prima di eseguire Sysprep. 

Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azureps-cmdlets-docs). Dopo aver preparato il disco rigido virtuale, caricarlo nell'account di archiviazione di Azure usando il cmdlet `Add-AzureRmVhd` come segue:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server e Dynamics possono usare anche le licenza di Software Assurance. È comunque necessario preparare l'immagine di Windows Server installando i componenti dell'applicazione e specificando i codici di licenza corrispondenti, quindi caricando l'immagine del disco in Azure. Esaminare la documentazione appropriata per l'esecuzione di SysPrep con l'applicazione, ad esempio [Considerazioni sull'installazione di SQL Server tramite SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) o [Creare un'immagine di riferimento di SharePoint Server 2016 con Sysprep](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Altre informazioni sul [caricamento del disco rigido virtuale in Azure](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Distribuire una VM caricata tramite Resource Manager
Nei modelli di Resource Manager è possibile specificare un parametro aggiuntivo per `licenseType`. Altre informazioni sulla [creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Dopo aver caricato il disco rigido virtuale in Azure, modificare il modello di Resource Manager per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello come di consueto:

Per Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Per Client Windows:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>Distribuire una VM caricata tramite l'avvio rapido di PowerShell
Quando si distribuisce la macchina virtuale Windows Server con PowerShell è presente un parametro aggiuntivo per `-LicenseType`. Dopo aver caricato il disco rigido virtuale in Azure, creare una nuova VM usando `New-AzureRmVM` e specificare il tipo di licenza come segue:

Per Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Per Client Windows:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

È possibile [leggere una procedura più dettagliata sulla distribuzione di una VM in Azure con PowerShell](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) più avanti nel documento oppure vedere una guida più descrittiva con i diversi passaggi per [creare una VM Windows usando Resource Manager e PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale usi il vantaggio della licenza
Dopo avere distribuito la VM con il metodo di distribuzione tramite PowerShell o Resource Manager, verificare il tipo di licenza con `Get-AzureRmVM` come indicato di seguito:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

L'output è simile all'esempio seguente per Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

L'output differisce da quello della seguente VM distribuita senza vantaggio Azure Hybrid Use, quale ad esempio una VM distribuita direttamente dalla raccolta di Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>Procedura dettagliata per la distribuzione con PowerShell
La procedura dettagliata per PowerShell seguente illustra la distribuzione completa di una VM. Altre informazioni sui cmdlet effettivi e sui diversi componenti creati sono disponibili in [Creare una VM Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Verranno creati il gruppo di risorse, l'account di archiviazione e la rete virtuale, quindi verrà definita e creata la VM.

Ottenere prima le credenziali in modo sicuro, specificare una località e definire un nome per il gruppo di risorse:

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

Creare un IP pubblico:

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

Definire la subnet, la scheda di interfaccia di rete e la rete virtuale:

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Assegnare un nome alla macchina virtuale e creare una configurazione:

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Definire il sistema operativo:

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Aggiungere la scheda di interfaccia di rete alla macchina virtuale:

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Definire l'account di archiviazione da usare:

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

Caricare il disco rigido virtuale adeguatamente preparato e collegarlo alla macchina virtuale:

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Creare infine la macchina virtuale e definire il tipo di licenza per l'uso del vantaggio Azure Hybrid Use:

Per Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

Per Client Windows:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Client"
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul [Vantaggio Microsoft Azure Hybrid Use](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Altre informazioni sull' [uso dei modelli di Resource Manager](../azure-resource-manager/resource-group-overview.md).

