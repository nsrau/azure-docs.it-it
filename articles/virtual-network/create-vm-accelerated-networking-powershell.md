---
title: Creare una macchina virtuale di Azure con rete accelerata | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux con accelerazione di rete.
services: virtual-network
documentationcenter: 
author: jdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/20/2017
ms.author: jimdial
ms.openlocfilehash: 8c2cc9ef487ee754f904f04e604ef76c3f9e07af
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Creare una macchina virtuale Windows con accelerazione di rete

In questa esercitazione imparare a creare una macchina virtuale di Windows (VM) con accelerazione di rete. La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso ad alte prestazioni consente di ignorare l'host dal relativo al percorso dati, riducendo l'utilizzo della CPU, per l'utilizzo con i carichi di lavoro rete più complesse in tipi di macchine Virtuali supportati, instabilità e latenza. La figura seguente illustra la comunicazione tra due macchine virtuali con e senza la configurazione di rete:

![Confronto](./media/create-vm-accelerated-networking/accelerated-networking.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni sui commutatori virtuali, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con rete accelerata, il traffico di rete raggiunge la scheda di interfaccia di rete della VM e quindi viene inoltrato alla VM. Tutti i criteri di rete che si applica il commutatore virtuale sono ora scaricati e applicati in hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità rete accelerata si applicano solo alla VM in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due VM connesse alla stessa Rete virtuale di Azure. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa e più pacchetti al secondo (pps):** rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** l'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Data Center di Microsoft Windows Server 2012 R2 e Windows Server 2016.

## <a name="supported-vm-instances"></a>Istanze di macchina virtuale supportate
Rete accelerata è supportata in più generale e dimensioni di istanze di calcolo ottimizzata con Vcpu 4 o più. Sulle istanze, ad esempio D/DSv3 o/ESv3 E che supportano l'Hyper-Threading, accelerazione di rete è supportata nelle istanze di macchina virtuale con più di 8 Vcpu. Sono supportate serie: D/DSv2, D/DSv3, E/ESv3, ADFS/F/Fsv2 e Ms/Mms.

Per ulteriori informazioni sulle istanze di macchina virtuale, vedere [dimensioni delle macchine Virtuali di Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regioni
Disponibile in tutte le aree di Azure pubbliche e Cloud di Azure per enti pubblici. 

## <a name="limitations"></a>Limitazioni
L'uso della funzionalità Rete accelerata presenta le limitazioni seguenti:

* **Creazione di un'interfaccia di rete**: la funzionalità rete accelerata può essere abilitata solo per una nuova scheda di interfaccia di rete. Non può essere abilitata per una scheda di interfaccia di rete esistente.
* **Creazione di una VM**: una scheda di interfaccia di rete con rete accelerata abilitata può essere collegata a una VM solo durante la creazione della VM. Non è possibile collegare la scheda di interfaccia di rete a una VM esistente. Se l'aggiunta della macchina virtuale a un gruppo di disponibilità è impostata, tutte le macchine virtuali nel set di disponibilità devono inoltre accelerato networking abilitato.
* **Distribuzione tramite Gestione risorse di Azure:** macchine virtuali (classico) non possono essere distribuiti con accelerazione di rete.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Installare [Azure PowerShell](/powershell/azure/install-azurerm-ps) versione 5.1.1 o versione successiva. Per trovare la versione attualmente installata, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, installare la versione più recente del modulo AzureRM da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM). In una sessione di PowerShell, accedere a un account Azure tramite [Aggiungi AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio inclusi *myResourceGroup*, *myNic*, e *myVM*.

Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nel *centralus* percorso:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Innanzitutto, creare una configurazione di subnet con [New AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). L'esempio seguente crea una subnet denominata *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Creare una rete virtuale con [New AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), con la *mySubnet* subnet.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Innanzitutto, creare una regola gruppo di sicurezza di rete con [New AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Creare un gruppo di sicurezza di rete con [New AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) e assegnare il *RDP-Consenti-All* regola di sicurezza a esso. Oltre al *RDP-Consenti-All* regola, il gruppo di sicurezza di rete contiene regole predefinite di diversi. Una regola predefinita disabilita tutti gli accessi in ingresso da Internet, motivo per cui il *RDP-Consenti-All* regola viene assegnata al gruppo di sicurezza di rete in modo che è possibile connettersi in remoto alla macchina virtuale, dopo averla creata.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associare il gruppo di sicurezza di rete per il *mySubnet* subnet con [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). La regola nel gruppo di sicurezza di rete è efficace per tutte le risorse distribuite nella subnet.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Creare un'interfaccia di rete con rete accelerata
Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Se non si prevede di accedere alla macchina virtuale da Internet, ma per completare i passaggi in questo articolo non è necessario un indirizzo IP pubblico, è necessario.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Creare un'interfaccia di rete con [New AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) con accelerazione networking abilitato e assegnare l'indirizzo IP pubblico per l'interfaccia di rete. L'esempio seguente crea un'interfaccia di rete denominata *myNic* nel *mySubnet* subnet del *myVnet* rete virtuale e assegna il *myPublicIp*  indirizzo IP pubblico a esso:

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Impostare le credenziali di macchina virtuale il `$cred` utilizzando variabili [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Prima di definire la macchina virtuale con [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). L'esempio seguente definisce una macchina virtuale denominata *myVM* con una dimensione di macchina virtuale che supporta l'accelerazione di rete (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Per un elenco di tutte le dimensioni delle macchine Virtuali e di caratteristiche, vedere [dimensioni delle macchine Virtuali di Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Creare il resto della configurazione della VM con [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) e [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). L'esempio seguente crea una VM Windows Server 2016:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Collegare l'interfaccia di rete creato in precedenza con [Aggiungi AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Creare infine la VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confermare che il driver è installato nel sistema operativo

Dopo aver creato la macchina virtuale in Azure, connettersi alla macchina virtuale e verificare che il driver è installato in Windows. 

1. Da un browser Internet, aprire Azure [portale](https://portal.azure.com) e accedere con l'account di Azure.
2. Nella finestra che contiene il testo *individuare risorse* nella parte superiore del portale di Azure, digitare *myVm*. Quando **myVm** viene visualizzato nei risultati della ricerca, fare clic. Se **creazione** visibile quando si seleziona il **Connetti** pulsante, Azure non è ancora terminata la creazione della macchina virtuale. Fare clic su **Connetti** nell'angolo superiore sinistro della panoramica solo dopo avere non più vedere **creazione** sotto il **Connetti** pulsante.
3. Immettere il nome utente e la password immessa in [creare la macchina virtuale](#create-the-virtual-machine). Se non si è mai connessi a una macchina virtuale Windows in Azure, vedere [Connetti alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Fare clic con il pulsante destro del mouse sul pulsante Start di Windows e scegliere **Gestione dispositivi**. Espandere il nodo **Schede di rete**. Verificare che sia visualizzata la voce **Scheda Ethernet VF Mellanox ConnectX-3**, come illustra la figura seguente:
   
    ![Gestione dispositivi](./media/create-vm-accelerated-networking/device-manager.png)

La funzionalità di rete accelerata è ora abilitata per la VM.
