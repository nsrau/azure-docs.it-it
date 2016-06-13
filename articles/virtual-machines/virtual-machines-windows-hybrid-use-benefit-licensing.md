<properties
   pageTitle="Vantaggio Azure Hybrid Use per Windows Server | Microsoft Azure"
   description="Informazioni su come ottimizzare i vantaggi di Software Assurance per Windows Server per trasferire le licenze locali in Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/03/2016"
   ms.author="georgem"/>

# Vantaggio Azure Hybrid Use per Windows Server

Per i clienti che usano Windows Server con Software Assurance è possibile trasferire le licenze locali di Windows Server in Azure ed eseguire macchine virtuali Windows Server in Azure a costi ridotti. Il vantaggio Azure Hybrid Use consente di eseguire macchine virtuali Windows Server in Azure alla sola tariffa di calcolo di base. Per altre informazioni, vedere la [pagina del vantaggio Azure Hybrid Use per la licenza](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Questo articolo spiega come distribuire macchine virtuali Windows Server in Azure per usare questo vantaggio.

> [AZURE.NOTE] Il vantaggio Azure Hybrid Use non consente di usare immagini di Azure Marketplace per distribuire macchine virtuali Windows Server. È necessario distribuire le macchine virtuali usando PowerShell o modelli di Resource Manager per registrare correttamente le macchine virtuali come idonee per la tariffa di calcolo di base.

## Prerequisiti
Per usufruire del vantaggio Azure Hybrid Use per macchine virtuali Windows Server è necessario soddisfare alcuni requisiti:

- Disporre del modulo Azure PowerShell
- Disporre di un disco rigido virtuale di Windows Server da caricare in Archiviazione di Azure

### Installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione da usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Anche se si intende distribuire le macchine virtuali tramite modelli di Resource Manager sarà comunque necessario installare Azure PowerShell per caricare il disco rigido virtuale di Windows Server. Vedere il passaggio successivo.

### Caricare un disco rigido virtuale di Windows Server

Per distribuire una macchina virtuale Windows Server in Azure è prima necessario creare un disco rigido virtuale contenente la build di base di Windows Server. Questo disco rigido virtuale deve essere correttamente preparato con Sysprep prima di caricarlo in Azure. Sono disponibili [altre informazioni sui requisiti dei dischi rigidi virtuali e sul processo Sysprep](./virtual-machines-windows-upload-image.md). Dopo aver preparato il disco rigido virtuale, caricarlo nell'account di archiviazione di Azure usando il `Add-AzureRmVhd` cmdlet come segue:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

Altre informazioni sul [caricamento del disco rigido virtuale in Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Questo articolo è incentrato sulla distribuzione di macchine virtuali Windows Server; è tuttavia anche possibile distribuire macchine virtuali di client Windows nello stesso modo. Negli esempi seguenti, sostituire `Server` con `Client`.

## Avvio rapido: Distribuire una macchina virtuale con PowerShell
Quando si distribuisce la macchina virtuale Windows Server con PowerShell è presente un parametro aggiuntivo per `-LicenseType`. Dopo aver caricato il disco rigido virtuale in Azure, creare una nuova macchina virtuale usando `New-AzureRmVM` e specificare il tipo di licenza come segue:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm
    -LicenseType Windows_Server
```

È possibile [leggere una procedura più dettagliata sulla distribuzione di una macchina virtuale in Azure con PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) più avanti nel documento oppure una guida più descrittiva con i diversi passaggi per [creare una macchina virtuale Windows usando Resource Manager e PowerShell](./virtual-machines-windows-ps-create.md).

## Distribuire una macchina virtuale con Resource Manager
Nei modelli di Resource Manager è possibile specificare un parametro aggiuntivo per `licenseType`. Altre informazioni sulla [creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md). Dopo aver caricato il disco rigido virtuale in Azure, modificare il modello di Resource Manager per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello come di consueto:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## Verificare che la macchina virtuale usi il vantaggio della licenza
Dopo avere distribuito la macchina virtuale con il metodo PowerShell o Resource Manager, verificare il tipo di licenza con `Get-AzureRmVM` come indicato di seguito:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

L'output sarà simile al seguente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

L'output differisce da quello della macchina virtuale seguente distribuita senza vantaggio Azure Hybrid Use, ad esempio una macchina virtuale distribuita direttamente dalla raccolta di Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## Procedura dettagliata per PowerShell

La procedura dettagliata per PowerShell seguente illustra la distribuzione completa di una macchina virtuale. Altre informazioni sui cmdlet effettivi e sui diversi componenti creati sono disponibili in [Creare una VM Windows con Resource Manager e PowerShell](./virtual-machines-windows-ps-create.md). Verranno creati il gruppo di risorse, l'account di archiviazione e la rete virtuale, quindi verrà definita e creata la macchina virtuale.
 
Ottenere prima le credenziali in modo sicuro, specificare una località e definire un nome per il gruppo di risorse:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Creare un IP pubblico:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Definire la subnet, la scheda di interfaccia di rete e la rete virtuale:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Assegnare un nome alla macchina virtuale e creare una configurazione:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Definire il sistema operativo:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Aggiungere la scheda di interfaccia di rete alla macchina virtuale:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Definire l'account di archiviazione da usare:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Caricare il disco rigido virtuale adeguatamente preparato e collegarlo alla macchina virtuale:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Creare infine la macchina virtuale e definire il tipo di licenza per l'uso del vantaggio Azure Hybrid Use:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## Passaggi successivi

Altre informazioni sul [vantaggio Azure Hybrid Use](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Altre informazioni sull'[uso dei modelli di Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0601_2016-->