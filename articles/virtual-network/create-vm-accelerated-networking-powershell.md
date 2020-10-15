---
title: Creare una VM Windows con rete accelerata-Azure PowerShell
description: Creare una macchina virtuale (VM) Windows con rete accelerata per migliorare significativamente le prestazioni di rete.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: fd50af98fe0d7f20273c45e2b86c18215a3626f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289626"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Creare una VM Windows con rete accelerata usando Azure PowerShell

Questa esercitazione illustra come creare una macchina virtuale (VM) Windows con rete accelerata.

> [!NOTE]
> Per usare la rete accelerata con una macchina virtuale Linux, vedere [creare una VM Linux con rete accelerata](create-vm-accelerated-networking-cli.md).

La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate ignora l'host dal percorso dei dati, riducendo la latenza, l'instabilità e l'utilizzo della CPU per i carichi di lavoro di rete più complessi sui tipi di VM supportati. Il diagramma seguente illustra il modo in cui due VM comunicano con e senza rete accelerata:

![Comunicazione tra macchine virtuali di Azure con e senza rete accelerata](./media/create-vm-accelerated-networking/accelerated-networking.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati.

> [!NOTE]
> Per ulteriori informazioni sui commutatori virtuali, vedere [commutatore virtuale Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Con la rete accelerata, il traffico di rete raggiunge l'interfaccia di rete (NIC) della macchina virtuale e viene quindi inviato alla macchina virtuale. Il carico di tutti i criteri di rete del commutatore virtuale viene ora ripartito e applicato all'hardware. Poiché i criteri vengono applicati nell'hardware, la scheda di interfaccia di rete può inviare il traffico di rete direttamente alla macchina virtuale. La scheda di interfaccia di rete ignora l'host e il Commuter virtuale, mentre gestisce tutti i criteri applicati nell'host.

I vantaggi della rete accelerata si applicano solo alla macchina virtuale in cui è abilitata. Per ottenere risultati ottimali, abilitare questa funzionalità in almeno due macchine virtuali connesse alla stessa rete virtuale di Azure. Per la comunicazione tra reti virtuali o per la connessione locale, questa funzionalità ha un effetto minimo sulla latenza complessiva.

## <a name="benefits"></a>Vantaggi

- **Latenza più bassa/pacchetti superiori al secondo (PPS)**: l'eliminazione del commutatore virtuale dal percorso dati consente di rimuovere la spesa di pacchetti temporali nell'host per l'elaborazione dei criteri. Aumenta anche il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.

- **Jitter ridotto**: l'elaborazione del Commuter virtuale dipende dalla quantità di criteri che devono essere applicati. Dipende anche dal carico di lavoro della CPU che sta eseguendo l'elaborazione. L'offload dell'imposizione dei criteri nell'hardware consente di rimuovere tale variabilità distribuendo i pacchetti direttamente alla macchina virtuale. L'offload consente inoltre di rimuovere la comunicazione da host a macchina virtuale, tutti gli interrupt software e tutti i cambi di contesto.

- **Riduzione dell'utilizzo della CPU**: se si ignora il Commuter virtuale nell'host, si verificherà un minor utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Le distribuzioni seguenti sono supportate direttamente dalla raccolta di Azure:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Limiti e vincoli

### <a name="supported-vm-instances"></a>Istanze di VM supportate

La funzionalità rete accelerata è supportata per la maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con due o più CPU virtuali (vCPU).  Queste serie supportate sono: dv2/DSv2 e F/FS.

Nelle istanze che supportano l'Hyper-Threading, la rete accelerata è supportata nelle istanze di VM con quattro o più vCPU. Le serie supportate sono: D/Dsv3, D/Dsv4, da/Dasv4, E/Esv3, EA/Easv4, Fsv2, Lsv2, MS/MMS e MS/Mmsv2.

Per altre informazioni sulle istanze di VM, vedere [dimensioni per le macchine virtuali Windows in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Immagini personalizzate

Se si usa un'immagine personalizzata e l'immagine supporta la rete accelerata, assicurarsi di avere i driver necessari che funzionano con le schede NIC Mellanox ConnectX-3 e ConnectX-4 LX in Azure.

### <a name="regions"></a>Regioni

La funzionalità rete accelerata è disponibile in tutte le aree globali di Azure e nel cloud di Azure per enti pubblici.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Abilitazione della rete accelerata in una macchina virtuale in esecuzione

Una dimensione di macchina virtuale supportata senza la funzionalità rete accelerata abilitata può essere abilitata solo quando viene arrestata e deallocata.

### <a name="deployment-through-azure-resource-manager"></a>Distribuzione tramite Azure Resource Manager

Le macchine virtuali (classiche) non possono essere distribuite con la rete accelerata.

## <a name="vm-creation-using-the-portal"></a>Creazione di macchine virtuali tramite il portale

Questo articolo illustra la procedura per creare una macchina virtuale con rete accelerata usando Azure PowerShell, ma è anche possibile [usare la portale di Azure per creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) che consente la rete accelerata. Quando si crea una VM nel portale, nella pagina **Crea una macchina virtuale** scegliere la scheda **rete** . Questa scheda include un'opzione per la **rete accelerata**. Se è stato scelto un [sistema operativo supportato](#supported-operating-systems) e le [dimensioni della macchina virtuale](#supported-vm-instances), questa opzione viene impostata automaticamente **su on**. In caso contrario, l'opzione è impostata su **off**e Azure Visualizza il motivo per cui non è possibile abilitarlo.

> [!NOTE]
> Solo i sistemi operativi supportati possono essere abilitati tramite il portale. Se si usa un'immagine personalizzata e l'immagine supporta la rete accelerata, creare la VM usando l'interfaccia della riga di comando o PowerShell. 

Dopo aver creato la macchina virtuale, è possibile verificare se la rete accelerata è abilitata. Seguire queste istruzioni:

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le macchine virtuali. Cercare e selezionare **Macchine virtuali**.

2. Nell'elenco macchina virtuale scegliere la nuova VM.

3. Nella barra dei menu della macchina virtuale scegliere **rete**.

Nelle informazioni sull'interfaccia di rete, accanto all'etichetta **rete accelerata** , il portale Visualizza **disabilitato** o **abilitato** per lo stato della rete accelerata.

## <a name="vm-creation-using-powershell"></a>Creazione di VM con PowerShell

Prima di procedere, installare [Azure PowerShell](/powershell/azure/install-az-ps) versione 1.0.0 o successiva. Per trovare la versione attualmente installata, eseguire `Get-Module -ListAvailable Az`. Se è necessario installare o aggiornare, installare la versione più recente del modulo AZ dal [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). In una sessione di PowerShell accedere a un account Azure usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNic*e *myVM*.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Il comando seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *centralus* :

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Creare una configurazione di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Il comando seguente crea una *subnet denominata subnet*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)con la subnet di *Subnet* .

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. Creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
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

2. Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) e assegnare alla regola di sicurezza *Allow-RDP-all* . A parte la regola *Allow-RDP-all* , il gruppo di sicurezza di rete contiene diverse regole predefinite. Una regola predefinita Disabilita tutti gli accessi in ingresso da Internet. Una volta creata, la regola *Allow-RDP-all* viene assegnata al gruppo di sicurezza di rete in modo che sia possibile connettersi in remoto alla macchina virtuale.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Associare il gruppo di sicurezza di rete alla subnet di *subnet* con [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). La regola nel gruppo di sicurezza di rete viene applicata a tutte le risorse distribuite nella subnet.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Creare un'interfaccia di rete con rete accelerata

1. Creare un indirizzo IP pubblico con [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Un indirizzo IP pubblico non è necessario se non si prevede di accedere alla macchina virtuale da Internet. Tuttavia, è necessario completare i passaggi descritti in questo articolo.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) con rete accelerata abilitata e assegnare l'indirizzo IP pubblico all'interfaccia di rete. L'esempio seguente crea un'interfaccia di rete denominata *myNic* nella subnet di *subnet* della rete virtuale *myVnet* , assegnando all'indirizzo IP pubblico *myPublicIp* :

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Creare una macchina virtuale e collegare l'interfaccia di rete

1. Impostare le credenziali della VM sulla `$cred` variabile usando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), che richiede di eseguire l'accesso:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Definire la macchina virtuale con [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Il comando seguente definisce una VM denominata *myVM* con una dimensione della macchina virtuale che supporta la rete accelerata (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Per un elenco di tutte le dimensioni e le caratteristiche delle VM, vedere [Dimensioni per le macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Creare la parte restante della configurazione della macchina virtuale con [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) e [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Il comando seguente crea una macchina virtuale Windows Server 2016:

    ```azurepowershell
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

4. Collegare l'interfaccia di rete creata in precedenza con [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Creare la macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Verificare che il controller Ethernet sia installato nella macchina virtuale Windows

Dopo aver creato la VM in Azure, connettersi alla macchina virtuale e verificare che il controller Ethernet sia installato in Windows.

1. Passare alla [portale di Azure](https://portal.azure.com) per gestire le macchine virtuali. Cercare e selezionare **Macchine virtuali**.

2. Nell'elenco macchina virtuale scegliere la nuova VM.

3. Nella pagina Panoramica macchina virtuale, se lo **stato** della macchina virtuale è elencato come **creazione**, attendere che Azure finisca di creare la macchina virtuale. Lo **stato** verrà modificato in **in esecuzione** al termine della creazione della macchina virtuale.

4. Dalla barra degli strumenti panoramica macchina virtuale selezionare **Connetti**  >  **RDP**  >  **Scarica file RDP**.

5. Aprire il file con estensione RDP, quindi accedere alla macchina virtuale con le credenziali immesse nella sezione [creare una macchina virtuale e collegare l'interfaccia di rete](#create-a-vm-and-attach-the-network-interface) . Se non si è mai stabilita una connessione a una VM Windows in Azure, vedere [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Quando viene visualizzata la sessione Desktop remoto per la macchina virtuale, fare clic con il pulsante destro del mouse sul pulsante Start di Windows e scegliere **Device Manager**.

7. Nella finestra **Device Manager** espandere il nodo **schede di rete** .

8. Verificare che venga visualizzata la **scheda Ethernet della funzione virtuale Mellanox ConnectX-3** , come illustrato nell'immagine seguente:

    ![Mellanox ConnectX-3 scheda Ethernet della funzione virtuale, nuova scheda di rete per rete accelerata, Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

La rete accelerata è ora abilitata per la macchina virtuale.

> [!NOTE]
> Se non è possibile avviare l'adattatore Mellanox, aprire un prompt di amministratore nella sessione Desktop remoto e immettere il comando seguente:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Abilitare la rete accelerata nelle macchine virtuali esistenti

Se è stata creata una macchina virtuale senza rete accelerata, è possibile abilitare questa funzionalità in una macchina virtuale esistente. La macchina virtuale deve supportare la rete accelerata rispettando i prerequisiti seguenti, descritti in precedenza:

* Le dimensioni della macchina virtuale devono essere supportate per la rete accelerata.
* La macchina virtuale deve essere un'immagine della raccolta di Azure supportata (e la versione del kernel per Linux).
* Tutte le macchine virtuali in un set di disponibilità o in un set di scalabilità di macchine virtuali devono essere interrotte o deallocate prima di abilitare la rete accelerata su qualsiasi NIC.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Singole VM e macchine virtuali in un set di disponibilità

1. Arrestare o deallocare la VM o, se un set di disponibilità, tutte le macchine virtuali nel set:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Quando si crea una macchina virtuale singolarmente, senza un set di disponibilità, è sufficiente arrestare o deallocare la singola macchina virtuale per abilitare la rete accelerata. Se la macchina virtuale è stata creata con un set di disponibilità, è necessario arrestare o deallocare tutte le macchine virtuali contenute nel set di disponibilità prima di abilitare la rete accelerata in una qualsiasi delle schede di rete, in modo che le macchine virtuali si trovino in un cluster che supporta la rete accelerata. Il requisito di arresto o deallocazione non è necessario se si disabilita la rete accelerata, perché i cluster che supportano la rete accelerata funzionano anche con le schede di rete che non usano la rete accelerata.

2. Abilitare la rete accelerata nella scheda di interfaccia di rete della macchina virtuale:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Riavviare la macchina virtuale o, se si trova in un set di disponibilità, tutte le macchine virtuali del set e verificare che sia abilitata la funzionalità rete accelerata:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Set di scalabilità di macchine virtuali

Un set di scalabilità di macchine virtuali è leggermente diverso, ma segue lo stesso flusso di lavoro.

1. Arrestare le macchine virtuali:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Aggiornare la proprietà rete accelerata nell'interfaccia di rete:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Impostare gli aggiornamenti applicati su automatico in modo che le modifiche vengano rilevate immediatamente:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Un set di scalabilità include aggiornamenti di VM che applicano aggiornamenti con tre impostazioni diverse: automatico, in sequenza e manuale. In queste istruzioni il criterio è impostato su automatico, quindi il set di scalabilità preleva le modifiche subito dopo il riavvio.

4. Riavviare il set di scalabilità:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Una volta riavviato, attendere il completamento degli aggiornamenti. Al termine dell'aggiornamento, la funzione virtuale (VF) viene visualizzata all'interno della macchina virtuale. Assicurarsi di usare un sistema operativo supportato e le dimensioni della macchina virtuale.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ridimensionamento di macchine virtuali esistenti con rete accelerata

Se una macchina virtuale ha una rete accelerata abilitata, è possibile solo ridimensionarla in una macchina virtuale che supporta la rete accelerata.  

Una macchina virtuale con rete accelerata abilitata non può essere ridimensionata in un'istanza di macchina virtuale che non supporta la rete accelerata con l'operazione di ridimensionamento. Per ridimensionare una macchina virtuale di questo tipo:

1. Arrestare o deallocare la macchina virtuale. Per un set di disponibilità o un set di scalabilità, arrestare o deallocare tutte le VM nel set di disponibilità o nel set di scalabilità.

2. Disabilitare la rete accelerata nella scheda di interfaccia di rete della macchina virtuale. Per un set di disponibilità o un set di scalabilità, disabilitare la rete accelerata sulle schede di rete di tutte le VM nel set di disponibilità o nel set di scalabilità.

3. Dopo aver disabilitato la rete accelerata, spostare la macchina virtuale, il set di disponibilità o il set di scalabilità in una nuova dimensione che non supporta la rete accelerata e quindi riavviarla.
