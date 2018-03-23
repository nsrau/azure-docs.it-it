---
title: Creare una rete virtuale di Azure con più subnet - PowerShell | Microsoft Docs
description: Informazioni su come creare una rete virtuale con più subnet usando PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Creare una rete virtuale con più subnet usando PowerShell

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. La creazione di più subnet in una rete virtuale consente di segmentare la rete in modo da filtrare o controllare il flusso del traffico tra le subnet. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare una subnet
> * Testare la comunicazione di rete tra le macchine virtuali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 3.6 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Il prefisso **AddressPrefix** è specificato nella notazione CIDR. Il prefisso dell'indirizzo specificato include gli indirizzi IP 10.0.0.0-10.0.255.254.

## <a name="create-a-subnet"></a>Creare una subnet

Una subnet viene creata creando per prima cosa una configurazione subnet e quindi aggiornando la rete virtuale con la configurazione subnet. Creare una configurazione di subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L'esempio seguente crea due configurazioni subnet per le subnet *Public* e *Private*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

Il prefisso **AddressPrefix** specificato per una subnet deve essere compreso nel prefisso dell'indirizzo definito per la rete virtuale. Il server DHCP di Azure assegna gli indirizzi IP da un prefisso dell'indirizzo della subnet alle risorse distribuite in una subnet. Azure assegna solo gli indirizzi 10.0.0.4-10.0.0.254 alle risorse distribuite all'interno della subnet **Public**, poiché Azure riserva i primi quattro indirizzi (10.0.0.0-10.0.0.3 per la subnet, in questo esempio) e l'ultimo indirizzo (10.0.0.255 per la subnet, in questo esempio) in ogni subnet.

Scrivere le configurazioni subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare le subnet nella rete virtuale:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Prima di distribuire reti virtuali e subnet di Azure per la produzione, è consigliabile acquisire familiarità con le [considerazioni](manage-virtual-network.md#create-a-virtual-network) relative allo spazio degli indirizzi e i [limiti della rete virtuale](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Dopo che le risorse sono state distribuite nelle subnet, alcune modifiche a livello di rete virtuale e di subnet, come quelle degli intervalli di indirizzi, possono richiedere la ridistribuzione delle risorse di Azure esistenti distribuite all'interno delle subnet.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale allo scopo di testare la comunicazione di rete tra di esse e con Internet in un passaggio successivo. 

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVmWeb* nella subnet *Public* della rete virtuale *myVirtualNetwork*. L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo. Quando richiesto, immettere il nome utente e la password con cui si vuole accedere alla macchina virtuale.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

Azure ha assegnato automaticamente 10.0.0.4 come indirizzo IP privato della macchina virtuale, perché 10.0.0.4 è il primo indirizzo IP disponibile nella subnet *Public*. 

Creare una macchina virtuale nella subnet *Private*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

La creazione della macchina virtuale richiede alcuni minuti. Anche se non è visibile nell'output restituito, Azure ha assegnato 10.0.1.4 come indirizzo IP privato della macchina virtuale, perché 10.0.1.4 è il primo indirizzo IP disponibile nella subnet *Private* di *myVirtualNetwork*. 

Non continuare con i passaggi rimanenti fino a quando non viene creata la macchina virtuale e PowerShell non restituisce l'output.

Le macchine virtuali create in questo articolo hanno un'[interfaccia di rete](virtual-network-network-interface.md) con un indirizzo IP assegnato dinamicamente a tale interfaccia. Dopo avere distribuito la macchina virtuale, è possibile [aggiungere più indirizzi IP pubblici e privati o modificare il metodo di assegnazione degli indirizzi IP impostandolo su statico](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile [aggiungere interfacce di rete](virtual-network-network-interface-vm.md#vm-add-nic) fino al limite supportato dalle [dimensioni della macchina virtuale](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) selezionate al momento della creazione della macchina virtuale. È anche possibile [abilitare Single Root I/O Virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) per una macchina virtuale, ma solo quando si crea una macchina virtuale con dimensioni in grado di supportare questa funzionalità.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicare tra macchine virtuali e con Internet

È possibile connettersi all'indirizzo IP pubblico di una macchina virtuale da Internet. Quando in Azure è stata creata la macchina virtuale *myVmMgmt*, anche un indirizzo IP pubblico denominato *myVmMgmt* è stato creato e assegnato alla macchina virtuale. Anche se non è richiesto che a una macchina virtuale sia assegnato un indirizzo IP pubblico, per impostazione predefinita Azure assegna un indirizzo IP pubblico a ogni macchina virtuale creata. Per comunicare da Internet a una macchina virtuale, alla macchina virtuale deve essere assegnato un indirizzo IP pubblico. Tutte le macchine virtuali possono comunicare in uscita con Internet, indipendentemente dal fatto che un indirizzo IP pubblico sia assegnato o meno alla macchina virtuale. Per altre informazioni sulle connessioni Internet in uscita in Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Usare il comando seguente per creare una sessione Desktop remoto con la macchina virtuale *myVmMgmt* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Viene creato, scaricato nel computer e aperto un file Remote Desktop Protocol con estensione rdp. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale) e quindi fare clic su **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione. 

In un passaggio successivo viene usato ping per comunicare con la macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*. Per impostazione predefinita, ping usa il protocollo ICMP, che non viene consentito tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da un prompt dei comandi:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Anche se in questo articolo viene usato ping, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

Per motivi di sicurezza è comune limitare il numero di macchine virtuali a cui è possibile eseguire la connessione in remoto in una rete virtuale. In questa esercitazione viene usata la macchina virtuale *myVmMgmt* per gestire la macchina virtuale *myVmWeb* nella rete virtuale. Usare il comando seguente per eseguire una connessione Desktop remoto alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*:

``` 
mstsc /v:myVmWeb
```

Per comunicare con la macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*, immettere il comando seguente da un prompt dei comandi:

```
ping myvmmgmt
```

L'output è simile all'output di esempio seguente:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Si noti che l'indirizzo della macchina virtuale *myVmMgmt* è 10.0.1.4. 10.0.1.4 è il primo indirizzo IP disponibile nell'intervallo di indirizzi della subnet *Private* in cui è stata distribuita la macchina virtuale *myVmMgmt* in un passaggio precedente.  Il nome di dominio completo della macchina virtuale è *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Anche se la parte *dar5p44cif3ulfq00wxznl3i3f* del nome di dominio è diversa per la macchina virtuale che si sta usando, le parti restanti del nome di dominio sono le stesse. Per impostazione predefinita, tutte le macchine virtuali di Azure usano il servizio DNS di Azure predefinito. Tutte le macchine virtuali all'interno di una rete virtuale possono risolvere i nomi di tutte le altre macchine virtuali nella stessa rete virtuale usando il servizio DNS predefinito di Azure. Invece di usare il servizio DNS predefinito di Azure, è possibile usare il proprio server DNS o la funzionalità di dominio privato del servizio DNS di Azure. Per maggiori dettagli, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso di DNS di Azure per i domini privati](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Per installare Internet Information Services (IIS) per Windows Server nella macchina virtuale *myVmWeb*, immettere il comando seguente da una sessione di PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine dell'installazione di IIS, disconnettere la sessione Desktop remoto *myVmWeb*, rimanendo nella sessione Desktop remoto *myVmMgmt*. Aprire un Web browser e passare a http://myvmweb. Verrà visualizzata la pagina iniziale di IIS.

Disconnettere la sessione Desktop remoto *myVmMgmt*.

Ottenere l'indirizzo pubblico che Azure ha assegnato alla macchina virtuale *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Nel proprio computer selezionare l'indirizzo IP pubblico della macchina virtuale *myVmWeb*. Il tentativo di visualizzare la pagina iniziale di IIS dal proprio computer fallisce. Il tentativo non riesce perché quando le macchine virtuali sono state distribuite, per impostazione predefinita Azure ha creato un gruppo di sicurezza di rete per ogni macchina virtuale. 

Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o impediscono il traffico di rete in ingresso e in uscita in base alla porta e all'indirizzo IP. Il gruppo di sicurezza di rete predefinito creato da Azure consente la comunicazione su tutte le porte tra le risorse nella stessa rete virtuale. Per le macchine virtuali Windows, il gruppo di sicurezza di rete predefinito impedisce tutto il traffico in ingresso da Internet su tutte le porte, fatta eccezione per la porta TCP 3389 (RDP). Di conseguenza, per impostazione predefinita è anche possibile connettersi tramite RDP direttamente alla macchina virtuale *myVmWeb* da Internet, anche se non si vuole che la porta 3389 sia aperta per un server Web. Dal momento che il Web browser comunica sulla porta 80, la comunicazione non riesce da Internet, poiché non esiste alcuna regola nel gruppo di sicurezza di rete predefinito che consenta il traffico sulla porta 80.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come distribuire una rete virtuale con più subnet. È stato inoltre spiegato che quando si crea una macchina virtuale Windows, Azure crea un'interfaccia di rete che viene collegata alla macchina virtuale e crea un gruppo di sicurezza di rete che consente solo il traffico sulla porta 3389 da Internet. Passare all'esercitazione successiva per informazioni su come filtrare il traffico di rete verso le subnet, anziché verso singole macchine virtuali.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete verso le subnet](./virtual-networks-create-nsg-arm-ps.md)
