---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito: PowerShell | Documentazione Microsoft'
description: "Passaggi per creare una connessione IPsec dalla rete locale a una rete virtuale di Azure tramite Internet pubblico. Questa procedura consentirà di creare una connessione gateway VPN da sito a sito cross-premise usando PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4605ac9fe40f369d562dfcbf2abe7403f307d2a9
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager

Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico assegnato e non dietro una NAT. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride.

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

Questo articolo illustra la creazione di una rete virtuale e di una connessione gateway VPN da sito a sito alla rete locale con il modello di distribuzione Azure Resource Manager. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride. È anche possibile creare questa configurazione usando strumenti di distribuzione diversi oppure selezionando una diversa opzione nell'elenco seguente per il modello di distribuzione classica:

> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Distribuzione classica - Portale di Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Distribuzione classica - Portale classico](vpn-gateway-site-to-site-create.md)
>
>

#### <a name="additional-configurations"></a>Configurazioni aggiuntive
Se si vogliono connettere più reti virtuali senza creare una connessione a un percorso locale, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md). Per aggiungere una connessione da sito a sito a una rete virtuale che ha già una connessione, vedere [Add a S2S connection to a VNet with an existing VPN gateway connection](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente).


## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

* Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).
* La versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="Login"></a>1. Eseguire la connessione alla sottoscrizione
Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Login-AzureRmAccount
```

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="VNet"></a>2. Creare una rete virtuale e una subnet del gateway
Gli esempi usano una subnet del gateway con dimensioni di /28. Nonostante sia possibile creare una subnet del gateway con dimensioni di /29 soltanto, è consigliabile crearne una più grande che includa più indirizzi selezionando almeno /28 o /27. In questo modo saranno disponibili indirizzi sufficienti per supportare in futuro le possibili configurazioni aggiuntive desiderate.

Se si ha già una rete virtuale con una subnet del gateway con dimensioni di /29 o superiori, è possibile passare direttamente ad [Aggiungere il gateway di rete locale](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Per creare una rete virtuale e una subnet del gateway
Per creare una rete virtuale e una subnet del gateway, usare l'esempio seguente:

Creare prima un gruppo di risorse:

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Successivamente, creare la rete virtuale. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.

L'esempio seguente crea una rete virtuale denominata *testvnet* e due subnet, una denominata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo.

Impostare le variabili.

```powershell
$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
```

Creare la rete virtuale.

```powershell
New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
-Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
```

### <a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale già creata
Questo passaggio è obbligatorio solo se è necessario aggiungere una subnet del gateway a una rete virtuale creata in precedenza.

È possibile creare la subnet del gateway con l'esempio seguente. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegna un nome diverso, la subnet viene creata, ma non verrà trattata da Azure come una subnet del gateway.

Impostare le variabili.

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
```

Creare la subnet del gateway.\

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
```

Impostare la configurazione.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## 3. <a name="localnet"></a>Aggiungere il gateway di rete locale
In una rete virtuale il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure possa usare come riferimento e specificare il prefisso dello spazio dell'indirizzo per il gateway di rete locale.

Azure usa il prefisso di indirizzo IP che viene specificato per identificare il traffico da inviare al percorso locale. Ciò significa che è necessario specificare ogni prefisso di indirizzo da associare al gateway di rete locale. Se la rete locale viene modificata, è possibile aggiornare facilmente questi prefissi.

Quando si usano gli esempi di PowerShell, tenere presente quanto segue:

* Il *GatewayIPAddress* è l'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT.
* *AddressPrefix* corrisponde allo spazio degli indirizzi locale.

Per aggiungere un gateway di rete locale con un solo prefisso di indirizzo:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Per aggiungere un gateway di rete locale con più prefissi di indirizzo:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
```

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Per modificare i prefissi di indirizzo IP per il gateway di rete locale
Talvolta è possibile modificare i prefissi del gateway di rete locale. I passaggi necessari per modificare i prefissi di indirizzo IP dipendono dal fatto che sia stata creata una connessione di gateway VPN. Vedere la sezione [Per modificare i prefissi di indirizzo IP per un gateway di rete locale](#modify) di questo articolo.

## <a name="PublicIP"></a>4. Richiedere un indirizzo IP pubblico del gateway VPN
Successivamente, richiedere un indirizzo IP pubblico da allocare per il gateway VPN di rete virtuale di Azure. Non si tratta dello stesso indirizzo IP assegnato al dispositivo VPN, ma viene assegnato al gateway VPN di Azure stesso. Non è possibile specificare l'indirizzo IP che si vuole usare. Viene allocato in modo dinamico per il gateway. Si usa questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Il gateway VPN di Azure per il modello di distribuzione di Gestione risorse supporta attualmente solo indirizzi IP pubblici usando il metodo di allocazione dinamica. Ciò non significa però che l'indirizzo IP verrà modificato. L'indirizzo IP del gateway VPN di Azure viene modificato solamente quando il gateway viene eliminato e ricreato. L'indirizzo IP pubblico del gateway non cambia in caso di ridimensionamento, reimpostazione o altri aggiornamenti o manutenzioni interne del gateway VPN di Azure.

Usare l'esempio di PowerShell seguente:

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Creare la configurazione di indirizzamento IP gateway
La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Creare il gateway di rete virtuale
In questo passaggio si crea il gateway di rete virtuale. La creazione di un gateway può richiedere molto tempo. Spesso anche oltre 45 minuti.

Usare i valori seguenti:

* Il valore di *-GatewayType* per una configurazione da sito a sito è *Vpn*. Il tipo di gateway è sempre specifico della configurazione che si sta implementando. Ad esempio, altre configurazioni di gateway possono richiedere -GatewayType ExpressRoute.
* Il valore di *-VpnType* può essere *RouteBased*, talvolta definito gateway dinamico nella documentazione, o *PolicyBased*, talvolta definito gateway statico nella documentazione. Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).
* Il valore di *-GatewaySku* può essere *Basic*, *Standard* o *HighPerformance*.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. Configurare il dispositivo VPN
A questo punto, è necessario l'indirizzo IP pubblico del gateway di rete virtuale per la configurazione del dispositivo VPN locale. Utilizzare il produttore del dispositivo per le informazioni di configurazione specifiche. Per altre informazioni, vedere [Dispositivi VPN](vpn-gateway-about-vpn-devices.md).

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, utilizzare l'esempio seguente:

```powershell
Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg
```

## <a name="CreateConnection"></a>8. Creare la connessione VPN
Successivamente, creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori con i propri. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN. Si noti che `-ConnectionType` per la connessione da sito a sito è *IPsec*.

Impostare le variabili.

```powershell
$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
```

Creare la connessione.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

La connessione verrà stabilita in breve tempo.

## <a name="toverify"></a>Per verificare una connessione VPN
Esistono diversi modi per verificare la connessione VPN.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Per modificare i prefissi di indirizzo IP per un gateway di rete locale
Se è necessario modificare i prefissi per il gateway di rete locale, usare le istruzioni seguenti. Sono disponibili due set di istruzioni. La scelta delle istruzioni dipende dal fatto che sia già stata creata o meno la connessione gateway.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Per modificare l'indirizzo IP del gateway per un gateway di rete locale
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi
*  Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).

