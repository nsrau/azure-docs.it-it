---
title: Configurare BGP sui gateway VPN di Azure - Resource Manager - PowerShell | Microsoft Docs
description: In questo articolo viene illustrata la configurazione di BGP con i gateway VPN di Azure tramite Azure Resource Manager e PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: b00a3fe7ba4b12c2e9c486188c292cd6fafb60a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Come configurare BGP sui gateway VPN di Azure tramite PowerShell
Questo articolo illustra i passaggi per abilitare BGP in una connessione VPN da sito a sito (S2S) cross-premise e in una connessione da rete virtuale a rete virtuale usando il modello di distribuzione Resource Manager e PowerShell.

## <a name="about-bgp"></a>Informazioni su BGP
BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. BGP consente ai gateway VPN di Azure e ai dispositivi VPN locali, detti peer BGP o router adiacenti, lo scambio di "route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router coinvolti. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP.

Vedere [Panoramica di BGP con i gateway VPN di Azure](vpn-gateway-bgp-overview.md) per altre informazioni sui vantaggi di BGP e per conoscere i requisiti tecnici e le considerazioni sull'uso di BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introduzione a BGP nei gateway VPN di Azure

L'articolo illustra la procedura per eseguire le attività seguenti:

* [Parte 1: Abilitare BGP nel gateway VPN di Azure](#enablebgp)
* [Parte 2: Stabilire una connessione cross-premise con BGP](#crossprembgp)
* [Parte 3: Stabilire una connessione da rete virtuale a rete virtuale con BGP](#v2vbgp)

Ogni parte delle istruzioni costituisce un blocco predefinito di base per abilitare BGP nella connettività di rete. Se si completano tutte e tre le parti, si crea la topologia illustrata nel diagramma seguente:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

È possibile combinare le parti per creare una rete di transito a più hop più complessa per soddisfare le proprie esigenze.

## <a name ="enablebgp"></a>Parte 1: Configurare BGP nel gateway VPN di Azure
La procedura di configurazione configura i parametri BGP nel gateway VPN di Azure come illustrato nel diagramma seguente:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Prima di iniziare
* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* Installare i cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>Passaggio 1: Creare e configurare VNet1
#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Per questo esercizio, si inizierà dichiarando le variabili. L'esempio seguente dichiara le variabili usando i valori per questo esercizio. Assicurarsi di sostituire i valori con quelli reali durante la configurazione per la produzione. Queste variabili possono essere usate se si esegue la procedura per acquisire familiarità con questo tipo di configurazione. Modificare le variabili, quindi copiarle e incollarle nella console di PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subcription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connettersi alla sottoscrizione e creare un nuovo gruppo di risorse
Per usare i cmdlet di Resource Manager assicurarsi di passare alla modalità PowerShell. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Creare TestVNet1
L'esempio seguente mostra come creare una rete virtuale denominata TestVNet1 e tre subnet, denominate rispettivamente GatewaySubnet, FrontEnd e Backend. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passaggio 2: Creare il gateway VPN per TestVNet1 con i parametri BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Creare le configurazioni IP e subnet
Richiedere un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Verranno create anche le configurazioni necessarie per subnet e IP.

```powershell
$gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Creare il gateway VPN con il numero AS
Creare il gateway di rete virtuale per TestVNet1. Per impostare il parametro ASN (AS Number, numero AS) per TestVNet1BGP, sono necessari un gateway VPN basato su route e il parametro aggiuntivo, -Asn. Se non si imposta il parametro ASN, a questo viene assegnato il valore 65515. La creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Ottenere l'indirizzo IP del peer BGP di Azure
Dopo la creazione del gateway, è necessario ottenere l'indirizzo IP del peer BGP per il gateway VPN di Azure. Questo indirizzo è necessario per configurare il gateway VPN di Azure come peer BGP per i dispositivi VPN locali.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

L'ultimo comando visualizza le configurazioni BGP corrispondenti nel gateway VPN di Azure, ad esempio:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Una volta creato il gateway, è possibile usarlo per stabilire una connessione cross-premise o una connessione da rete virtuale a rete virtuale con BGP. Le sezioni seguenti illustrano i passaggi per il completamento dell'esercizio.

## <a name ="crossprembbgp"></a>Parte 2: Stabilire una connessione cross-premise con BGP

Per stabilire una connessione cross-premise, è necessario creare un gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN al gateway di rete locale. Anche se sono disponibili articoli che descrivono questi passaggi in dettaglio, questo articolo contiene le proprietà aggiuntive necessarie per specificare i parametri di configurazione di BGP.

![BGP per la connessione cross-premise](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Prima di procedere, verificare di avere completato la [Parte 1](#enablebgp) di questo esercizio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1: Creare e configurare il gateway di rete locale

#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili

Questo esercizio continua a creare la configurazione illustrata nel diagramma. Sostituire i valori con quelli desiderati per la propria configurazione.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

È necessario tenere presenti due aspetti relativi ai parametri del gateway di rete:

* Il gateway di rete locale può essere nella stessa località e nello stesso gruppo di risorse del gateway VPN oppure in altri. In questo esempio sono in gruppi di risorse diversi in località diverse.
* Il prefisso minimo che è necessario dichiarare per il gateway di rete locale è l'indirizzo host dell'indirizzo IP del peer BGP nel dispositivo VPN. In questo caso, è un prefisso /32 di "10.52.255.254/32".
* Si ricordi che è necessario usare ASN BGP diversi nelle reti locali e nella rete virtuale di Azure. Se sono gli stessi, è necessario modificare l'ASN della rete virtuale se il dispositivo VPN locale usa già l'ASN per il peering con altri vicini BGP.

Prima di proseguire, verificare di essere ancora connessi alla sottoscrizione 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Creare il gateway di rete locale per Site5

Assicurarsi di creare il gruppo di risorse se non è stato creato, prima di creare il gateway di rete locale. Si notino i due parametri aggiuntivi per il gateway di rete locale: Asn e BgpPeerAddress.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passaggio 2: Connettere il gateway di rete virtuale e il gateway di rete locale

#### <a name="1-get-the-two-gateways"></a>1. Ottenere i due gateway

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Creare la connessione da TestVNet1 a Site5

In questo passaggio viene creata la connessione da TestVNet1 a Site5. È necessario specificare "-EnableBgp $True" per abilitare BGP per questa connessione. Come illustrato in precedenza, è possibile avere connessioni sia BGP che non BGP per lo stesso gateway VPN di Azure. A meno che BGP non venga abilitato nella proprietà della connessione, Azure non abiliterà BGP per questa connessione nemmeno se i parametri BGP sono già configurati in entrambi i gateway.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

L'esempio seguente elenca i parametri da immettere nella sezione della configurazione BGP nel dispositivo VPN locale per questo esercizio:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

La connessione viene stabilita in pochi minuti e la sessione di peering BGP viene avviata non appena stabilita la connessione IPsec.

## <a name ="v2vbgp"></a>Parte 3: Stabilire una connessione da rete virtuale a rete virtuale con BGP

Questa sezione aggiunge una connessione da rete virtuale a rete virtuale con BGP, come illustrato nel diagramma seguente:

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Le istruzioni seguenti sono la prosecuzione dei passaggi precedenti. È necessario completare la [Parte I](#enablebgp) per creare e configurare TestVNet1 e il gateway VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1: Creare TestVNet2 e il gateway VPN

È importante verificare che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet2, non si sovrapponga ad altri di intervalli di rete virtuale.

In questo esempio le reti virtuali appartengono alla stessa sottoscrizione. È possibile configurare connessioni da rete virtuale a rete virtuale tra sottoscrizioni diverse. Per altre informazioni, vedere [Configurare una connessione tra reti virtuali](vpn-gateway-vnet-vnet-rm-ps.md). Assicurarsi di aggiungere "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili

Sostituire i valori con quelli desiderati per la propria configurazione.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Creare il gateway VPN per TestVNet2 con i parametri BGP

Richiedere un indirizzo IP pubblico da allocare al gateway che verrà creato per la rete virtuale e definire le configurazioni necessarie per subnet e IP.

```powershell
$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Creare il gateway VPN con il numero AS. È necessario eseguire l'override dell'ASN predefinito nei gateway VPN di Azure. Gli ASN per le reti virtuali connesse devono essere diversi per abilitare BGP e il routing di transito.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
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

In questo passaggio vengono create le connessioni rispettivamente da TestVNet1 a TestVNet2 e da TestVNet2 a TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Assicurarsi di abilitare BGP per ENTRAMBE le connessioni.
> 
> 

Dopo il completamento di questi passaggi, la connessione viene stabilita entro pochi minuti. Non appena completata la connessione da rete virtuale a rete virtuale, la sessione di peering BGP è attiva.

Se sono state completate tutte e tre le parti di questo esercizio, è stata stabilita la topologia di rete seguente:

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .