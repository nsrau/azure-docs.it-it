---
title: Configurare connessioni di rete privata virtuale da sito a sito active-active per i gateway VPN - Azure Resource Manager - PowerShell | Microsoft Docs
description: In questo articolo viene illustrata la configurazione di connessioni active-active con i gateway VPN di Azure tramite Azure Resource Manager e PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: ea5546a636bd567853438ae2620ae24ce2d7da23
ms.lasthandoff: 04/27/2017


---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configurare connessioni di rete privata virtuale da sito a sito active-active con gateway VPN di Azure

Questo articolo illustra i passaggi per creare connessioni cross-permise active-active e da rete virtuale a rete virtuale usando il modello di distribuzione Resource Manager e PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>Informazioni sulle connessioni cross-premise a disponibilità elevata
Per ottenere una disponibilità elevata per la connettività cross-premise e da rete virtuale a rete virtuale, occorre distribuire più gateway VPN e stabilire più connessioni parallele tra le reti e Azure. Per una panoramica delle opzioni di connettività e della topologia, vedere [Highly Available Cross-Premises and VNet-to-VNet Connectivity](vpn-gateway-highlyavailable.md) (Connettività cross-premise e da rete virtuale a rete virtuale a disponibilità elevata).

In questo articolo vengono fornite le istruzioni per configurare una connessione VPN cross-premise active-active tra due reti virtuali:

* [Parte 1: creare e configurare il gateway VPN di Azure in modalità active-active](#aagateway)
* [Parte 2: stabilire connessioni cross-premise active-active](#aacrossprem)
* [Parte 3: stabilire connessioni da rete virtuale a rete virtuale active-active](#aav2v)
* [Parte 4: aggiornare il gateway esistente tra modalità active-active e active-standby](#aaupdate)

È possibile combinare questi elementi per creare una topologia di rete altamente scalabile e più complessa adatta alle proprie esigenze.

> [!IMPORTANT]
> Notare che la modalità active-active funziona solo negli SKU HighPerformance
> 
> 

## <a name ="aagateway"></a>Parte 1: creare e configurare gateway VPN active-active
Con i seguenti passaggi si configurerà il gateway VPN di Azure in modalità active-active. Le differenze principali tra i gateway active-active e active-standby sono:

* È necessario creare due configurazioni IP del gateway con due indirizzi IP pubblici
* È necessario impostare il contrassegno EnableActiveActiveFeature
* Lo SKU del gateway deve essere HighPerformance

Le altre proprietà corrispondono a quelle dei gateway non active-active. 

### <a name="before-you-begin"></a>Prima di iniziare
* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* È necessario installare i cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione di cmdlet PowerShell, vedere [Overview of Azure PowerShell](/powershell/azure/overview) (Panoramica di Azure PowerShell).

### <a name="step-1---create-and-configure-vnet1"></a>Passaggio 1: Creare e configurare VNet1
#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Per questo esercizio, si inizierà dichiarando le variabili. Nell'esempio seguente vengono dichiarate le variabili usando i valori per questo esercizio. Assicurarsi di sostituire i valori con quelli reali durante la configurazione per la produzione. Queste variabili possono essere usate se si esegue la procedura per acquisire familiarità con questo tipo di configurazione. Modificare le variabili, quindi copiarle e incollarle nella console di PowerShell.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connettersi alla sottoscrizione e creare un nuovo gruppo di risorse
Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Creare TestVNet1
Nell'esempio seguente vengono create una rete virtuale denominata TestVNet1 e tre subnet, denominate GatewaySubnet, FrontEnd e Backend. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway avrà esito negativo.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passaggio 2: creare il gateway VPN per TestVNet1 con la modalità active-active
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Creare gli indirizzi IP pubblici e le configurazioni IP del gateway
Richiedere due indirizzi IP pubblici da allocare al gateway che verrà creato per la rete virtuale. Si definiranno anche le configurazioni subnet e IP necessarie.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Creare il gateway VPN per la configurazione active-active
Creare il gateway di rete virtuale per TestVNet1. Notare che sono presenti due voci GatewayIpConfig e che viene impostato il contrassegno EnableActiveActiveFeature. La modalità active-active richiede un gateway VPN basato su route dello SKU HighPerformance. La creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Ottenere gli indirizzi IP pubblici del gateway e l'indirizzo IP di peering BGP
Una volta creato il gateway, sarà necessario ottenere l'indirizzo IP del peer BGP nel gateway VPN di Azure. Questo indirizzo è necessario per configurare il gateway VPN di Azure come peer BGP per i dispositivi VPN locali.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Usare i cmdlet seguenti per visualizzare i due indirizzi IP pubblici allocati per il gateway VPN e gli indirizzi IP di peering BGP corrispondenti per ogni istanza del gateway:

```powershell

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }
```

L'ordine degli indirizzi IP pubblici per le istanze del gateway e i relativi indirizzi di peering BGP corrispondono. In questo esempio, la VM del gateway con l'indirizzo IP pubblico 40.112.190.5 userà 10.12.255.4 come indirizzo di peering BGP, mentre il gateway con 138.91.156.129 userà 10.12.255.5. Queste informazioni sono necessarie quando si configurano dispositivi VPN locali che si connettono al gateway active-active. Il gateway è mostrato nel diagramma riportato di seguito con tutti gli indirizzi:

![active-active gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Una volta creato il gateway, è possibile usarlo per stabilire una connessione da rete virtuale a rete virtuale o cross-premise active-active. Le sezioni seguenti illustrano i passaggi per completare l'esercizio.

## <a name ="aacrossprem"></a>Parte 2: stabilire una connessione cross-premise active-active
Per stabilire una connessione cross-premise, è necessario creare un gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN di Azure al gateway di rete locale. In questo esempio, il gateway VPN di Azure è in modalità active-active. Di conseguenza, anche se sono presenti un solo dispositivo VPN locale (gateway di rete locale) e una risorsa di connessione, entrambe le istanze del gateway VPN di Azure stabiliranno tunnel VPN S2S con il dispositivo locale.

Prima di procedere, verificare di avere completato la [Parte 1](#aagateway) di questo esercizio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1: Creare e configurare il gateway di rete locale
#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Questo esercizio continuerà a creare la configurazione illustrata nel diagramma. Sostituire i valori con quelli desiderati per la propria configurazione.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

È necessario tenere presenti due aspetti relativi ai parametri del gateway di rete:

* Il gateway di rete locale può essere nella stessa località e nello stesso gruppo di risorse del gateway VPN oppure in altri. In questo esempio vengono mostrati in gruppi di risorse diversi ma nello stesso percorso di Azure.
* Se è presente un solo dispositivo VPN locale come illustrato sopra, la connessione di tipo active-active può funzionare con o senza protocollo BGP. In questo esempio viene usato il protocollo BGP per la connessione cross-premise.
* Se è abilitato BGP, il prefisso che è necessario dichiarare per il gateway di rete locale è l'indirizzo host dell'indirizzo IP di peering BGP nel dispositivo VPN. In questo caso, è un prefisso /32 di "10.52.255.253/32".
* Si ricordi che è necessario usare ASN BGP diversi nelle reti locali e nella rete virtuale di Azure. Se sono gli stessi, è necessario modificare l'ASN della rete virtuale se il dispositivo VPN locale usa già l'ASN per il peering con altri vicini BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Creare il gateway di rete locale per Site5
Prima di proseguire, verificare di essere ancora connessi alla sottoscrizione 1. Creare il gruppo di risorse se non è ancora stato creato.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passaggio 2: Connettere il gateway di rete virtuale e il gateway di rete locale
#### <a name="1-get-the-two-gateways"></a>1. Ottenere i due gateway

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Creare la connessione da TestVNet1 a Site5
In questo passaggio si creerà la connessione da TestVNet1 a Site5_1 con "EnableBGP" impostato su $True.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parametri di VPN e BGP per il dispositivo VPN locale
L'esempio seguente elenca i parametri che si immetteranno nella sezione della configurazione BGP nel dispositivo VPN locale per questo esercizio:

    - Site5 ASN            : 65050
    - Site5 BGP IP         : 10.52.255.253
    - Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
    - Azure VNet ASN       : 65010
    - Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
    - Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
    - Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5                        Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
    - eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

La connessione verrà stabilita in pochi minuti e la sessione di peering BGP verrà avviata una volta stabilità la connessione IPSec. In questo esempio finora è stato configurato un solo dispositivo VPN locale; il risultato è nel diagramma di seguito:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passaggio 3: connettere due dispositivi VPN locali al gateway VPN active-active
Se si dispone di due dispositivi VPN nella stessa rete locale, è possibile ottenere ridondanza doppia connettendo il gateway VPN di Azure al secondo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Creare il secondo gateway di rete locale per Site5
Notare che l'indirizzo IP del gateway, il prefisso dell'indirizzo e l'indirizzo di peering BGP per il secondo gateway di rete locale non devono sovrapporsi al gateway di rete locale precedente per la stessa rete locale.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"

New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Connettere il gateway di rete virtuale e il secondo gateway di rete locale
Creare la connessione da TestVNet1 a Site5_2 con "EnableBGP" impostato su $True.

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametri di VPN e BGP per il secondo dispositivo VPN locale
In modo analogo, di seguito sono elencati i parametri che verranno immessi nel secondo dispositivo VPN:

      - Site5 ASN            : 65050
      - Site5 BGP IP         : 10.52.255.254
      - Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
      - Azure VNet ASN       : 65010
      - Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
      - Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
      - Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                             Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
      - eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

Una volta che viene stabilita la connessione (tunnel), si disporrà di dispositivi VPN a doppia ridondanza e di tunnel che connettono la rete locale e Azure:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Parte 3: stabilire una connessione da rete virtuale a rete virtuale active-active
In questa sezione viene creata una connessione da rete virtuale a rete virtuale active-active con BGP. 

Le istruzioni seguenti sono la prosecuzione dei passaggi precedenti. È necessario completare la [Parte 1](#aagateway) per creare e configurare TestVNet1 e il gateway VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1: Creare TestVNet2 e il gateway VPN
È importante verificare che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet2, non si sovrapponga ad altri di intervalli di rete virtuale.

In questo esempio le reti virtuali appartengono alla stessa sottoscrizione. È possibile configurare connessioni da rete virtuale a rete virtuale tra sottoscrizioni diverse. Per altri dettagli, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md). Assicurarsi di aggiungere "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Sostituire i valori con quelli desiderati per la propria configurazione.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Creare TestVNet2 nel nuovo gruppo di risorse

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Creare il gateway VPN active-active per TestVNet2
Richiedere due indirizzi IP pubblici da allocare al gateway che verrà creato per la rete virtuale. Si definiranno anche le configurazioni subnet e IP necessarie.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Creare il gateway VPN con il numero AS e il contrassegno "EnableActiveActiveFeature". Si noti che è necessario eseguire l'override dell'ASN predefinito nei gateway VPN di Azure. Gli ASN per le reti virtuali connesse devono essere diversi per abilitare BGP e il routing di transito.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passaggio 2: Connettere i gateway TestVNet1 e TestVNet2
In questo esempio entrambi i gateway sono nella stessa sottoscrizione. È possibile completare questo passaggio nella stessa sessione di PowerShell.

#### <a name="1-get-both-gateways"></a>1. Ottenere entrambi i gateway
Eseguire l'accesso e connettersi alla Sottoscrizione 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Creare entrambe le connessioni
In questo passaggio si creerà la connessione da TestVNet1 a TestVNet2 e la connessione da TestVNet2 a TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Assicurarsi di abilitare BGP per ENTRAMBE le connessioni.
> 
> 

Dopo avere completato questi passaggi, la connessione verrà stabilita in pochi minuti e la sessione di peering BGP sarà attiva una volta completata la connessione da rete virtuale e rete virtuale con doppia ridondanza:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4: aggiornare il gateway esistente tra modalità active-active e active-standby
Nell'ultima sezione verrà descritto come configurare un gateway VPN di Azure esistente dalla modalità active-standby alla modalità active-active o viceversa.

> [!IMPORTANT]
> Notare che la modalità active-active funziona solo negli SKU HighPerformance
> 
> 

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurare un gateway da active-standby a active-active
#### <a name="1-gateway-parameters"></a>1. Parametri del gateway
Nell'esempio seguente un gateway active-standby viene convertito in gateway active-active. È necessario creare un altro indirizzo IP pubblico, quindi aggiungere una seconda configurazione IP del gateway. Di seguito vengono illustrati i parametri usati:

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"

$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Creare un altro indirizzo IP pubblico, quindi aggiungere una seconda configurazione IP del gateway

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Abilitare la modalità active-active e aggiornare il gateway
È necessario impostare l'oggetto del gateway in PowerShell per attivare l'aggiornamento effettivo. Anche lo SKU dell'oggetto gateway deve essere modificato in HighPerformance, perché è stato creato in precedenza come Standard.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

Questo aggiornamento può richiedere da 30 a 45 minuti.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurare un gateway da active-active a active-standby
#### <a name="1-gateway-parameters"></a>1. Parametri del gateway
Usare gli stessi parametri riportati sopra e ottenere il nome della configurazione IP che si desidera rimuovere.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"

$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Rimuovere la configurazione IP del gateway e disabilitare la modalità active-active
In modo analogo, impostare l'oggetto del gateway in PowerShell per attivare l'aggiornamento effettivo.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Questo aggiornamento può richiedere fino a 30-45 minuti.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .


