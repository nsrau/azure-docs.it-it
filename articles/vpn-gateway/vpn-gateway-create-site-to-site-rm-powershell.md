---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito: PowerShell'
description: Creare una connessione gateway VPN da sito a sito IPsec dalla rete locale a una rete virtuale di Azure tramite la rete Internet pubblica usando PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7de5f125789b0ed3782224024a9ee9c80d359e77
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659370"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager

Questo articolo illustra come usare PowerShell per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Valori di esempio

Gli esempi di questo articolo usano i valori seguenti. È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. creare una rete virtuale e una subnet del gateway

Se non si ha una rete virtuale, crearne una. Quando si crea una rete virtuale, verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale. 

>[!NOTE]
>Per consentire a questa rete virtuale di connettersi a una posizione locale, è necessario coordinarsi con l'amministratore di rete locale per definire un intervallo di indirizzi IP da usare in modo specifico per questa rete virtuale. Se su entrambi i lati della connessione VPN è presente un intervallo di indirizzi duplicato, il traffico non viene indirizzato nel modo previsto. Se inoltre si vuole connettere questa rete virtuale a un'altra rete virtuale, lo spazio degli indirizzi non può sovrapporsi a un'altra rete virtuale. Pianificare quindi con attenzione la configurazione della rete.
>
>

### <a name="about-the-gateway-subnet"></a>Informazioni sulla subnet del gateway

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>Creare una rete virtuale e una subnet del gateway

Questo esempio crea una rete virtuale e una subnet del gateway. Se esiste già una rete virtuale a cui è necessario aggiungere una subnet del gateway, vedere [Per aggiungere una subnet del gateway a una rete virtuale già creata](#gatewaysubnet).

Creare un gruppo di risorse:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Creare la rete virtuale.

1. Impostare le variabili.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Creare la rete virtuale.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale già creata

Usare la procedura illustrata in questa sezione se è già disponibile una rete virtuale, ma è necessario aggiungere una subnet del gateway.

1. Impostare le variabili.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Creare la subnet del gateway.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Impostare la configurazione.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a> creare il gateway di rete locale

Il gateway di rete locale in genere fa riferimento al percorso locale. Non si tratta di un gateway di rete virtuale. Assegnare al sito un nome che Azure possa usare come riferimento, quindi specificare l'indirizzo IP del dispositivo VPN locale con cui si creerà una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata, è possibile aggiornare facilmente i prefissi.

Usare i valori seguenti:

* Il *GatewayIPAddress* è l'indirizzo IP del dispositivo VPN locale.
* *AddressPrefix* corrisponde allo spazio degli indirizzi locale.

Per aggiungere un gateway di rete locale con un solo prefisso di indirizzo:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Per aggiungere un gateway di rete locale con più prefissi di indirizzo:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Per modificare i prefissi di indirizzo IP per il gateway di rete locale:

Talvolta è possibile modificare i prefissi del gateway di rete locale. I passaggi necessari per modificare i prefissi di indirizzo IP dipendono dal fatto che sia stata creata una connessione di gateway VPN. Vedere la sezione [Per modificare i prefissi di indirizzo IP per un gateway di rete locale](#modify) di questo articolo.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. richiedere un indirizzo IP pubblico

Un gateway VPN deve avere un indirizzo IP pubblico. È necessario richiedere prima di tutto la risorsa dell'indirizzo IP e quindi farvi riferimento durante la creazione del gateway di rete virtuale. L'indirizzo IP viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. 

Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Non è possibile richiedere un'assegnazione degli indirizzi IP pubblici statici. Ciò non significa tuttavia che l'indirizzo IP verrà modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

Richiedere un indirizzo IP pubblico che verrà assegnato per il gateway VPN di rete virtuale.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. creare la configurazione di indirizzamento IP del gateway

La configurazione del gateway definisce la subnet ('GatewaySubnet') e l'indirizzo IP pubblico da usare. Per creare la configurazione del gateway, usare l'esempio seguente:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. creare il gateway VPN

Creare il gateway VPN di rete virtuale.

Usare i valori seguenti:

* Il valore di *-GatewayType* per una configurazione da sito a sito è *Vpn*. Il tipo di gateway è sempre specifico della configurazione che si sta implementando. Ad esempio, altre configurazioni di gateway possono richiedere -GatewayType ExpressRoute.
* Il valore di *-VpnType* può essere *RouteBased* (denominato gateway dinamico in alcune documentazioni) o *PolicyBased* (denominato gateway statico in alcune documentazioni). Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).
* Selezionare lo SKU del gateway da usare. Esistono limitazioni di configurazione per alcuni SKU. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Se durante la creazione del gateway VPN si ottiene un errore relativo a -GatewaySku, verificare di avere installato la versione più recente dei cmdlet di PowerShell.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

Dopo aver eseguito questo comando, la configurazione del gateway può richiedere fino a 45 minuti.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Per la configurazione del dispositivo VPN è necessario quanto segue:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway di rete virtuale con PowerShell, usare l'esempio seguente. In questo esempio VNet1GWPIP è il nome della risorsa indirizzo IP pubblico creata nel passaggio precedente.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. creare la connessione VPN

Successivamente, creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori con i propri. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN. Si noti che "-ConnectionType" per la connessione da sito a sito è **IPsec**.

1. Impostare le variabili.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Creare la connessione.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

La connessione verrà stabilita in breve tempo.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. verificare la connessione VPN

Esistono diversi modi per verificare la connessione VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Per modificare i prefissi di indirizzo IP per un gateway di rete locale

In caso di modifica dei prefissi degli indirizzi IP da indirizzare alla posizione locale, è possibile modificare il gateway di rete locale. Sono disponibili due set di istruzioni. La scelta delle istruzioni dipende dal fatto che sia già stata creata o meno la connessione gateway. Quando si usano questi esempi, modificare i valori per adattarli all'ambiente.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Per modificare l'indirizzo IP del gateway per un gateway di rete locale

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>Per eliminare una connessione gateway

Se non si conosce il nome della connessione, è possibile trovarlo usando il cmdlet "Get-AzVirtualNetworkGatewayConnection".

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>Passaggi successivi

*  Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](../index.yml).
* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Per informazioni sulla creazione di una connessione VPN da sito a sito usando Azure Resource Manager modello, vedere [creare una connessione VPN da sito a sito](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Per informazioni sulla creazione di una connessione VPN da VNET a VNET usando Azure Resource Manager modello, vedere [distribuire la replica geografica di HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).