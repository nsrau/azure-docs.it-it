---
title: Filtrare il traffico di rete - Azure PowerShell | Microsoft Docs
description: In questo articolo viene illustrato come filtrare il traffico di rete a una subnet, con un gruppo di sicurezza di rete, usando PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: dfeff34de882602711ed375d81977ae501ec51cf
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428398"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrare il traffico di rete con un gruppo di sicurezza di rete usando PowerShell

È possibile filtrare il traffico di rete in ingresso e in uscita in una subnet di rete virtuale con un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono regole di sicurezza per filtrare il traffico di rete in base a indirizzo IP, porta e protocollo. Le regole di sicurezza vengono applicate alle risorse distribuite in una subnet. In questo articolo viene spiegato come:

* Creare un gruppo di sicurezza di rete e le regole di sicurezza
* Creare una rete virtuale e associare un gruppo di sicurezza di rete a una subnet
* Distribuire le macchine virtuali in una subnet
* Testare i filtri del traffico

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 6.2.1 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Un gruppo di sicurezza di rete contiene regole di sicurezza. Le regole di sicurezza specificano un'origine e una destinazione. Le origini e le destinazioni possono essere gruppi di sicurezza delle applicazioni.

### <a name="create-application-security-groups"></a>Creare gruppi di sicurezza dell'applicazione

Creare prima di tutto un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) per tutte le risorse create in questo articolo. L'esempio seguente crea un gruppo di risorse denominato nella località *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare un gruppo di sicurezza dell'applicazione con [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Un gruppo di sicurezza dell'applicazione consente di raggruppare i server con requisiti di filtro delle porte simili. L'esempio seguente crea due gruppi di sicurezza dell'applicazione.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Creare regole di sicurezza

Creare una regola di sicurezza con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). L'esempio seguente crea una regola che consenta il traffico in ingresso proveniente da Internet per il gruppo di sicurezza dell'applicazione *myWebServers* sulle porte 80 e 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

In questo articolo RDP (porta 3389) è esposto a Internet per la VM *myAsgMgmtServers*. Per gli ambienti di produzione, anziché esporre la porta 3389 a Internet, è consigliabile connettersi alle risorse di Azure da gestire con una connessione di rete [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [privata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Creare una configurazione di una subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). L'esempio seguente aggiunge una subnet denominata *mySubnet* alla rete virtuale e vi associa il gruppo di sicurezza di rete *myNsg*:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Prima di creare le macchine virtuali, recuperare l'oggetto rete virtuale con la subnet con [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Creare un indirizzo IP pubblico per ogni VM con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Creare due interfacce di rete con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) e assegnare un indirizzo IP pubblico all'interfaccia di rete. L'esempio seguente crea un'interfaccia di rete, vi associa l'indirizzo IP pubblico *myVmWeb* e lo rende un membro del gruppo di sicurezza dell'applicazione *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

L'esempio seguente crea un'interfaccia di rete, vi associa l'indirizzo IP pubblico *myVmMgmt* e lo rende un membro del gruppo di sicurezza dell'applicazione *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Creare due VM nella rete virtuale allo scopo di convalidare il filtro del traffico in un passaggio successivo. 

Creare una configurazione di VM con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), quindi creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una VM che funge da server Web. L'opzione `-AsJob` crea la macchina virtuale in background, quindi è possibile continuare con il passaggio successivo: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Creare una VM che funge da server di gestione:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

La creazione della macchina virtuale richiede alcuni minuti. Prima di continuare con i passaggi successivi, attendere che Azure finisca di creare la VM.

## <a name="test-traffic-filters"></a>Testare i filtri del traffico

Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una VM. L'esempio seguente restituisce l'indirizzo IP pubblico della VM *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Usare il comando seguente per creare una sessione di Desktop remoto con la macchina virtuale *myVmMgmt* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Aprire il file RDP scaricato. Quando richiesto, selezionare **Connetti**.

Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale), quindi scegliere **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione. 
   
La connessione ha esito positivo, perché nella porta 3389 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza dell'applicazione *myAsgMgmtServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmMgmt*.

Usare il comando seguente per creare una connessione Desktop remoto alla VM *myVmWeb*, dalla VM *myVmMgmt*, con il comando seguente, da PowerShell:

``` 
mstsc /v:myvmWeb
```

La connessione ha esito positivo perché una regola di sicurezza predefinita in ciascun gruppo di sicurezza di rete consente il traffico su tutte le porte tra tutti gli indirizzi IP all'interno di una rete virtuale. Non è possibile creare una connessione Desktop remoto alla VM *myVmWeb* da Internet perché la regola di sicurezza per il gruppo *myAsgWebServers* non consente il traffico in ingresso da Internet sulla porta 3389.

Usare il comando seguente per installare Microsoft IIS nella VM *myVmWeb* da PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine dell'installazione di IIS, disconnettersi dalla VM *myVmWeb*, rimanendo nella connessione Desktop remoto della VM *myVmMgmt*. Per visualizzare la schermata iniziale di IIS, aprire un browser Internet e passare a http://myVmWeb.

Disconnettersi dalla VM *myVmMgmt*.

Nel computer immettere il comando seguente da PowerShell per recuperare l'indirizzo IP pubblico del server *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Per verificare che sia possibile accedere al server Web *myVmWeb* dall'esterno di Azure, aprire un browser Internet nel computer e passare a `http://<public-ip-address-from-previous-step>`. La connessione ha esito positivo, perché nella porta 80 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza dell'applicazione *myAsgWebServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmWeb*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un gruppo di sicurezza di rete, che è stato associato a una subnet di rete virtuale. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md) e [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md).

Per impostazione predefinita, Azure instrada il traffico tra subnet. È anche possibile ad esempio scegliere di instradare il traffico tra subnet tramite una VM che funge da firewall. Per altre informazioni, vedere [Creare una tabella di route](tutorial-create-route-table-powershell.md).
