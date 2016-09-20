<properties
   pageTitle="Aprire porte a una VM di Azure tramite PowerShell | Microsoft Azure"
   description="Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Azure Resource Manager e Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# Apertura di porte a una VM tramite PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole del controllo di accesso, è necessario che [sia installata la versione più recente di Azure PowerShell](../powershell-install-configure.md). È possibile anche [eseguire questi passaggi tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md).

È prima di tutto necessario creare una regola per consentire il traffico HTTP sulla porta TCP 80, immettendo il proprio nome e una descrizione:

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Creare quindi il gruppo di sicurezza di rete e assegnare la regola HTTP appena creata come indicato di seguito, immettendo il nome del gruppo di risorse e il percorso:

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

Ora si assegnerà il gruppo di sicurezza di rete a una subnet. Selezionare prima di tutto la rete virtuale:

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

Associare il gruppo di sicurezza di rete alla subnet:

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

Infine, aggiornare la rete virtuale affinché le modifiche abbiano effetto:

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

- [Panoramica di Gestione risorse di Azure](../resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->