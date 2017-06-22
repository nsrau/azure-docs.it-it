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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: d1f1852d720ebf51df9ec3207b070510b08a6483
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017


---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Come configurare BGP sui gateway VPN di Azure tramite PowerShell
Questo articolo illustra i passaggi per abilitare BGP in una connessione VPN da sito a sito (S2S) cross-premise e in una connessione da rete virtuale a rete virtuale usando il modello di distribuzione Resource Manager e PowerShell.

## <a name="about-bgp"></a>Informazioni su BGP
BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. BGP consente ai gateway VPN di Azure e ai dispositivi VPN locali, detti peer BGP o router adiacenti, lo scambio di "route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router coinvolti. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP.

Vedere [Panoramica di BGP con i gateway VPN di Azure](vpn-gateway-bgp-overview.md) per altre informazioni sui vantaggi di BGP e per conoscere i requisiti tecnici e le considerazioni sull'uso di BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introduzione a BGP nei gateway VPN di Azure
L'articolo illustra i passaggi per eseguire queste attività:

* [Parte 1: Abilitare BGP nel gateway VPN di Azure](#enablebgp)
* [Parte 2: Stabilire una connessione cross-premise con BGP](#crossprembgp)
* [Parte 3: Stabilire una connessione da rete virtuale a rete virtuale con BGP](#v2vbgp)

Ogni parte delle istruzioni costituisce un blocco predefinito di base per abilitare BGP nella connettività di rete. Se si completano tutte e tre le parti, si creerà la topologia illustrata nel diagramma seguente:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

È possibile combinare questi elementi per creare una rete di transito più complessa a più hop per soddisfare le proprie esigenze.

## <a name ="enablebgp"></a>Parte 1: Configurare BGP nel gateway VPN di Azure
La procedura di configurazione seguente configurerà i parametri BGP nel gateway VPN di Azure come illustrato nel diagramma seguente:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Prima di iniziare
* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* È necessario installare i cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .

### <a name="step-1---create-and-configure-vnet1"></a>Passaggio 1: Creare e configurare VNet1
#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Per questo esercizio, si inizierà dichiarando le variabili. Nell'esempio seguente vengono dichiarate le variabili usando i valori per questo esercizio. Assicurarsi di sostituire i valori con quelli reali durante la configurazione per la produzione. Queste variabili possono essere usate se si esegue la procedura per acquisire familiarità con questo tipo di configurazione. Modificare le variabili, quindi copiarle e incollarle nella console di PowerShell.

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
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passaggio 2: Creare il gateway VPN per TestVNet1 con i parametri BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Creare le configurazioni IP e subnet
Richiedere un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Si definiranno anche le configurazioni subnet e IP necessarie.

```powershell
$gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Creare il gateway VPN con il numero AS
Creare il gateway di rete virtuale per TestVNet1. Si noti che BGP richiede un Gateway VPN basato su route e anche il parametro aggiuntivo, -Asn, per impostare ASN (numero AS) per TestVNet1. Se non si imposta il parametro ASN, verrà assegnato ASN 65515. La creazione di un gateway può richiedere un po' di tempo (30 minuti o più).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Ottenere l'indirizzo IP del peer BGP di Azure
Una volta creato il gateway, sarà necessario ottenere l'indirizzo IP del peer BGP nel gateway VPN di Azure. Questo indirizzo è necessario per configurare il gateway VPN di Azure come peer BGP per i dispositivi VPN locali.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

L'ultimo comando visualizzerà le configurazioni BGP corrispondenti nel gateway VPN di Azure, ad esempio:

```powershell
    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }
```

Una volta creato il gateway, è possibile usarlo per stabilire una connessione cross-premise o una connessione da rete virtuale a rete virtuale con BGP. Le sezioni seguenti illustrano i passaggi per completare l'esercizio.

## <a name ="crossprembbgp"></a>Parte 2: Stabilire una connessione cross-premise con BGP
Per stabilire una connessione cross-premise, è necessario creare un gateway di rete locale per rappresentare il dispositivo VPN locale e una connessione per connettere il gateway VPN di Azure al gateway di rete locale. La differenza tra le istruzioni in questo articolo è costituita dalle proprietà aggiuntive necessarie per specificare i parametri di configurazione BGP.

![BGP per la connessione cross-premise](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Prima di procedere, verificare di avere completato la [Parte 1](#enablebgp) di questo esercizio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passaggio 1: Creare e configurare il gateway di rete locale
#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Questo esercizio continuerà a creare la configurazione illustrata nel diagramma. Sostituire i valori con quelli desiderati per la propria configurazione.

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
* Si ricordi che è necessario usare ASN BGP diversi nelle reti locali e nella rete virtuale di Azure. Se sono gli stessi, è necessario modificare l'ASN della rete virtuale se il dispositivo VPN locale usa già l'ASN per il peer con altri vicini BGP.

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
In questo passaggio si creerà la connessione da TestVNet1 a Site5. È necessario specificare "-EnableBgp $True" per abilitare BGP per questa connessione. Come illustrato in precedenza, è possibile avere connessioni sia BGP che non BGP per lo stesso gateway VPN di Azure. A meno che BGP non venga abilitato nella proprietà della connessione, Azure non abiliterà BGP per questa connessione nemmeno se i parametri BGP sono già configurati in entrambi i gateway.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```


L'esempio seguente elenca i parametri che si immetteranno nella sezione della configurazione BGP nel dispositivo VPN locale per questo esercizio:

    - Site5 ASN            : 65050
    - Site5 BGP IP         : 10.52.255.254
    - Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
    - Azure VNet ASN       : 65010
    - Azure VNet BGP IP    : 10.12.255.30
    - Static routes        : Destination 10.12.255.30/32, nexthop the VPN tunnel interface to 40.112.190.5                        Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
    - eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

La connessione verrà stabilita in pochi minuti e la sessione di peering BGP verrà avviata una volta stabilità la connessione IPSec.

## <a name ="v2vbgp"></a>Parte 3: Stabilire una connessione da rete virtuale a rete virtuale con BGP
Questa sezione aggiunge una connessione da rete virtuale a rete virtuale con BGP, come illustrato nel diagramma seguente. 

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Le istruzioni seguenti sono la prosecuzione dei passaggi precedenti. È necessario completare la [Parte I](#enablebgp) per creare e configurare TestVNet1 e il gateway VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passaggio 1: Creare TestVNet2 e il gateway VPN
È importante verificare che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet2, non si sovrapponga ad altri di intervalli di rete virtuale.

In questo esempio le reti virtuali appartengono alla stessa sottoscrizione. È possibile configurare connessioni da rete virtuale a rete virtuale tra sottoscrizioni diverse. Per altri dettagli, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md). Assicurarsi di aggiungere "-EnableBgp $True" quando si creano le connessioni per abilitare BGP.

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
Richiedere un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Si definiranno anche le configurazioni subnet e IP necessarie.

```powershell
$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Creare il gateway VPN con il numero AS. Si noti che è necessario eseguire l'override dell'ASN predefinito nei gateway VPN di Azure. Gli ASN per le reti virtuali connesse devono essere diversi per abilitare BGP e il routing di transito.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passaggio 2: Connettere i gateway TestVNet1 e TestVNet2
In questo esempio entrambi i gateway sono nella stessa sottoscrizione. È possibile completare questo passaggio nella stessa sessione di PowerShell.

#### <a name="1-get-both-gateways"></a>1. Ottenere entrambi i gateway
Eseguire l'accesso e connettersi a Sottoscrizione 1.

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

Dopo avere completato questi passaggi, la connessione verrà stabilita in pochi minuti e la sessione di peering BGP sarà attiva una volta completata la connessione da rete virtuale e rete virtuale.

Se sono state completate tutte e tre le parti di questo esercizio, risulterà stabilita una topologia di rete come la seguente:

![BGP per la connessione da rete virtuale a rete virtuale](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .


