---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito: interfaccia della riga di comando | Microsoft Docs'
description: "Passaggi per creare una connessione IPsec dalla rete locale a una rete virtuale di Azure tramite Internet pubblico. Questa procedura consentirà di creare una connessione gateway VPN da sito a sito cross-premise usando l'interfaccia della riga di comando."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: d97dfa3bc14c54e4c7097b5418c5b61e204e7676
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Creare una rete virtuale con una connessione VPN da sito a sito usando l'interfaccia della riga di comando

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:<br>

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.
* Verificare di avere installato la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0) e [Get Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0).

### <a name="example-values"></a>Valori di esempio

È possibile usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Eseguire la connessione alla sottoscrizione

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a>2. Creare un gruppo di risorse

L'esempio seguente crea un gruppo di risorse denominato "TestRG1" nella località "eastus". Se è già disponibile un gruppo di risorse nell'area in cui si vuole creare la rete virtuale, è possibile usarlo.

```azurecli
az group create --name TestRG1 --location eastus
```

## <a name="VNet"></a>3. Crea rete virtuale

Se non si ha già una rete virtuale, crearne una usando il comando [az network vnet create](/cli/azure/network/vnet#create). Quando si crea una rete virtuale, verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.

L'esempio seguente crea una rete virtuale denominata "TestVNet1" e una subnet "Subnet1".

```azurecli
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## 4. <a name="gwsub"></a>Creare la subnet del gateway

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Per questa configurazione, è necessaria anche una subnet del gateway. Il gateway di rete virtuale usa una subnet del gateway che contiene gli indirizzi IP usati dai servizi del gateway VPN. Quando si crea una subnet del gateway, deve essere denominata "GatewaySubnet". Se si assegna un nome diverso, la subnet viene creata, ma non verrà trattata da Azure come una subnet del gateway.

Le dimensioni della subnet del gateway specificata dipendono dalla configurazione del gateway VPN che si vuole creare. Nonostante sia possibile creare una subnet del gateway con dimensioni di /29 soltanto, è consigliabile crearne una più grande che includa più indirizzi selezionando /27 o /28. L'uso di una subnet del gateway di dimensioni maggiori consente un numero sufficiente di indirizzi IP per eventuali configurazioni future.

Usare il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) per creare la subnet gateway.

```azurecli
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Creare il gateway di rete locale

Il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure possa usare come riferimento, quindi specificare l'indirizzo IP del dispositivo VPN locale con cui si creerà una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata, è possibile aggiornare facilmente i prefissi.

Usare i valori seguenti:

* *--gateway-ip-address* è l'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT.
* *--local-address-prefixes* sono gli spazi di indirizzi locali.

Usare il comando [az network local-gateway create](/cli/azure/network/local-gateway#create) per aggiungere un gateway di rete locale con più prefissi degli indirizzi:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Richiedere un indirizzo IP pubblico

Un gateway VPN deve avere un indirizzo IP pubblico. È necessario richiedere prima di tutto la risorsa dell'indirizzo IP e quindi farvi riferimento durante la creazione del gateway di rete virtuale. L'indirizzo IP viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Non è possibile richiedere un'assegnazione degli indirizzi IP pubblici statici. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

Usare il comando [az network public-ip create](/cli/azure/network/public-ip#create) per richiedere un indirizzo IP pubblico dinamico.

```azurecli
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Creare il gateway VPN

Creare il gateway VPN di rete virtuale. Per completare la creazione di un gateway VPN, possono essere necessari fino a 45 minuti o più.

Usare i valori seguenti:

* Il valore di *--gateway-type* per una configurazione da sito a sito è *Vpn*. Il tipo di gateway è sempre specifico della configurazione che si sta implementando. Per altre informazioni, vedere [Tipi di gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* Il valore di *--vpn-type* può essere *RouteBased*, talvolta definito gateway dinamico nella documentazione, o *PolicyBased*, talvolta definito gateway statico nella documentazione. L'impostazione è specifica dei requisiti del dispositivo a cui ci si connette. Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Selezionare lo SKU del gateway da usare. Esistono limitazioni di configurazione per alcuni SKU. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Creare il gateway VPN usando il comando [az network vnet-gateway create](/cli/azure/network/vnet-gateway#create). Se si esegue questo comando usando il parametro '--no-wait', non viene visualizzato alcun output o commento. Questo parametro consente la creazione in background del gateway. La creazione di un gateway richiede circa 45 minuti.

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="VPNDevice"></a>8. Configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Durante la configurazione del dispositivo VPN, è necessario quanto segue:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, usare il comando [az network public-ip list](/cli/azure/network/public-ip#list). Per facilitare la lettura, la formattazione dell'output visualizza l'elenco di IP pubblici in formato tabella.

  ```azurecli
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>9. Creare la connessione VPN

Creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale. Prestare particolare attenzione al valore della chiave condivisa, che deve corrispondere al valore della chiave condivisa configurato per il dispositivo VPN.

Creare la connessione usando il comando [az network vpn-connection create](/cli/azure/network/vpn-connection#create).

```azurecli
az network vpn-connection create --name VNet1toSite2 -resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

La connessione verrà stabilita in breve tempo.

## <a name="toverify"></a>10. Verificare la connessione VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Per usare un altro metodo per verificare la connessione, vedere [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="connectVM"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="common-tasks"></a>Attività comuni

Questa sezione contiene i comandi comuni che risultano utili quando si usano configurazioni da sito a sito. Per l'elenco completo dei comandi di rete dell'interfaccia della riga di comando, vedere [Azure CLI - Networking](/cli/azure/network) (Interfaccia della riga di comando di Azure - Rete).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Per informazioni sul tunneling forzato, vedere [Informazioni sul tunneling forzato](vpn-gateway-forced-tunneling-rm.md).
* Per informazioni sulle connessioni da rete attiva a rete attiva a disponibilità elevata, vedere [Connettività cross-premise e da rete virtuale a rete virtuale a disponibilità elevata](vpn-gateway-highlyavailable.md).
* Per un elenco di comandi dell'interfaccia della riga di comando di Azure di rete, vedere [Azure CLI](https://docs.microsoft.com/cli/azure/network) (Interfaccia della riga di comando di Azure).
