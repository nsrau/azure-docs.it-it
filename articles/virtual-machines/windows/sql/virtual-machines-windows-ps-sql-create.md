---
title: Guida al provisioning per macchine virtuali di SQL Server con Azure PowerShell | Microsoft Docs
description: Illustra le procedure e i comandi di PowerShell necessari per la creazione di una macchina virtuale di Azure con le immagini della galleria di macchine virtuali di SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c37347928e75986fa8aee0d3d0a4db2667312f56
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984376"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell

Questa guida illustra le opzioni necessarie per creare macchine virtuali di Windows SQL Server con Azure PowerShell. Per un esempio semplificato di Azure PowerShell con più valori predefiniti, vedere [Creare una macchina virtuale Windows di SQL Server con Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Configurare la sottoscrizione

1. Aprire PowerShell e accedere all'account Azure eseguendo il comando **Connect-AzAccount**.

   ```PowerShell
   Connect-AzAccount
   ```

1. Viene visualizzata una schermata per l'immissione delle credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

## <a name="define-image-variables"></a>Definire le variabili di immagine
Per riutilizzare i valori e semplificare la creazione degli script, iniziare definendo una serie di variabili. Cambiare i valori dei parametri in base alla necessità, ma quando si modificano i valori forniti occorre prestare attenzione alle restrizioni di denominazione correlate alle lunghezze dei nomi e ai caratteri speciali.

### <a name="location-and-resource-group"></a>Posizione e gruppo di risorse
Definire l'area dati e il gruppo di risorse in cui devono essere create le altre risorse per la macchina virtuale.

Modificare come desiderato e quindi eseguire questi cmdlet per inizializzare le variabili.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Proprietà della risorsa di archiviazione
Definire l'account di archiviazione e il tipo di risorsa di archiviazione che devono essere usati dalla macchina virtuale.

Modificare come desiderato e quindi eseguire il cmdlet seguente per inizializzare le variabili. Per i carichi di lavoro di produzione è consigliabile usare l'[Archiviazione Premium](../premium-storage.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Proprietà di rete
Definire le proprietà da usare per la rete nella macchina virtuale. 

- interfaccia di rete
- Metodo di allocazione TCP/IP
- Nome della rete virtuale
- Nome della subnet virtuale
- Intervallo di indirizzi IP per la rete virtuale
- Intervallo di indirizzi IP per la subnet
- Etichetta di nome di dominio pubblico

Modificare come desiderato e quindi eseguire questo cmdlet per inizializzare le variabili.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Proprietà della macchina virtuale
Definire il nome della macchina virtuale, il nome del computer, le dimensioni della macchina virtuale e il nome del disco del sistema operativo per la macchina virtuale.

Modificare come desiderato e quindi eseguire questo cmdlet per inizializzare le variabili.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Scegliere un'immagine di SQL Server

Usare le variabili seguenti per definire l'immagine di SQL Server da usare per la macchina virtuale. 

1. Per prima cosa, visualizzare l'elenco completo delle immagini di SQL Server disponibili con il comando `Get-AzVMImageOffer`. Questo comando elenca le immagini correnti disponibili nel portale di Azure e anche le immagini precedenti che possono essere installate solo con PowerShell:

   ```PowerShell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Per questa esercitazione, usare le variabili seguenti per specificare SQL Server 2017 in Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Visualizzare quindi l'elenco delle edizioni disponibili per la propria offerta.

   ```PowerShell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Per questa esercitazione, usare l'edizione SQL Server 2017 Developer (**SQLDEV**). L'edizione Developer è concessa in licenza gratuitamente per test e sviluppo e si paga solo il costo di esecuzione della VM.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Il primo oggetto creato con il modello di distribuzione Resource Manager è il gruppo di risorse. Usare il cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) per creare un gruppo di risorse di Azure e le relative risorse. Specificare le variabili inizializzate in precedenza per nome e posizione del gruppo di risorse.

Eseguire questo cmdlet per creare un nuovo gruppo di risorse.

```PowerShell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
La macchina virtuale richiede risorse di archiviazione per il disco del sistema operativo e per i file di dati e di log di SQL Server. Per semplificare, verrà creato un singolo disco per entrambi. Successivamente è possibile collegare dischi aggiuntivi usando il cmdlet [Add-Azure Disk](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) per posizionare i file di dati e di log di SQL Server in dischi dedicati. Usare il cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) per creare un account di archiviazione standard nel nuovo gruppo di risorse. Specificare le variabili inizializzate in precedenza per nome dell'account di archiviazione, nome dello SKU di archiviazione e posizione.

Eseguire questo cmdlet per creare il nuovo account di archiviazione.

```PowerShell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> La creazione dell'account di archiviazione può richiedere alcuni minuti.

## <a name="create-network-resources"></a>Creare risorse di rete
La macchina virtuale richiede alcune risorse di rete per la connettività di rete.

* Ogni macchina virtuale richiede una rete virtuale.
* In una rete virtuale deve essere definita almeno una subnet.
* È necessario che sia definita un'interfaccia di rete con un indirizzo IP pubblico o privato.

### <a name="create-a-virtual-network-subnet-configuration"></a>Creare una configurazione di subnet di rete virtuale
Per prima cosa, creare una configurazione di subnet per la rete virtuale. Per questa esercitazione, creare una subnet predefinita mediante il cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Specificare le variabili inizializzate in precedenza per nome della subnet, posizione e prefisso dell'indirizzo.

> [!NOTE]
> È possibile definire altre proprietà della configurazione della subnet della rete virtuale usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione.

Eseguire questo cmdlet per creare la configurazione di subnet virtuale.

```PowerShell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare quindi la rete virtuale nel nuovo gruppo di risorse mediante il cmdlet [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). Specificare le variabili inizializzate in precedenza per nome, posizione e prefisso dell'indirizzo. Usare la configurazione di subnet definita nel passaggio precedente.

Eseguire questo cmdlet per creare la rete virtuale.

```PowerShell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico
Dopo avere definito la rete virtuale, è necessario configurare un indirizzo IP per la connettività alla macchina virtuale. Per questa esercitazione, creare un indirizzo IP pubblico mediante indirizzi IP dinamici per supportare la connettività Internet. Usare il cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) per creare l'indirizzo IP pubblico nel nuovo gruppo di risorse. Specificare le variabili inizializzate in precedenza per nome, posizione, metodo di allocazione ed etichetta del nome di dominio DNS.

> [!NOTE]
> È possibile definire altre proprietà dell'indirizzo IP pubblico usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione. È anche possibile creare un indirizzo privato o un indirizzo di tipo statico, ma anche questa procedura non rientra nell'ambito dell'esercitazione.

Eseguire questo cmdlet per creare l'indirizzo IP pubblico.

```PowerShell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete
Per proteggere la macchina virtuale e il traffico di SQL Server, creare un gruppo di sicurezza di rete.

1. Per prima cosa, creare una regola del gruppo di sicurezza di rete per RDP in modo da consentire connessioni desktop remote.

   ```PowerShell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configurare una regola del gruppo di sicurezza di rete che consenta il traffico sulla porta TCP 1433, in modo da permettere connessioni a SQL Server tramite Internet.

   ```PowerShell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Creare il gruppo di sicurezza di rete.

   ```PowerShell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete
È ora possibile creare l'interfaccia di rete per la macchina virtuale. Usare il cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) per creare l'interfaccia di rete nel nuovo gruppo di risorse. Specificare il nome, la posizione, la subnet e l'indirizzo IP pubblico definiti in precedenza.

Eseguire questo cmdlet per creare l'interfaccia di rete.

```PowerShell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurare un oggetto VM
Dopo avere definito le risorse di archiviazione e di rete, è possibile definire le risorse di calcolo per la macchina virtuale.

- Specificare le dimensioni della macchina virtuale e varie proprietà del sistema operativo.
- Specificare l'interfaccia di rete creata in precedenza.
- Definire l'archivio BLOB.
- Specificare il disco del sistema operativo.

### <a name="create-the-vm-object"></a>Creare l'oggetto macchina virtuale
Specificare prima di tutto le dimensioni della macchina virtuale. Per questa esercitazione, specificare DS13. Usare il cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) per creare un oggetto macchina virtuale configurabile. Specificare le variabili inizializzate in precedenza per nome e dimensioni.

Eseguire questo cmdlet per creare l'oggetto macchina virtuale.

```PowerShell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creare un oggetto credenziali per includere il nome e la password per le credenziali di amministratore locale
Prima che sia possibile configurare le proprietà del sistema operativo per la macchina virtuale, è necessario specificare le credenziali per l'account di amministratore locale sotto forma di stringa sicura. Per eseguire questa operazione usare il cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Eseguire il cmdlet seguente e nella finestra della richiesta di credenziali di PowerShell immettere il nome e la password da usare per l'account di amministratore locale nella macchina virtuale.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Configurare le proprietà del sistema operativo per la macchina virtuale
A questo punto, è possibile impostare le proprietà del sistema operativo della macchina virtuale con il cmdlet [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem).

- Impostare il tipo di sistema operativo Windows.
- Richiedere l'installazione dell'[agente per la macchina virtuale](../../extensions/agent-windows.md).
- Specificare che il cmdlet abilita l'aggiornamento automatico.
- Specificare le variabili inizializzate in precedenza per il nome della macchina virtuale, il nome del computer e le credenziali.

Eseguire questo cmdlet per configurare le proprietà del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Aggiungere l'interfaccia di rete alla macchina virtuale
Successivamente, usare il cmdlet [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) per aggiungere l'interfaccia di rete usando la variabile definita in precedenza.

Eseguire questo cmdlet per configurare l'interfaccia di rete per la macchina virtuale.

```PowerShell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Impostare la posizione dell'archivio BLOB per il disco da usare per la macchina virtuale
A questo punto, impostare la posizione dell'archivio BLOB per il disco della macchina virtuale specificando le variabili definite in precedenza.

Eseguire questo cmdlet per impostare la posizione dell'archivio BLOB.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Configurare le proprietà del disco del sistema operativo per la macchina virtuale
Configurare quindi le proprietà del disco del sistema operativo per la macchina virtuale usando il cmdlet [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). 

- Specificare che il sistema operativo per la macchina virtuale verrà da un'immagine.
- Impostare la memorizzazione nella cache sulla modalità di sola lettura, perché SQL Server viene installato nello stesso disco.
- Specificare le variabili inizializzate in precedenza per nome della macchina virtuale e disco del sistema operativo.

Eseguire questo cmdlet per configurare le proprietà del disco del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Specificare l'immagine di piattaforma per la macchina virtuale
L'ultimo passaggio della configurazione consiste nello specificare l'immagine della piattaforma per la macchina virtuale. Per questa esercitazione, usare l'immagine più recente per SQL Server 2016 CTP. Usare il cmdlet [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) per usare questa immagine con le variabili definite in precedenza.

Eseguire questo cmdlet per specificare l'immagine della piattaforma per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Creare la macchina virtuale SQL
Al termine della procedura di configurazione, è possibile creare la macchina virtuale. Usare il cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) per creare la macchina virtuale usando le variabili definite.

> [!TIP]
> La creazione della macchina virtuale può richiedere alcuni minuti.

Eseguire questo cmdlet per creare la macchina virtuale.

```PowerShell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La macchina virtuale viene creata.

> [!NOTE]
> Se si verifica un errore sulla diagnostica di avvio, è possibile ignorarlo. Viene creato un account di archiviazione standard per la diagnostica di avvio, perché l'account di archiviazione specificato per il disco della macchina virtuale è un account di archiviazione Premium.

## <a name="install-the-sql-iaas-agent"></a>Installare SQL Iaas Agent
Le macchine virtuali di SQL Server supportano funzionalità di gestione automatica mediante l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Per installare l'agente nella nuova macchina virtuale, eseguire il comando seguente dopo la creazione.


   ```PowerShell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Arrestare o rimuovere una macchina virtuale

Se non è necessario che la macchina virtuale sia continuamente in esecuzione, è possibile evitare addebiti superflui arrestandola quando non è in uso. Il comando seguente arresta la VM ma la lascia disponibile per un uso futuro.

```PowerShell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

È anche possibile eliminare definitivamente tutte le risorse associate alla macchina virtuale con il comando **Remove-AzResourceGroup**. In questo modo viene eliminata definitivamente anche la macchina virtuale, quindi usare questo comando con cautela.

## <a name="example-script"></a>Script di esempio
Lo script seguente contiene lo script PowerShell completo per l'esercitazione. Si presuppone che sia già stata impostata la sottoscrizione di Azure da usare con i comandi **Connect-AzAccount** e **Select-AzSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato la macchina virtuale, è possibile:

- Connettersi alla macchina virtuale tramite RDP
- Configurare le impostazioni SQL Server nel portale relative alla macchina virtuale, tra cui:
   - [Impostazioni di archiviazione](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Attività di gestione automatica](virtual-machines-windows-sql-server-agent-extension.md)
- [Configurare la connettività](virtual-machines-windows-sql-connect.md)
- Connettere client e applicazioni alla nuova istanza di SQL Server

