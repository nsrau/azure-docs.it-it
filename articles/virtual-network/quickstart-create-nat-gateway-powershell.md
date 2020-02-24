---
title: 'Avvio rapido: Creare un gateway NAT - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Questa guida di avvio rapido mostra come creare un gateway NAT usando Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: d2df67479d8b6ab490bf06bda1ec033f9bf7866e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429122"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Avvio rapido: Creare un gateway NAT usando Azure PowerShell

Questa guida di avvio rapido illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

>[!NOTE] 
>Il servizio NAT di rete virtuale di Azure è attualmente disponibile come anteprima pubblica e in un set limitato di [aree](https://azure.microsoft.com/global-infrastructure/regions/). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa esercitazione, è possibile usare Azure Cloud Shell o eseguire i comandi localmente.  Se è la prima volta che si usa Azure Cloud Shell, [accedere ora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Dopo aver abilitato il servizio NAT di rete virtuale di Azure [anteprima](./nat-overview.md#enable-preview) nella sottoscrizione, usare https://aka.ms/natportal per accedere al portale.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroupNAT** nella località **Stati Uniti orientali 2**:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

Le opzioni degli indirizzi IP pubblici per il gateway NAT sono:

* **Indirizzi IP pubblici**
* **Prefissi indirizzi IP pubblici**

Entrambe le opzioni possono essere usate con il gateway NAT.

Per la dimostrazione, verranno aggiunti un indirizzo IP pubblico e un prefisso indirizzo IP pubblico a questo scenario.

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere a Internet, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) per creare una risorsa indirizzo IP pubblico denominata **myPublicIP** in **myResourceGroupNAT**. Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIP** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

Usare il comando [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) per creare una risorsa prefisso indirizzo IP pubblico denominata **myPublicIPprefix** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIPPrefix** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

Questa sezione descrive in modo dettagliato come creare e configurare i componenti seguenti del servizio NAT tramite la risorsa gateway NAT:
  - Un pool di indirizzi IP pubblici e un prefisso indirizzo IP pubblico da usare per i flussi in uscita convertiti dalla risorsa gateway NAT.
  - Modificare il timeout di inattività dal valore predefinito di 4 minuti a 10 minuti.

Creare un gateway NAT di Azure globale con [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Il risultato di questo comando creerà una risorsa gateway denominata **myNATgateway** che usa l'indirizzo IP pubblico **myPublicIP** e il prefisso indirizzo IP pubblico **myPublicIPprefix**. Il timeout di inattività è impostato su 10 minuti.  Il risultato di questo comando verrà archiviato in una variabile denominata **$natGateway** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

A questo punto, il gateway NAT è operativo e rimane solo da configurare da quali subnet di rete virtuale dovrà essere usato.

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Creare la rete virtuale e associare la subnet al gateway.

Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in **myResourceGroup** con [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.  Il risultato dei comandi verrà archiviato nelle variabili denominate **$subnet** e **$vnet** che verranno usate successivamente.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il servizio NAT.  Non è necessario configurare una route definita dall'utente.

## <a name="create-a-vm-to-use-the-nat-service"></a>Creare una macchina virtuale da usare con il servizio NAT

Ora verrà creata una macchina virtuale per l'uso del servizio NAT.  Questa macchina virtuale ha un indirizzo IP pubblico da usare come indirizzo IP pubblico a livello di istanza per consentire l'accesso alla macchina virtuale.  Il servizio NAT è in grado di riconoscere la direzione del flusso e sostituirà la destinazione Internet predefinita nella subnet. L'indirizzo IP pubblico della macchina virtuale non verrà usato per le connessioni in uscita.

### <a name="create-public-ip-for-source-vm"></a>Creare un indirizzo IP pubblico per la macchina virtuale di origine

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale.  Usare il comando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPVM** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIpVM** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Creare un gruppo di sicurezza di rete ed esporre l'endpoint SSH per la macchina virtuale

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', sarà necessario creare un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH. Usare il comando [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) per creare una risorsa gruppo di sicurezza di rete denominata **myNSG**. Usare il comando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) per creare una regola del gruppo di sicurezza di rete per l'accesso SSH denominata **ssh** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$nsg** che verrà usata successivamente.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale

Creare un'interfaccia di rete denominata [myNic](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) con **New-AzNetworkInterface**. Questo comando associa l'indirizzo IP pubblico e il gruppo di sicurezza di rete. Il risultato di questo comando verrà archiviato in una variabile denominata **$nic** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Creare una macchina virtuale

#### <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se si ha già una coppia di chiavi SSH, ignorare questo passaggio.

Usare ssh-keygen per creare una coppia di chiavi SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Per informazioni più dettagliate su come creare coppie di chiavi SSH, incluso l'uso di PuTTy, vedere [Come usare le chiavi SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Se si crea la coppia di chiavi SSH usando Cloud Shell, la coppia di chiavi viene archiviata in un'immagine del contenitore. Questo [account di archiviazione viene creato automaticamente](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Non eliminare l'account di archiviazione o la condivisione file contenuta al suo interno fino a quando non sono state recuperate le chiavi.

#### <a name="create-vm-configuration"></a>Creare una configurazione di macchina virtuale

Per creare una macchina virtuale in PowerShell, creare una configurazione con impostazioni quali l'immagine da usare, le dimensioni e le opzioni di autenticazione. La configurazione viene usata per creare la macchina virtuale.

Definire le credenziali SSH, le informazioni sul sistema operativo e le dimensioni della macchina virtuale. In questo esempio la chiave SSH viene archiviata in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combinare le definizioni di configurazione per creare una macchina virtuale denominata **myVM** con [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Attendere la preparazione della macchina virtuale per la distribuzione, quindi continuare con i passaggi rimanenti.

## <a name="discover-the-ip-address-of-the-vm"></a>Individuare l'indirizzo IP della macchina virtuale

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale che è stata creata. Per ottenere l'indirizzo IP pubblico della macchina virtuale, usare il cmdlet [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare per accedere alla macchina virtuale.

### <a name="sign-in-to-vm"></a>Accedere alla macchina virtuale

Le credenziali SSH sono state archiviate in Cloud Shell nel corso dell'operazione precedente.  Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale.

```bash
ssh azureuser@<ip-address-destination>
```

A questo punto si è pronti per usare il servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) per rimuoverli.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un gateway NAT e una macchina virtuale. 

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.


- Informazioni sul [servizio NAT di rete virtuale di Azure](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).
- [Inviare commenti e suggerimenti sull'anteprima pubblica](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]


