---
title: Configurare BGP sui gateway VPN di Azure - Resource Manager - Interfaccia della riga di comando | Microsoft Docs
description: Questo articolo illustra la configurazione di BGP con i gateway VPN di Azure tramite Azure Resource Manager e l'interfaccia della riga di comando.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 16fd25b0271b8fefe71d069215c9ebe0bc73a80d
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-cli"></a>Come configurare BGP sui gateway VPN di Azure tramite l'interfaccia della riga di comando

Questo articolo illustra come abilitare BGP in una connessione VPN da sito a sito (S2S) cross-premise e in una connessione da rete virtuale a rete virtuale usando il modello di distribuzione Resource Manager e l'interfaccia della riga di comando.

## <a name="about-bgp"></a>Informazioni su BGP

BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. BGP abilita i gateway VPN e i dispositivi VPN locali, definiti peer o vicini BGP, per consentire lo scambio di "route". Le route forniscono ai gateway informazioni sulla disponibilità e sull'accessibilità dei prefissi per l'attraversamento dei gateway o dei router interessati. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP.

Per altre informazioni sui vantaggi di BGP, sui requisiti tecnici e sulle considerazioni relative all'uso di BGP, vedere [Panoramica di BGP con i gateway VPN di Azure](vpn-gateway-bgp-overview.md).

Questo articolo illustra come eseguire le attività seguenti:

* [Abilitare BGP per il gateway VPN](#enablebgp) (obbligatorio)

  È quindi possibile configurare una o entrambe le sezioni seguenti:

* [Stabilire una connessione cross-premise con BGP](#crossprembgp)
* [Stabilire una connessione da rete virtuale a rete virtuale con BGP](#v2vbgp)

Ognuna delle tre sezioni costituisce un blocco predefinito di base per abilitare BGP nella connettività di rete. Se si completano tutte e tre le sezioni, si crea la topologia illustrata nel diagramma seguente:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

È possibile combinare le sezioni per creare una rete di transito a più hop più complessa per soddisfare le proprie esigenze.

## <a name ="enablebgp"></a>Abilitare BGP per il gateway VPN

Questa sezione è obbligatoria prima dell'esecuzione dei passaggi illustrati nelle altre due sezioni di configurazione. La procedura di configurazione seguente configura i parametri BGP nel gateway VPN di Azure come illustrato nel diagramma seguente:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Prima di iniziare

Installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0) e [Get Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Introduzione all'interfaccia della riga di comando di Azure 2.0).

### <a name="step-1---create-and-configure-testvnet1"></a>Passaggio 1: Creare e configurare TestVNet1

#### <a name="Login"></a>1. Eseguire la connessione alla sottoscrizione

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Creare un gruppo di risorse

L'esempio seguente crea un gruppo di risorse denominato "TestRG1" nella località "eastus". Se è già disponibile un gruppo di risorse nell'area in cui si vuole creare la rete virtuale, è possibile usarlo.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Creare TestVNet1

L'esempio seguente mostra come creare una rete virtuale denominata TestVNet1 e tre subnet, denominate rispettivamente GatewaySubnet, FrontEnd e Backend. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

Il primo comando crea lo spazio degli indirizzi front-end e la subnet FrontEnd. Il secondo comando crea uno spazio indirizzi aggiuntivo per la subnet back-end. Il terzo e il quarto comando creano la subnet BackEnd e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passaggio 2: Creare il gateway VPN per TestVNet1 con i parametri BGP

#### <a name="1-create-the-public-ip-address"></a>1. Creare l'indirizzo IP pubblico

Richiedere un indirizzo IP pubblico. L'indirizzo IP pubblico verrà allocato al gateway VPN creato per la rete virtuale.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Creare il gateway VPN con il numero AS

Creare il gateway di rete virtuale per TestVNet1. BGP richiede un gateway VPN basato su route. È necessario anche il parametro aggiuntivo '-Asn' per la configurazione del valore ASN (numero AS) per TestVNet1. La creazione di un gateway può richiedere anche oltre 45 minuti. 

Se si esegue questo comando usando il parametro '--no-wait', non viene visualizzato alcun output o commento. Il parametro "--no-wait" consente la creazione in background del gateway. Questo non significa che la creazione del gateway VPN termina immediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Ottenere l'indirizzo IP del peer BGP di Azure

Dopo la creazione del gateway, è necessario ottenere l'indirizzo IP del peer BGP per il gateway VPN di Azure. Questo indirizzo è necessario per configurare il gateway VPN come peer BGP per i dispositivi VPN locali.

Eseguire il comando seguente e verificare la sezione bgpSettings nella parte superiore dell'output

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Dopo avere creato il gateway, è possibile usarlo per stabilire una connessione cross-premise o una connessione da rete virtuale a rete virtuale con BGP.

## <a name ="crossprembgp"></a>Stabilire una connessione cross-premise con BGP

Per stabilire una connessione cross-premise, è necessario creare un gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN di Azure al gateway di rete locale. Anche se questi passaggi sono simili a quelli per la creazione di altre connessioni, includono le proprietà aggiuntive necessarie per specificare i parametri di configurazione per BGP.

![BGP per la connessione cross-premise](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1: Creare e configurare il gateway di rete locale

Questo esercizio continua a creare la configurazione illustrata nel diagramma. Sostituire i valori con quelli desiderati per la propria configurazione. Quando si usano i gateway di rete locali, occorre tenere presente quanto segue:

* Il gateway di rete locale può essere nella stessa località e nello stesso gruppo di risorse del gateway VPN oppure in altri. In questo esempio sono in gruppi di risorse diversi in località diverse.
* Il prefisso minimo che è necessario dichiarare per il gateway di rete locale è l'indirizzo host dell'indirizzo IP del peer BGP nel dispositivo VPN. In questo caso, è un prefisso /32 di "10.52.255.254/32".
* Si ricordi che è necessario usare ASN BGP diversi nelle reti locali e nella rete virtuale di Azure. Se sono uguali, è necessario modificare il valore ASN della rete locale se i dispositivi VPN locali usano già il valore ASN per il peering con altri vicini BGP.

Prima di continuare, assicurarsi di avere completato la sezione [Abilitare BGP per il gateway VPN](#enablebgp) di questo esercizio e che sia ancora attiva la connessione alla sottoscrizione 1. Si noti che in questo esempio viene creato un nuovo gruppo di risorse. Si notino anche i due parametri aggiuntivi per il gateway di rete locale: Asn e BgpPeerAddress.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passaggio 2: Connettere il gateway di rete virtuale e il gateway di rete locale

In questo passaggio viene creata la connessione da TestVNet1 a Site5. È necessario specificare il parametro '--enable-bgp' per abilitare BGP per questa connessione. 

In questo esempio il gateway di rete virtuale e il gateway di rete locale sono presenti in gruppi di risorse diversi. Quando i gateway si trovano in gruppi di risorse diversi, è necessario specificare l'intero ID risorsa dei due gateway per stabilire una connessione tra reti virtuali.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Ottenere l'ID risorsa di VNet1GW

Usare l'output del comando seguente per ottenere l'ID risorsa per VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Nell'output trovare la riga "id:". I valori tra virgolette sono necessari per creare la connessione nella sezione successiva. Copiare questi valori in un editor di testo, ad esempio il Blocco note, per poterli incollare facilmente quando si crea la connessione.

Output di esempio:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Copiare i valori dopo **"id":** tra virgolette. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Ottenere l'ID risorsa di Site5

Usare il comando seguente per ottenere l'ID risorsa di Site5 dall'output:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Creare la connessione da TestVNet1 a Site5

In questo passaggio viene creata la connessione da TestVNet1 a Site5. Come illustrato in precedenza, è possibile avere connessioni sia BGP che non BGP per lo stesso gateway VPN di Azure. A meno che BGP non venga abilitato nella proprietà della connessione, Azure non abiliterà BGP per questa connessione nemmeno se i parametri BGP sono già configurati in entrambi i gateway. Assicurarsi di sostituire gli ID delle sottoscrizioni con i propri.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Per questo esercizio l'esempio seguente elenca i parametri da immettere nella sezione della configurazione BGP nel dispositivo VPN locale:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Dopo alcuni minuti, la connessione dovrebbe stabilirsi. La sessione di peering BGP viene avviata non appena viene stabilita la connessione IPsec.

## <a name ="v2vbgp"></a>Stabilire una connessione da rete virtuale a rete virtuale con BGP

Questa sezione aggiunge una connessione da rete virtuale a rete virtuale con BGP, come illustrato nel diagramma seguente: 

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Le istruzioni seguenti sono la prosecuzione dei passaggi precedenti illustrati nelle sezioni precedenti. È necessario completare la sezione [Abilitare BGP per il gateway VPN](#enablebgp) per creare e configurare TestVNet1 e il gateway VPN con BGP.

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1: Creare TestVNet2 e il gateway VPN

È importante verificare che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet2, non si sovrapponga ad altri di intervalli di rete virtuale.

In questo esempio le reti virtuali appartengono alla stessa sottoscrizione. È possibile configurare connessioni da rete virtuale a rete virtuale tra sottoscrizioni diverse. Per altri dettagli, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-howto-vnet-vnet-cli.md). Assicurarsi di aggiungere "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Creare un nuovo gruppo di risorse

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Creare TestVNet2 nel nuovo gruppo di risorse

Il primo comando crea lo spazio degli indirizzi front-end e la subnet FrontEnd. Il secondo comando crea uno spazio indirizzi aggiuntivo per la subnet back-end. Il terzo e il quarto comando creano la subnet BackEnd e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Creare l'indirizzo IP pubblico

Richiedere un indirizzo IP pubblico. L'indirizzo IP pubblico verrà allocato al gateway VPN creato per la rete virtuale.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Creare il gateway VPN con il numero AS

Creare il gateway di rete virtuale per TestVNet2. È necessario eseguire l'override dell'ASN predefinito nei gateway VPN di Azure. Gli ASN per le reti virtuali connesse devono essere diversi per abilitare BGP e il routing di transito.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passaggio 2: Connettere i gateway TestVNet1 e TestVNet2

In questo passaggio viene creata la connessione da TestVNet1 a Site5. Per abilitare BGP per questa connessione, è necessario specificare il parametro '--enable-bgp'.

Nell'esempio seguente il gateway di rete virtuale e il gateway di rete locale sono presenti in gruppi di risorse diversi. Quando i gateway si trovano in gruppi di risorse diversi, è necessario specificare l'intero ID risorsa dei due gateway per stabilire una connessione tra reti virtuali. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Ottenere l'ID risorsa di VNet1GW 

Ottenere l'ID risorsa di VNet1GW dall'output del comando seguente:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Ottenere l'ID risorsa di VNet2GW

Ottenere l'ID risorsa di VNet2GW dall'output del comando seguente:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Creare le connessioni

Creare la connessione da TestVNet1 a TestVNet2 e da TestVNet2 a TestVNet1. Assicurarsi di sostituire gli ID delle sottoscrizioni con i propri.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Assicurarsi di abilitare BGP per ENTRAMBE le connessioni.
> 
> 

Dopo il completamento di questi passaggi, la connessione verrà stabilita entro pochi minuti. Non appena completata la connessione da rete virtuale a rete virtuale, la sessione di peering BGP sarà attiva.

Se si completano tutte e tre le sezioni dell'esercizio, si crea la topologia di rete mostrata nell'esempio seguente:

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .
