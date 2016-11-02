<properties
   pageTitle="Configurare più schede di rete in una macchina virtuale di Windows | Microsoft Azure"
   description="Informazioni su come creare una macchina virtuale con più schede di rete collegate utilizzando i modelli di Azure Resource Manager o PowerShell."
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
   ms.workload="infrastructure"
   ms.date="08/04/2016"
   ms.author="iainfou"/>

# Creazione di una macchina virtuale con più schede di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end, oppure disporre di una rete dedicata a una soluzione di monitoraggio o di backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di PowerShell, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Le differenti [dimensioni della macchina virtuale](virtual-machines-windows-sizes.md) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

>[AZURE.WARNING] È necessario collegare più schede di rete quando si crea una VM, poiché non è possibile aggiungere le schede di rete a una macchina virtuale esistente. È possibile [creare una nuova macchina virtuale basata sui dischi virtuali originali](virtual-machines-windows-specialized-image.md) e creare più schede di rete mentre si distribuisce la macchina virtuale.

## Creare le risorse principali
Verificare di aver prima [installato e configurato la versione più recente di Azure PowerShell](../powershell-install-configure.md).

Creare prima un gruppo di risorse:

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

Creare un account di archiviazione in cui salvare le VM:

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## Creare la rete virtuale e le subnet
Definire due subnet per la rete virtuale: una per il traffico front-end e l'altra per il traffico di back-end. Creare la rete virtuale con queste subnet:

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## Creare più schede di rete
Creare due schede di rete, collegarne una alla subnet di front-end e l'altra alla subnet di back-end:

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le macchine virtuali. L'articolo dettagliato sulla [macchina virtuale con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) offre una guida alla creazione di un gruppo di sicurezza di rete e all'assegnazione delle schede di rete.


## Creare la macchina virtuale
Ora è possibile iniziare con la configurazione della macchina virtuale. Ad ogni dimensione della macchina virtuale corrisponde un limite del numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine Virtuali di Windows](virtual-machines-windows-sizes.md). Nell'esempio seguente viene utilizzata una dimensione della macchina virtuale che supporta fino a due schede di rete (`Standard_DS2_v2`):

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

Collegare le due schede di rete create in precedenza:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

Configurare l'archiviazione e il disco virtuale per la nuova macchina virtuale:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

Infine, creare una macchina virtuale:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Creazione di più schede di rete utilizzando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../resource-group-create-multiple.md).

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `NIC1`, `NIC2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Passaggi successivi
Assicurarsi di consultare [Dimensioni delle macchine virtuali di Windows](virtual-machines-windows-sizes.md) durante il tentativo di creazione di una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale.

Tenere presente che non è possibile aggiungere altre schede di rete a una macchina virtuale esistente. È necessario creare tutte le schede di rete quando si distribuisce la macchina virtuale. Prestare attenzione quando si pianificano le distribuzioni per assicurarsi di avere la connettività di rete necessaria fin dall'inizio.

<!---HONumber=AcomDC_0817_2016-->