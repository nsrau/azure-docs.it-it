---
title: Come creare macchine virtuali di SQL Server con Azure PowerShell | Microsoft Docs
description: Illustra le procedure e i comandi di PowerShell necessari per la creazione di una macchina virtuale di Azure con le immagini della galleria di macchine virtuali di SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: e6d1f36d998ac8726e3a74b31772a5dd5a24bd58
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>Come creare macchine virtuali di SQL Server con Azure PowerShell

Questa guida illustra le opzioni necessarie per creare macchine virtuali di Windows SQL Server con Azure PowerShell. Per un esempio semplificato di Azure PowerShell con più valori predefiniti, vedere [Creare una macchina virtuale Windows di SQL Server con Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Configurare la sottoscrizione

1. Aprire PowerShell e accedere all'account Azure eseguendo il comando **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Viene visualizzata una schermata di accesso per l'immissione delle credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

## <a name="define-image-variables"></a>Definire le variabili di immagine
Per semplificare le operazioni di riutilizzo e la creazione degli script, iniziare definendo una serie di variabili. Cambiare i valori dei parametri in base alla necessità, ma quando si modificano i valori forniti occorre prestare attenzione alle restrizioni di denominazione correlate alle lunghezze dei nomi e ai caratteri speciali.

### <a name="location-and-resource-group"></a>Posizione e gruppo di risorse
Usare due variabili per definire l'area dei dati e il gruppo di risorse in cui devono essere create le altre risorse per la macchina virtuale.

Modificare in base alle esigenze specifiche e quindi eseguire i cmdlet seguenti per inizializzare le variabili.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Proprietà della risorsa di archiviazione
Usare le variabili seguenti per definire l'account di archiviazione e il tipo di risorsa di archiviazione che devono essere usati dalla macchina virtuale.

Modificare in base alle esigenze specifiche e quindi eseguire il cmdlet seguente per inizializzare le variabili. Si noti che in questo esempio viene usata l' [Archiviazione Premium](../premium-storage.md), consigliata per carichi di lavoro di produzione.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Proprietà di rete
Usare le variabili seguenti per definire l'interfaccia di rete, il metodo di allocazione TCP/IP, il nome della rete virtuale, il nome della subnet virtuale, l'intervallo di indirizzi IP per la rete virtuale, l'intervallo di indirizzi IP per la subnet e l'etichetta del nome del dominio pubblico che devono essere usati dalla rete nella macchina virtuale.

Modificare in base alle esigenze specifiche e quindi eseguire il cmdlet seguente per inizializzare le variabili.

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
Usare le variabili seguenti per definire il nome della macchina virtuale, il nome del computer, le dimensioni della macchina virtuale e il nome del disco del sistema operativo per la macchina virtuale.

Modificare in base alle esigenze specifiche e quindi eseguire il cmdlet seguente per inizializzare le variabili.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Scegliere un'immagine di SQL Server
Usare le variabili seguenti per definire l'immagine di SQL Server da usare per la macchina virtuale.

1. Per prima cosa, visualizzare l'elenco completo delle immagini di SQL Server disponibili con il comando **Get-AzureRmVMImageOffer**:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Per questa esercitazione, usare le variabili seguenti per specificare SQL Server 2017 in Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Visualizzare quindi l'elenco delle edizioni disponibili per la propria offerta.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Per questa esercitazione, usare l'edizione SQL Server 2017 Developer (**SQLDEV**). L'edizione Developer è concessa in licenza gratuitamente per test e sviluppo e si paga solo il costo di esecuzione della VM.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Il primo oggetto creato con il modello di distribuzione Resource Manager è il gruppo di risorse. Usare il cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) per creare un gruppo di risorse di Azure e le rispettive risorse, con nome e posizione del gruppo di risorse definiti dalle variabili inizializzate in precedenza.

Eseguire il cmdlet seguente per creare un nuovo gruppo di risorse.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
La macchina virtuale richiede risorse di archiviazione per il disco del sistema operativo e per i file di dati e di log di SQL Server. Per maggiore semplicità, viene creato un singolo disco per entrambi. Successivamente è possibile collegare dischi aggiuntivi usando il cmdlet [Add-Azure Disk](/powershell/module/azure/add-azuredisk) per posizionare i file di dati e di log di SQL Server in dischi dedicati. Usare il cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) per creare un account di archiviazione standard nel nuovo gruppo di risorse, con il nome dell'account di archiviazione, il nome dello SKU di storage e la posizione definiti usando le variabili inizializzate in precedenza.

Eseguire il cmdlet seguente per creare il nuovo account di archiviazione.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
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
Per prima cosa, creare una configurazione di subnet per la rete virtuale. Per questa esercitazione viene creata una subnet predefinita mediante il cmdlet [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). La configurazione della subnet di rete virtuale viene creata con il nome di subnet e il prefisso di indirizzo definiti usando le variabili inizializzate in precedenza.

> [!NOTE]
> È possibile definire altre proprietà della configurazione della subnet della rete virtuale usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione.

Eseguire il cmdlet seguente per creare la configurazione di subnet virtuale.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Si crea quindi la rete virtuale mediante il cmdlet [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Creare la rete virtuale nel nuovo gruppo di risorse, con nome, posizione e prefisso di indirizzo definiti usando le variabili inizializzate in precedenza e con la configurazione di subnet definita nel passaggio precedente.

Eseguire il cmdlet seguente per creare la rete virtuale.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico
Dopo avere definito la rete virtuale, è necessario configurare un indirizzo IP per la connettività alla macchina virtuale. Per questa esercitazione viene creato un indirizzo IP pubblico mediante indirizzi IP dinamici per supportare la connettività Internet. Usare il cmdlet [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) per creare l'indirizzo IP pubblico nel gruppo di risorse creato in precedenza e con nome, posizione, metodo di allocazione ed etichetta del nome di dominio DNS definiti usando le variabili inizializzate in precedenza.

> [!NOTE]
> È possibile definire altre proprietà dell'indirizzo IP pubblico usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione. È anche possibile creare un indirizzo privato o un indirizzo di tipo statico, ma anche questa procedura non rientra nell'ambito dell'esercitazione.

Eseguire il cmdlet seguente per creare l'indirizzo IP pubblico.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete
Per proteggere la macchina virtuale e il traffico di SQL Server, creare un gruppo di sicurezza di rete.

1. Per prima cosa, creare una regola del gruppo di sicurezza di rete per RDP in modo da consentire connessioni desktop remote.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configurare una regola del gruppo di sicurezza di rete che consenta il traffico sulla porta TCP 1433, in modo da permettere connessioni a SQL Server tramite Internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Creare quindi il gruppo di sicurezza di rete.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete
È ora possibile creare l'interfaccia di rete che verrà usata dalla macchina virtuale. Viene chiamato il cmdlet [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) per creare l'interfaccia di rete nel gruppo di risorse creato in precedenza e con nome, posizione, subnet e indirizzo IP pubblico definiti in precedenza.

Eseguire il cmdlet seguente per creare l'interfaccia di rete.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurare un oggetto VM
Dopo avere definito le risorse di archiviazione e di rete, è possibile definire le risorse di calcolo per la macchina virtuale. Per questa esercitazione vengono specificate le dimensioni della macchina virtuale e alcune proprietà del sistema operativo, viene specificata l'interfaccia di rete creata in precedenza, viene definito l'archivio BLOB e viene quindi specificato il disco del sistema operativo.

### <a name="create-the-vm-object"></a>Creare l'oggetto macchina virtuale
Specificare prima di tutto le dimensioni della macchina virtuale. Per questa esercitazione verrà specificata l'opzione DS13. Viene chiamato il cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) per creare un oggetto macchina virtuale configurabile con nome e dimensioni definiti usando le variabili inizializzate in precedenza.

Eseguire il cmdlet seguente per creare l'oggetto macchina virtuale.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creare un oggetto credenziali per includere il nome e la password per le credenziali di amministratore locale
Prima di configurare le proprietà del sistema operativo per la macchina virtuale, è necessario specificare le credenziali per l'account di amministratore locale sotto forma di stringa sicura. Per eseguire questa operazione usare il cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Eseguire il cmdlet seguente e nella finestra della richiesta di credenziali di PowerShell immettere il nome e la password da usare per l'account di amministratore locale nella macchina virtuale.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Configurare le proprietà del sistema operativo per la macchina virtuale
È ora possibile configurare le proprietà del sistema operativo per la macchina virtuale usando il cmdlet [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) per configurare il tipo di sistema operativo come Windows, richiedere l'installazione dell'[agente di macchine virtuali](../agent-user-guide.md), specificare che il cmdlet abilita l'aggiornamento automatico e configurare il nome della macchina virtuale, il nome del computer e le credenziali usando le variabili inizializzate in precedenza.

Eseguire il cmdlet seguente per configurare le proprietà del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Aggiungere l'interfaccia di rete alla macchina virtuale
Si aggiunge quindi alla macchina virtuale l'interfaccia di rete creata in precedenza. Chiamare il cmdlet [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) per aggiungere l'interfaccia di rete usando la variabile di interfaccia di rete definita in precedenza.

Eseguire il cmdlet seguente per configurare l'interfaccia di rete per la macchina virtuale.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Impostare la posizione dell'archivio BLOB per il disco da usare per la macchina virtuale
Si imposta quindi la posizione dell'archivio BLOB per il disco che verrà usato dalla macchina virtuale specificando le variabili definite in precedenza.

Eseguire il cmdlet seguente per impostare la posizione dell'archivio BLOB.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Configurare le proprietà del disco del sistema operativo per la macchina virtuale
Si configurano quindi le proprietà del disco del sistema operativo per la macchina virtuale. Usare il cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) per specificare che il sistema operativo per la macchina virtuale deriverà da un'immagine, per configurare la memorizzazione nella cache su sola lettura, perché SQL Server viene installato nello stesso disco e per definire il nome della macchina virtuale e il disco del sistema operativo specificati usando le variabili definite in precedenza.

Eseguire il cmdlet seguente per configurare le proprietà del disco del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Specificare l'immagine di piattaforma per la macchina virtuale
L'ultimo passaggio della configurazione consiste nello specificare l'immagine di piattaforma per la macchina virtuale. Per l'esercitazione viene usata l'immagine più recente per SQL Server 2016 CTP. Usare il cmdlet [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) per usare questa immagine in base a quanto definito dalle variabili definite in precedenza.

Eseguire il cmdlet seguente per specificare l'immagine di piattaforma per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Creare la macchina virtuale SQL
Al termine della procedura di configurazione, è possibile creare la macchina virtuale. Usare il cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) per creare la macchina virtuale usando le variabili definite.

Eseguire il cmdlet seguente per creare la macchina virtuale.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La macchina virtuale viene creata.

> [!NOTE]
> È possibile ignorare l'errore sulla diagnostica di avvio. Viene creato un account di archiviazione standard per la diagnostica dell'avvio, perché l'account di archiviazione specificato per il disco della macchina virtuale è un account di archiviazione Premium.

## <a name="install-the-sql-iaas-agent"></a>Installare SQL Iaas Agent
Le macchine virtuali di SQL Server supportano funzionalità di gestione automatica mediante l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Per installare l'agente sulla nuova VM, eseguire il comando seguente dopo la creazione.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Rimuovere una macchina virtuale di test

Se non è necessario che la VM sia continuamente in esecuzione, è possibile evitare addebiti superflui arrestandola quando non è in uso. Il comando seguente arresta la VM ma la lascia disponibile per un uso futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

È anche possibile eliminare definitivamente tutte le risorse associate alla macchina virtuale con il comando **Remove-AzureRmResourceGroup**. In questo modo viene eliminata definitivamente anche la macchina virtuale, quindi usare questo comando con cautela.

## <a name="example-script"></a>Script di esempio
Lo script seguente contiene lo script PowerShell completo per l'esercitazione. Si presuppone che sia stata già impostata la sottoscrizione di Azure da usare con i comandi **Add-AzureRmAccount** e **Select-AzureRmSubscription**.

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
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato la macchina virtuale, è possibile:

- Connettersi alla macchina virtuale tramite Desktop remoto (RDP).
- Configurare le impostazioni SQL Server nel portale relative alla macchina virtuale, tra cui:
   - [Impostazioni di archiviazione](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Attività di gestione automatica](virtual-machines-windows-sql-server-agent-extension.md)
- [Configurare la connettività](virtual-machines-windows-sql-connect.md).
- Connettere client e applicazioni alla nuova istanza di SQL Server.

