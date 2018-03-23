---
title: Creare una macchina virtuale Windows con SQL Server tramite Azure PowerShell | Microsoft Docs
description: Questa esercitazione illustra come creare una macchina virtuale Windows di SQL Server 2017 con Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: daa5043a948e660b6c3e685e933855afff8f7671
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Guida introduttiva: Creare una macchina virtuale Windows con SQL Server tramite Azure PowerShell

Questa guida introduttiva illustra la creazione di una macchina virtuale di SQL Server con Azure PowerShell.

> [!TIP]
> Questa guida introduttiva offre un percorso per effettuare il provisioning e connettersi rapidamente a una macchina virtuale SQL. Per altre informazioni su altre opzioni di Azure PowerShell per la creazione di macchine virtuali SQL, vedere [Guida al provisioning di macchine virtuali SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Ottenere una sottoscrizione di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a id="powershell"></a>Ottenere Azure PowerShell

Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-powershell"></a>Configurare PowerShell

1. Aprire PowerShell e accedere all'account Azure eseguendo il comando **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Viene visualizzata una schermata di accesso per l'immissione delle credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Definire una variabile con un nome univoco per il gruppo di risorse. Per semplificare il resto della guida introduttiva, gli altri comandi usano questo nome come base per i nomi delle altre risorse.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Definire la posizione di un'area di Azure di destinazione per tutte le risorse della macchina virtuale.

   ```PowerShell
   $Location = "East US"
   ```

1. Creare il gruppo di risorse.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Configurare le impostazioni di rete

1. Creare una rete virtuale, una subnet e un indirizzo IP pubblico. Queste risorse vengono usate per fornire la connettività di rete alla macchina virtuale e connetterla a Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Creare un gruppo di sicurezza di rete. Configurare le regole per consentire connessioni RDP (Remote Desktop) e SQL Server.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Creare l'interfaccia di rete.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Creare la macchina virtuale SQL

1. Definire le credenziali per l'accesso alla macchina virtuale. Il nome utente è "azureadmin". Assicurarsi di cambiare la password prima di eseguire il comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Creare un oggetto di configurazione della macchina virtuale e quindi creare la VM. Il comando seguente crea una VM di SQL Server 2017 Developer Edition su Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > La creazione della macchina virtuale richiede alcuni minuti.

## <a name="install-the-sql-iaas-agent"></a>Installare SQL Iaas Agent

Per ottenere l'integrazione del portale e le funzionalità della macchina virtuale SQL, è necessario installare l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). Per installare l'agente sulla nuova VM, eseguire il comando seguente dopo la creazione.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Desktop remoto nella macchina virtuale

1. Usare il comando seguente per recuperare l'indirizzo IP pubblico per la nuova VM.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Accettare quindi l'indirizzo IP restituito e passarlo come parametro della riga di comando a **mstsc** per avviare una sessione di Desktop remoto nella nuova VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando vengono richieste le credenziali, scegliere di immettere le credenziali per un account diverso. Immettere il nome utente preceduto da una barra rovesciata, ad esempio `\azureadmin`, e la password configurata in precedenza in questa guida introduttiva.

## <a name="connect-to-sql-server"></a>Connettersi a SQL Server

1. Dopo l'accesso a una sessione di Desktop remoto, avviare **SQL Server Management Studio 2017** dal menu Start.

1. Nella finestra di dialogo **Connetti al server** mantenere i valori predefiniti. Il nome del server è il nome della VM. L'autenticazione è impostata su **Autenticazione di Windows**. Fare clic su **Connetti**.

Si è ora connessi localmente a SQL Server. Se ci si vuole connettere in modalità remota, è necessario [configurare la connettività](virtual-machines-windows-sql-connect.md) dal portale o manualmente.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non è necessario che la VM sia continuamente in esecuzione, è possibile evitare addebiti superflui arrestandola quando non è in uso. Il comando seguente arresta la VM ma la lascia disponibile per un uso futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

È anche possibile eliminare definitivamente tutte le risorse associate alla macchina virtuale con il comando **Remove-AzureRmResourceGroup**. In questo modo viene eliminata definitivamente anche la macchina virtuale, quindi usare questo comando con cautela.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state illustrate le procedure per creare una macchina virtuale di SQL Server 2017 con Azure PowerShell. Per altre informazioni su come eseguire la migrazione dei dati nella nuova VM di SQL Server, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un database in una VM di SQL Server](virtual-machines-windows-migrate-sql.md)