---
title: Aprire porte a una VM tramite Azure PowerShell | Documentazione Microsoft
description: Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Azure Resource Manager e Azure PowerShell
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 744563dc54edd5d38d9cb311d5679d744a0235eb
ms.lasthandoff: 03/31/2017


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Apertura di porte ed endpoint in una VM tramite PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole del controllo di accesso, è necessario che [sia installata la versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs). È possibile anche [eseguire questi passaggi tramite il portale di Azure](nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Accedere all'account di Azure:

```powershell
Login-AzureRmAccount
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `myNetworkSecurityGroup` e `myVnet`.

Creare una regola. L'esempio seguente crea una regola denominata `myNetworkSecurityGroupRule` per consentire il traffico TCP sulla porta 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Successivamente, creare il gruppo di sicurezza di rete e assegnare la regola HTTP appena creata come indicato di seguito. Nell'esempio seguente viene creato un gruppo di sicurezza di rete chiamato `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Ora si assegnerà il gruppo di sicurezza di rete a una subnet. Nell'esempio seguente viene assegnata una rete virtuale esistente chiamata `myVnet` alla variabile `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associare il gruppo di sicurezza di rete alla subnet. Nell'esempio seguente viene associata una subnet chiamata `mySubnet` al gruppo di sicurezza di rete:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Infine, aggiornare la rete virtuale affinché le modifiche abbiano effetto:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
* [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../../load-balancer/load-balancer-arm.md)


