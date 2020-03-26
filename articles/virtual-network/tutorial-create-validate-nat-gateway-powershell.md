---
title: 'Esercitazione: Creare e testare un gateway NAT - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Questa esercitazione illustra come creare un gateway NAT e testare il servizio NAT tramite Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202231"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Esercitazione: Creare un gateway NAT e testare il servizio NAT tramite Azure PowerShell

In questa esercitazione verrà creato un gateway NAT per fornire connettività in uscita per le macchine virtuali in Azure. Per testare il gateway NAT, distribuire una macchina virtuale di origine e di destinazione. Il gateway NAT verrà testato tramite connessioni in uscita a un indirizzo IP pubblico. Queste connessioni verranno stabilite dall'origine alla macchina virtuale di destinazione. Questa esercitazione distribuisce la macchina virtuale di origine e quella di destinazione in due reti virtuali diverse nello stesso gruppo di risorse per maggiore semplicità.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa esercitazione, è possibile usare Azure Cloud Shell o eseguire i rispettivi comandi localmente.  Se è la prima volta che si usa Azure Cloud Shell, è necessario [accedere ora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroupNAT** nella località **Stati Uniti orientali 2**:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere a Internet, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPsource** in **myResourceGroupNAT**. Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIPsource** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Creare un prefisso indirizzo IP pubblico

 Usare il comando [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) per creare una risorsa prefisso indirizzo IP pubblico denominata **myPublicIPprefixsource** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIPPrefixsource** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Creare una risorsa gateway NAT

Questa sezione descrive in modo dettagliato come creare e configurare i componenti seguenti del servizio NAT tramite la risorsa gateway NAT:
  - Un pool di indirizzi IP pubblici e un prefisso indirizzo IP pubblico da usare per i flussi in uscita convertiti dalla risorsa gateway NAT.
  - Modificare il timeout di inattività dal valore predefinito di 4 minuti a 10 minuti.

Creare un gateway NAT di Azure globale con [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Il risultato di questo comando creerà una risorsa gateway denominata **myNATgateway** che usa l'indirizzo IP pubblico **myPublicIPsource** e il prefisso indirizzo IP pubblico **myPublicIPprefixsource**. Il timeout di inattività è impostato su 10 minuti.  Il risultato di questo comando verrà archiviato in una variabile denominata **$natGateway** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

A questo punto, il gateway NAT è operativo e rimane solo da configurare da quali subnet di rete virtuale dovrà essere usato.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparare l'origine per il traffico in uscita

Verrà fornita la procedura dettagliata per la configurazione di un ambiente di test completo. Verrà configurato un test usando strumenti open source per verificare il gateway NAT. Si inizierà con l'origine, che userà la risorsa gateway NAT creata in precedenza.

### <a name="configure-virtual-network-for-source"></a>Configurare la rete virtuale per l'origine

Creare la rete virtuale e associare la subnet al gateway.

Creare una rete virtuale denominata **myVnetsource** con una subnet denominata **mySubnetsource** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in **myResourceGroupNAT** con [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.  Il risultato dei comandi verrà archiviato nelle variabili denominate **$subnetsource** e **$vnetsource** che verranno usate successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

A questo punto, tutto il traffico in uscita verso le destinazioni Internet usa il servizio NAT.  Non è necessario configurare una route definita dall'utente.

Prima di poter testare il gateway NAT, è necessario creare una macchina virtuale di origine.  Verrà assegnata una risorsa indirizzo IP pubblico come indirizzo IP pubblico a livello di istanza per accedere a questa macchina virtuale dall'esterno. Questo indirizzo viene usato solo per accedervi ai fini del test.  Verrà illustrato il modo in cui il servizio NAT avrà la precedenza rispetto ad altre opzioni in uscita.

Per esercitarsi, è anche possibile creare questa macchina virtuale senza un indirizzo IP pubblico e creare un'altra macchina virtuale da usare come JumpBox senza un indirizzo IP pubblico.

### <a name="create-public-ip-for-source-vm"></a>Creare un indirizzo IP pubblico per la macchina virtuale di origine

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale.  Usare il comando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPVM** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIpsourceVM** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Creare un gruppo di sicurezza di rete ed esporre l'endpoint SSH per la macchina virtuale

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', verrà creato un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH. Il servizio NAT è in grado di riconoscere la direzione del flusso. Questo gruppo di sicurezza di rete non verrà usato per il traffico in uscita se il gateway NAT è configurato nella stessa subnet. Usare il comando [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) per creare una risorsa gruppo di sicurezza di rete denominata **myNSGsource**. Usare il comando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) per creare una regola del gruppo di sicurezza di rete per l'accesso SSH denominata **ssh** in **myResourceGroupNAT**. Il risultato di questo comando verrà archiviato in una variabile denominata **$nsgsource** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale di origine

Creare un'interfaccia di rete denominata [myNicsource](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) con **New-AzNetworkInterface**. Questo comando assocerà l'indirizzo IP pubblico e il gruppo di sicurezza di rete. Il risultato di questo comando verrà archiviato in una variabile denominata **$nicsource** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Creare una macchina virtuale di origine

#### <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se si ha già una coppia di chiavi SSH, ignorare questo passaggio.

Usare ssh-keygen per creare una coppia di chiavi SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Per informazioni più dettagliate su come creare coppie di chiavi SSH, incluso l'uso di PuTTy, vedere [Come usare le chiavi SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Se si crea la coppia di chiavi SSH usando Cloud Shell, la coppia di chiavi viene archiviata in un'immagine del contenitore. Questo [account di archiviazione viene creato automaticamente](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Non eliminare l'account di archiviazione o la condivisione file contenuta al suo interno fino a quando non sono state recuperate le chiavi.

#### <a name="create-vm-configuration"></a>Creare una configurazione di macchina virtuale

Per creare una macchina virtuale in PowerShell, creare una configurazione con impostazioni quali l'immagine da usare, le dimensioni e le opzioni di autenticazione. La configurazione verrà quindi usata durante la creazione della macchina virtuale.

Definire le credenziali SSH, le informazioni sul sistema operativo e le dimensioni della macchina virtuale. In questo esempio la chiave SSH viene archiviata in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combinare le definizioni di configurazione per creare una macchina virtuale denominata **myVMsource** con [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Anche se il comando restituisce immediatamente un risultato, potrebbero essere necessari alcuni minuti per la distribuzione della macchina virtuale.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparare la destinazione per il traffico in uscita

Ora verrà creata una destinazione per il traffico in uscita convertito dal servizio NAT per consentirne il test.

### <a name="configure-virtual-network-for-destination"></a>Configurare la rete virtuale per la destinazione

È necessario creare la rete virtuale in cui risiederà la macchina virtuale di destinazione.  Questi comandi seguono gli stessi passaggi usati per la macchina virtuale di origine. Sono state aggiunte piccole modifiche per esporre l'endpoint di destinazione.

Creare una rete virtuale denominata **myVnetdestination** con una subnet denominata **mySubnetdestination** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in **myResourceGroupNAT** con [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Lo spazio indirizzi IP per la rete virtuale è **192.168.0.0/16**. La subnet all'interno della rete virtuale è **192.168.0.0/24**.  Il risultato dei comandi verrà archiviato nelle variabili denominate **$subnetdestination** e **$vnetdestination** che verranno usate successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Creare l'indirizzo IP pubblico per la macchina virtuale di destinazione

Verrà creato un indirizzo IP pubblico da usare per accedere alla macchina virtuale di destinazione.  Usare il comando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) per creare una risorsa indirizzo IP pubblico denominata **myPublicIPdestinationVM** in **myResourceGroupNAT**.  Il risultato di questo comando verrà archiviato in una variabile denominata **$publicIpdestinationVM** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Creare un gruppo di sicurezza di rete ed esporre l'endpoint SSH e HTTP per la macchina virtuale

Poiché gli indirizzi IP pubblici Standard sono 'sicuri per impostazione predefinita', verrà creato un gruppo di sicurezza di rete per consentire l'accesso in ingresso per SSH. Usare il comando [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) per creare una risorsa gruppo di sicurezza di rete denominata **myNSGdestination**. Usare il comando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) per creare una regola del gruppo di sicurezza di rete per l'accesso SSH denominata **ssh**.  Usare il comando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) per creare una regola del gruppo di sicurezza di rete per l'accesso HTTP denominata **http**. Entrambe le regole verranno create in **myResourceGroupNAT**. Il risultato di questo comando verrà archiviato in una variabile denominata **$nsgdestination** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Creare la scheda di interfaccia di rete per la macchina virtuale di destinazione

Creare un'interfaccia di rete denominata [myNicdestination](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) con **New-AzNetworkInterface**. Questo comando assocerà l'indirizzo IP pubblico e il gruppo di sicurezza di rete. Il risultato di questo comando verrà archiviato in una variabile denominata **$nicdestination** che verrà usata successivamente.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Creare una macchina virtuale di destinazione

#### <a name="create-vm-configuration"></a>Creare una configurazione di macchina virtuale

Per creare una macchina virtuale in PowerShell, creare una configurazione con impostazioni quali l'immagine da usare, le dimensioni e le opzioni di autenticazione. La configurazione verrà quindi usata durante la creazione della macchina virtuale.

Definire le credenziali SSH, le informazioni sul sistema operativo e le dimensioni della macchina virtuale. In questo esempio la chiave SSH viene archiviata in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combinare le definizioni di configurazione per creare una macchina virtuale denominata **myVMdestination** con [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Anche se il comando restituisce immediatamente un risultato, potrebbero essere necessari alcuni minuti per la distribuzione della macchina virtuale.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparare un server Web e testare il payload nella macchina virtuale di destinazione

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di destinazione.  Per ottenere l'indirizzo IP pubblico della macchina virtuale, usare il cmdlet [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di destinazione.

### <a name="sign-in-to-destination-vm"></a>Accedere alla macchina virtuale di destinazione

Le credenziali SSH sono state archiviate in Cloud Shell nel corso dell'operazione precedente.  Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) nel browser. Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale. 

```bash
ssh azureuser@<ip-address-destination>
```

Dopo aver eseguito l'accesso, copiare e incollare i comandi seguenti.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Questi comandi aggiorneranno la macchina virtuale, installeranno nginx e creeranno un file da 100 KB. Questo file verrà recuperato dalla macchina virtuale di origine usando il servizio NAT.

Chiudere la sessione SSH con la macchina virtuale di destinazione.

## <a name="prepare-test-on-source-vm"></a>Preparare il test nella macchina virtuale di origine

È prima di tutto necessario individuare l'indirizzo IP della macchina virtuale di origine.  Per ottenere l'indirizzo IP pubblico della macchina virtuale, usare il cmdlet [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copiare l'indirizzo IP pubblico e quindi incollarlo negli Appunti, in modo da poterlo usare nei passaggi successivi. Indicare che si tratta della macchina virtuale di origine.

### <a name="log-into-source-vm"></a>Accedere alla macchina virtuale di origine

Di nuovo, le credenziali SSH sono archiviate in Cloud Shell. Aprire una nuova scheda per [Azure Cloud Shell](https://shell.azure.com) nel browser.  Usare l'indirizzo IP recuperato nel passaggio precedente per stabilire una sessione SSH con la macchina virtuale. 

```bash
ssh azureuser@<ip-address-source>
```

Copiare e incollare i comandi seguenti per preparare il test del servizio NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Questi comandi aggiorneranno la macchina virtuale, installeranno go, installeranno [hey](https://github.com/rakyll/hey) da GitHub e aggiorneranno l'ambiente della shell.

A questo punto si è pronti per testare il servizio NAT.

## <a name="validate-nat-service"></a>Convalidare il servizio NAT

Mentre si è connessi alla macchina virtuale di origine, è possibile usare **curl** e **hey** per generare richieste all'indirizzo IP di destinazione.

Usare curl per recuperare il file da 100 KB.  Sostituire **\<ip-address-destination>** nell'esempio seguente con l'indirizzo IP di destinazione copiato in precedenza.  Il parametro **--output** indica che il file recuperato verrà rimosso.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

È anche possibile generare una serie di richieste usando **hey**. Di nuovo, sostituire **\<ip-address-destination>** con l'indirizzo IP di destinazione copiato in precedenza.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Questo comando genererà 100 richieste, di cui 10 simultanee, con un timeout di 30 secondi. La connessione TCP non verrà riutilizzata.  Ogni richiesta recupererà 100 KB.  Al termine dell'esecuzione, **hey** segnalerà alcune statistiche relative alle prestazioni del servizio NAT.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) per rimuoverli.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un gateway NAT, sono state create una macchina virtuale di origine e una di destinazione e quindi è stato testato il gateway NAT.

Esaminare le metriche in Monitoraggio di Azure per visualizzare il servizio NAT in funzione. Diagnosticare i problemi, ad esempio l'esaurimento delle risorse delle porte SNAT disponibili.  L'esaurimento delle risorse delle porte SNAT è facilmente risolvibile aggiungendo altre risorse indirizzo IP pubblico, risorse prefisso indirizzo IP pubblico o entrambe.

- Informazioni sul [servizio NAT di rete virtuale](./nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite l'interfaccia della riga di comando di Azure](./quickstart-create-nat-gateway-cli.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Avvio rapido per la distribuzione della [risorsa gateway NAT tramite il portale di Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

