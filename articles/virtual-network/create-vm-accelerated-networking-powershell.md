---
title: Creare una macchina virtuale di Azure con rete accelerata | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Windows con rete accelerata.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: f8f4f55f2c2aa4a0f9cce08e10c9f12f81a54dba
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678002"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Creare una macchina virtuale Windows con rete accelerata

Questa esercitazione illustra come creare una macchina virtuale (VM) Windows con rete accelerata. Per creare una macchina virtuale Linux con la funzionalità Rete accelerata, vedere [Creare una macchina virtuale Linux con Rete accelerata](create-vm-accelerated-networking-cli.md). La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate esclude l'host dal percorso dati, riducendo così la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più impegnativi nei tipi di VM supportati. L'immagine seguente illustra le comunicazioni tra due VM, con e senza rete accelerata:

![Confronto](./media/create-vm-accelerated-networking/accelerated-networking.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per ulteriori informazioni sui commutatori virtuali, vedere [virtualizzazione rete Hyper-V e commutatore virtuale](https://technet.microsoft.com/library/jj945275.aspx).

Con rete accelerata, il traffico di rete raggiunge la scheda di interfaccia di rete della VM e quindi viene inoltrato alla VM. Il carico di tutti i criteri di rete del commutatore virtuale viene ora ripartito e applicato all'hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità rete accelerata si applicano solo alla VM in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due VM connesse alla stessa Rete virtuale di Azure. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa/più pacchetti al secondo (pps):** Rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** L'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** Ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="limitations-and-constraints"></a>Limiti e vincoli

### <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le distribuzioni seguenti sono supportate in modo nativo dalla raccolta di Azure:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Istanze di VM supportate
La funzionalità Rete accelerata è supportata nella maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con 2 o più vCPU.  Queste serie supportate sono D/DSv2 e F/Fs

Nelle istanze che supportano l'hyperthreading, la Rete accelerata è supportata nelle istanze di macchine virtuali con 4 o più vCPU. Le serie supportate sono D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS e MS/Mmsv2.

Per altre informazioni sulle istanze di VM, vedere [Dimensioni per le macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regions
Questa funzionalità è disponibile in tutte le aree di Azure pubbliche e nel cloud di Azure per enti pubblici.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Abilitazione della Rete accelerata in una macchina virtuale in esecuzione
In una dimensione della macchina virtuale supportata in cui la Rete accelerata non è abilitata, è possibile abilitare la funzionalità solo quando la macchina virtuale è arrestata e deallocata.

### <a name="deployment-through-azure-resource-manager"></a>Distribuzione tramite Azure Resource Manager
Le macchine virtuali (classiche) non possono essere distribuite con la funzionalità Rete accelerata.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Creare una macchina virtuale Windows con Rete accelerata
## <a name="portal-creation"></a>Creazione nel portale
Questo articolo illustra i passaggi per creare una macchina virtuale con rete accelerata tramite Azure PowerShell, ma è anche possibile [creare una macchina virtuale con rete accelerata usando il portale di Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando si crea una macchina virtuale nel portale, nel pannello **creare una macchina virtuale** scegliere la scheda **rete** .  In questa scheda è disponibile un'opzione per la **rete accelerata**.  Se è stato scelto un [sistema operativo supportato](#supported-operating-systems) e le [dimensioni della macchina virtuale](#supported-vm-instances), questa opzione verrà popolata automaticamente in "on".  In caso contrario, viene popolata l'opzione "off" per la rete accelerata e viene fornito all'utente un motivo per cui non è abilitata.   
* *Nota:* Solo i sistemi operativi supportati possono essere abilitati tramite il portale.  Se si usa un'immagine personalizzata e l'immagine supporta la rete accelerata, creare la VM usando l'interfaccia della riga di comando o PowerShell. 

Dopo aver creato la macchina virtuale, è possibile verificare che la rete accelerata sia abilitata seguendo le istruzioni riportate nella pagina verificare che la rete accelerata sia abilitata.

## <a name="powershell-creation"></a>Creazione di PowerShell
## <a name="create-a-virtual-network"></a>Crea rete virtuale

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installare [Azure PowerShell](/powershell/azure/install-az-ps) versione 1.0.0 o successiva. Per trovare la versione attualmente installata, eseguire `Get-Module -ListAvailable Az`. Se è necessario installare o aggiornare, installare la versione più recente del modulo AZ dal [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). In una sessione di PowerShell accedere a un account Azure usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNic* e *myVM*.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *centralus*.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Per prima cosa, creare una configurazione di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). L'esempio seguente crea una subnet denominata *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)con la subnet di *Subnet* .

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per prima cosa, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
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

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e assegnare alla regola di sicurezza *Allow-RDP-all* . Oltre alla regola *Allow-RDP-All*, il gruppo di sicurezza di rete contiene diverse regole predefinite. Una regola predefinita disabilita tutti gli accessi in ingresso da Internet ed è per questo motivo che la regola *Allow-RDP-All* viene assegnata al gruppo di sicurezza di rete, consentendo così di connettersi in remoto alla macchina virtuale dopo averla creata.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associare il gruppo di sicurezza di rete alla subnet di *subnet* con [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). La regola nel gruppo di sicurezza di rete viene applicata a tutte le risorse distribuite nella subnet.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Creare un'interfaccia di rete con rete accelerata
Creare un indirizzo IP pubblico con [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Un indirizzo IP pubblico non è necessario se non si prevede di accedere alla macchina virtuale da Internet, ma è indispensabile per completare i passaggi in questo articolo.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) con rete accelerata abilitata e assegnare l'indirizzo IP pubblico all'interfaccia di rete. L'esempio seguente crea un'interfaccia di rete denominata *myNic* nella subnet *mySubnet* della rete virtuale *myVnet* e assegna l'indirizzo IP pubblico *myPublicIp* a tale interfaccia:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Impostare le credenziali della VM sulla variabile `$cred` usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Definire prima di tutto la VM con [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). L'esempio seguente definisce una VM denominata *myVM* con una dimensione che supporta la funzionalità Rete accelerata (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Per un elenco di tutte le dimensioni e le caratteristiche delle VM, vedere [Dimensioni per le macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Creare la parte restante della configurazione della macchina virtuale con [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). L'esempio seguente crea una VM Windows Server 2016:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Collegare l'interfaccia di rete creata in precedenza con [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Infine, creare la VM con [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Verificare che il driver sia installato nel sistema operativo

Dopo aver creato la VM in Azure, connettersi alla VM e verificare che il driver sia installato in Windows.

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure e accedere con l'account di Azure.
2. Nella casella che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *myVm*. Fare clic su **myVm** quando viene visualizzato nei risultati della ricerca. Se **Creazione in corso** è visibile sotto il pulsante **Connetti**, Azure non ha ancora terminato la creazione della VM. Fare clic su **Connetti** nell'angolo in alto a sinistra della finestra di panoramica solo dopo che **Creazione in corso** non è più visibile sotto il pulsante **Connetti**.
3. Immettere il nome utente e la password specificati nella procedura [Creare la macchina virtuale](#create-the-virtual-machine). Se non si è mai stabilita una connessione a una VM Windows in Azure, vedere [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Fare clic con il pulsante destro del mouse sul pulsante Start di Windows e scegliere **Gestione dispositivi**. Espandere il nodo **Schede di rete**. Verificare che sia visualizzata la voce **Scheda Ethernet VF Mellanox ConnectX-3**, come illustra la figura seguente:

    ![Gestione dispositivi](./media/create-vm-accelerated-networking/device-manager.png)

La funzionalità di rete accelerata è ora abilitata per la VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Abilitare la funzione Rete accelerata nelle macchine virtuali esistenti
Se si è creata una macchina virtuale senza Rete accelerata, è possibile abilitare questa funzionalità in una macchina virtuale esistente.  Per il supporto di Rete accelerata, la macchina virtuale deve soddisfare i prerequisiti seguenti, descritti anche in precedenza:

* La macchina virtuale deve essere di dimensioni supportate da Rete accelerata
* La macchina virtuale deve essere un'immagine della raccolta di Azure supportata (e con versione del kernel per Linux)
* Tutte le macchine virtuali in un set di disponibilità o set di scalabilità di macchine virtuali (VMSS) devono essere arrestate/deallocate prima di abilitare Rete accelerata su qualsiasi scheda di rete

### <a name="individual-vms--vms-in-an-availability-set"></a>Macchine virtuali singole e in un set di disponibilità
Prima di tutto, arrestare/deallocare la macchina virtuale oppure, nel caso di un set di disponibilità, tutte le macchine virtuali nel set:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Importante: se la macchina virtuale è stata creata singolarmente, senza un set di disponibilità, per abilitare Rete accelerata è sufficiente arrestare/deallocare la singola macchina virtuale.  Se la macchina virtuale è stata creata con un set di disponibilità, sarà necessario arrestare/deallocare tutte le macchine virtuali incluse nel set di disponibilità prima di abilitare la funzione Rete accelerata in una qualsiasi delle schede di interfaccia di rete. 

Dopo l'arresto, abilitare Rete accelerata nella scheda di rete della macchina virtuale:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Riavviare la macchina virtuale o, se si trova in un set di disponibilità, tutte le macchine virtuali del set e verificare che sia abilitata la funzionalità rete accelerata:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
Il caso dei set di scalabilità di macchine virtuali è leggermente diverso, ma segue lo stesso flusso di lavoro.  In primo luogo, arrestare le macchine virtuali:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Dopo l'arresto delle macchine virtuali, aggiornare la funzione Rete accelerata nell'interfaccia di rete:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Si noti che, all'interno di un set di scalabilità di macchine virtuali, gli aggiornamenti delle macchine virtuali vengono applicati in base a tre diverse impostazioni, ossia automaticamente, in sequenza e manualmente.  In queste istruzioni i criteri sono impostati sull'aggiornamento automatico, in modo che il set di scalabilità di macchine virtuali rilevi le modifiche immediatamente dopo il riavvio.  Per impostare l'aggiornamento automatico in modo che le modifiche vengano rilevate immediatamente:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Infine, riavviare il set di scalabilità di macchine virtuali:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Dopo il riavvio, attendere che gli aggiornamenti vengano completati. Al termine, la VF verrà visualizzata all'interno della macchina virtuale  (assicurarsi di usare una dimensione di macchina virtuale e un sistema operativo supportati).

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ridimensionamento di macchine virtuali esistenti con Rete accelerata

Le macchine virtuali con la funzione Rete accelerata abilitata possono essere ridimensionate solo nelle macchine virtuali che supportano questa funzionalità.  

Non è possibile ridimensionare una macchina virtuale con Rete accelerata abilitata in un'istanza di macchina virtuale che non supporta questa funzionalità tramite l'operazione di ridimensionamento.  Per ridimensionare una macchina virtuale di questo tipo:

* Arrestare/deallocare la macchina virtuale oppure, nel caso di un set di disponibilità/set di scalabilità di macchine virtuali, tutte le macchine virtuali nel set/set di scalabilità di macchine virtuali.
* La funzionalità Rete accelerata deve essere disabilitata nella scheda di rete della macchina virtuale o, se all'interno di un set di disponibilità/set di scalabilità di macchine virtuali, in tutte le macchine virtuali nel set/set di scalabilità di macchine virtuali.
* Dopo aver disabilitato Rete accelerata, è possibile convertire la macchina virtuale/il set di disponibilità/il set di scalabilità di macchine virtuali in una nuova dimensione che non supporta Rete accelerata e riavviare.
