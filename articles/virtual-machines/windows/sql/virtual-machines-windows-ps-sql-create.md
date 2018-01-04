---
title: Come creare le macchine virtuali di SQL Server con Azure PowerShell | Documenti Microsoft
description: Fornisce i passaggi e i comandi di PowerShell per la creazione di una macchina virtuale di Azure con immagini della galleria di macchina virtuale SQL Server.
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
ms.openlocfilehash: 5babea628180501e959387f80dac55618051f552
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>Come creare macchine virtuali di SQL Server con Azure PowerShell

Questa guida illustra le opzioni per creare macchine virtuali di Windows SQL Server con Azure PowerShell. Per un esempio di Azure PowerShell semplificato con più valori predefiniti, vedere il [Guida introduttiva di PowerShell per Azure VM SQL](quickstart-sql-vm-create-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Configurare la sottoscrizione

1. Aprire PowerShell e stabilire l'accesso al proprio account Azure eseguendo il **Aggiungi AzureRmAccount** comando.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Vedrai una schermata di accesso immettere le proprie credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

## <a name="define-image-variables"></a>Definire le variabili di immagine
Per semplificare le operazioni di creazione di riutilizzo e script, per iniziare, definire una serie di variabili. Cambiare i valori dei parametri in base alla necessità, ma quando si modificano i valori forniti occorre prestare attenzione alle restrizioni di denominazione correlate alle lunghezze dei nomi e ai caratteri speciali.

### <a name="location-and-resource-group"></a>Posizione e gruppo di risorse
Utilizzare due variabili per definire l'area dati e il gruppo di risorse in cui si creano le altre risorse per la macchina virtuale.

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
Utilizzare le seguenti variabili per definire l'immagine di SQL Server da utilizzare per la macchina virtuale.

1. In primo luogo, elencare tutte le offerte di immagine di SQL Server con il **Get AzureRmVMImageOffer** comando:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Per questa esercitazione, usare le variabili seguenti per specificare 2017 di SQL Server in Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Successivamente, elencare le edizioni disponibili per l'offerta.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Per questa esercitazione, usare l'edizione di SQL Server Developer Edition di 2017 (**SQLDEV**). L'edizione Developer è concessa in licenza gratuitamente per test e sviluppo e si paga solo il costo di esecuzione della VM.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Il primo oggetto creato con il modello di distribuzione Resource Manager è il gruppo di risorse. Utilizzare il [New AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet per creare un gruppo di risorse di Azure e le risorse con il nome del gruppo di risorse e il percorso definito dalle variabili che è stato inizializzato in precedenza.

Eseguire il cmdlet seguente per creare un nuovo gruppo di risorse.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
La macchina virtuale richiede risorse di archiviazione per il disco del sistema operativo e per i file di dati e di log di SQL Server. Per semplicità, è creare un singolo disco per entrambi. Successivamente è possibile collegare dischi aggiuntivi usando il cmdlet [Add-Azure Disk](/powershell/module/azure/add-azuredisk) per posizionare i file di dati e di log di SQL Server in dischi dedicati. Utilizzare il [New AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet per creare un account di archiviazione standard del nuovo gruppo di risorse e con il nome account di archiviazione, un nome di Sku archiviazione e un percorso definito utilizzando le variabili che è stato inizializzato in precedenza .

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
Iniziare creando una configurazione di subnet per la rete virtuale. Per questa esercitazione è creare una subnet predefinita utilizzando il [New AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet. Creare la configurazione di subnet della rete virtuale con il prefisso di nome e l'indirizzo subnet definito utilizzando le variabili che è stato inizializzato in precedenza.

> [!NOTE]
> È possibile definire altre proprietà della configurazione della subnet della rete virtuale usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione.

Eseguire il cmdlet seguente per creare la configurazione di subnet virtuale.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Successivamente, creare la rete virtuale utilizzando il [New AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet. Creare la rete virtuale nel nuovo gruppo di risorse, con il nome, percorso e l'indirizzo prefisso definito usando le variabili che è stato inizializzato in precedenza e la configurazione di subnet è definito nel passaggio precedente.

Eseguire il cmdlet seguente per creare la rete virtuale.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico
Dopo avere definito la rete virtuale, è necessario configurare un indirizzo IP per la connettività alla macchina virtuale. Per questa esercitazione, si crea un indirizzo IP pubblico utilizzando l'indirizzo IP dinamico indirizzamento per supportare la connettività Internet. Utilizzare il [New AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) per creare l'indirizzo IP pubblico e di indirizzi nel gruppo di risorse creato in precedenza con il nome, percorso, il metodo di allocazione ed etichetta del nome di dominio DNS definita utilizzando le variabili che si in precedenza è stato inizializzato.

> [!NOTE]
> È possibile definire altre proprietà dell'indirizzo IP pubblico usando questo cmdlet, ma questa operazione non rientra nell'ambito dell'esercitazione. È anche possibile creare un indirizzo privato o un indirizzo di tipo statico, ma anche questa procedura non rientra nell'ambito dell'esercitazione.

Eseguire il cmdlet seguente per creare l'indirizzo IP pubblico.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete
Per proteggere il traffico della macchina virtuale e SQL Server, creare un gruppo di sicurezza di rete.

1. Innanzitutto, creare una regola gruppo di sicurezza di rete per RDP consentire le connessioni desktop remoto.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configurare una regola del gruppo di sicurezza di rete che consente il traffico sulla porta TCP 1433. In questo modo le connessioni a SQL Server su internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Quindi creare il gruppo di sicurezza di rete.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete
È ora possibile creare l'interfaccia di rete che verrà usata dalla macchina virtuale. Viene chiamato il [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet per creare l'interfaccia di rete nel gruppo di risorse creato in precedenza e con il nome, la posizione, la subnet e l'indirizzo IP pubblico definito in precedenza.

Eseguire il cmdlet seguente per creare l'interfaccia di rete.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurare un oggetto VM
Dopo avere definito le risorse di archiviazione e di rete, è possibile definire le risorse di calcolo per la macchina virtuale. Per questa esercitazione è specificare varie proprietà del sistema operativo e dimensioni della macchina virtuale, specificare l'interfaccia di rete che abbiamo creato in precedenza, definire l'archiviazione blob e quindi specificare il disco del sistema operativo.

### <a name="create-the-vm-object"></a>Creare l'oggetto macchina virtuale
Avviare specificando le dimensioni della macchina virtuale. Per questa esercitazione verrà specificata l'opzione DS13. Viene chiamato il [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) per creare un oggetto macchina virtuale può essere configurato con il nome e una dimensione definita utilizzando le variabili che è stato inizializzato in precedenza.

Eseguire il cmdlet seguente per creare l'oggetto macchina virtuale.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creare un oggetto credenziali per includere il nome e la password per le credenziali di amministratore locale
Prima di configurare le proprietà del sistema operativo per la macchina virtuale, è necessario specificare le credenziali per l'account di amministratore locale sotto forma di stringa sicura. A tale scopo, utilizzare il [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

Eseguire il cmdlet seguente e, nella finestra di richiesta delle credenziali di PowerShell, digitare il nome e una password da utilizzare per l'account administrator locale nella macchina virtuale.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Configurare le proprietà del sistema operativo per la macchina virtuale
Ora è possibile impostare le proprietà del sistema operativo della macchina virtuale con [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet per impostare il tipo del sistema operativo di Windows, richiedono il [agente della macchina virtuale](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) a tal fine, specificare che il cmdlet Abilita aggiornamento automatico e impostare il nome della macchina virtuale, il nome del computer e le credenziali usando le variabili che è stato inizializzato in precedenza.

Eseguire il cmdlet seguente per configurare le proprietà del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Aggiungere l'interfaccia di rete alla macchina virtuale
È aggiungere l'interfaccia di rete creata in precedenza per la macchina virtuale. Chiamare il [Aggiungi AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet per aggiungere l'interfaccia di rete utilizzando la variabile di interfaccia di rete definita in precedenza.

Eseguire il cmdlet seguente per configurare l'interfaccia di rete per la macchina virtuale.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Impostare la posizione dell'archivio BLOB per il disco da usare per la macchina virtuale
Quindi, impostare il percorso di archiviazione blob per il disco da utilizzare per la macchina virtuale utilizzando le variabili definite in precedenza.

Eseguire il cmdlet seguente per impostare la posizione dell'archivio BLOB.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Configurare le proprietà del disco del sistema operativo per la macchina virtuale
Quindi, impostare il sistema operativo proprietà del disco per la macchina virtuale. Utilizzare il [Set AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet per specificare che il sistema operativo per la macchina virtuale verrà fornito da un'immagine, impostare la memorizzazione nella cache per sola lettura (perché SQL Server viene installato sul disco stesso) e definire la macchina virtuale nome e il disco del sistema operativo definita utilizzando le variabili definita in precedenza.

Eseguire il cmdlet seguente per configurare le proprietà del disco del sistema operativo per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Specificare l'immagine di piattaforma per la macchina virtuale
L'ultimo passaggio della configurazione consiste nello specificare l'immagine di piattaforma per la macchina virtuale. Per l'esercitazione viene usata l'immagine più recente per SQL Server 2016 CTP. Utilizzare il [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet per utilizzare questa immagine, come definito dalle variabili definite in precedenza.

Eseguire il cmdlet seguente per specificare l'immagine di piattaforma per la macchina virtuale.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Creare la macchina virtuale SQL
Al termine della procedura di configurazione, è possibile creare la macchina virtuale. Utilizzare il [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet per creare la macchina virtuale utilizzando le variabili che sono state definite.

Eseguire il cmdlet seguente per creare la macchina virtuale.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La macchina virtuale viene creata.

> [!NOTE]
> È possibile ignorare l'errore sulla diagnostica bot. Viene creato un account di archiviazione standard per la diagnostica di avvio, perché l'account di archiviazione specificato per il disco della macchina virtuale è un account di archiviazione premium.

## <a name="install-the-sql-iaas-agent"></a>Installare l'agente Iaas SQL
Macchine virtuali di SQL Server supporta le funzionalità di gestione automatica con il [estensione di SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Per installare l'agente nella nuova macchina virtuale, eseguire il comando seguente dopo averlo creato.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Rimuovere una macchina virtuale di test

Se non è necessaria la macchina virtuale in esecuzione continuamente, per evitare costi non necessari, arrestata quando non è in uso. Il comando seguente arresta la macchina virtuale, ma lascia disponibile per un utilizzo futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

È inoltre possibile eliminare definitivamente tutte le risorse associate alla macchina virtuale con il **Remove AzureRmResourceGroup** comando. In questo modo viene eliminata definitivamente anche la macchina virtuale, quindi usare questo comando con cautela.

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
Dopo aver creata la macchina virtuale, è possibile:

- Connettersi alla macchina virtuale tramite desktop remoto (RDP).
- Configurare le impostazioni di SQL Server nel portale per la macchina virtuale, tra cui:
   - [Impostazioni di archiviazione](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Attività di gestione automatica](virtual-machines-windows-sql-server-agent-extension.md)
- [Configurare la connettività](virtual-machines-windows-sql-connect.md).
- Connettere i client e applicazioni per la nuova istanza di SQL Server.

