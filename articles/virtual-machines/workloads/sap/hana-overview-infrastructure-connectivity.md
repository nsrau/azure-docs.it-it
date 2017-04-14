---
title: "Infrastruttura e connettività a SAP HANA in Azure (istanze grandi) | Microsoft Docs"
description: "Configurare l&quot;infrastruttura di connettività necessaria per l&quot;uso di SAP HANA in Azure (istanze grandi)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0c87a162eb478aecf207738fd6c9515aa515a847
ms.lasthandoff: 04/03/2017


---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure 

Dopo aver finalizzato l'acquisto di SAP HANA in Azure (istanze grandi) con il team Microsoft dedicato agli account aziendali, è necessario specificare le seguenti informazioni:

- Nome del cliente
- Informazioni di contatto aziendali (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto tecniche (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto di rete tecniche (inclusi indirizzo e-mail e numero di telefono)
- Area di distribuzione di Azure (Stati Uniti occidentali o Stati Uniti orientali a partire da settembre 2016)
- Confermare l'SKU (configurazione) di SAP HANA in Azure (istanze grandi)
- Per ciascuna area di Azure in cui viene distribuito:
  - Un intervallo di indirizzi IP /29 per connessioni P2P
  - Un blocco CIDR (usato per il pool NAT delle istanze HANA di grandi dimensioni; /24 consigliati)
- Per ogni rete virtuale di Azure che si connette alle istanze HANA di grandi dimensioni, a prescindere dall'area di Azure:
  - Uno op iù intervalli di indirizzi IP /28s o /27 (per il subnet del gateway della rete virtual del cliente)
  - Uno o più blocchi CIDR (per il subnet del tenant della rete virtuale del cliente; /24 consigliati)
- Dati per ogni sistema di istanze HANA di grandi dimensioni:
  - Nome host desiderato
  - Indirizzo IP desiderato dal pool di NAT
- Numero di sottoscrizione di Azure per la sottoscrizione a cui SAP HANA in Azure (istanze grandi) verrà connesso direttamente
- Il nome SID di SAP HANA per l'istanza di SAP HANA (necessario per creare i volumi disco richiesti legati a SAP HANA)

Dopo aver specificato le informazioni, Microsoft esegue il provisioning di SAP HAN in Azure (istanze grandi).

A quel punto si riceveranno informazioni di configurazione di rete per:

- Connettere le reti virtuali di Azure al circuito ExpressRoute che collega le reti virtuali alle istanze HANA di grandi dimensioni
  - Per Azure Resource Manager:
     - Chiave/i di autorizzazione
     - PeerID ExpressRoute
- Accedere a istanze HANA di grandi dimensioni con il circuito ExpressRoute e la rete virtuale di Azure configurati

## <a name="creating-an-azure-vnet"></a>Creazione di una rete virtuale di Azure

Questa rete virtuale di Azure deve essere creata usando il modello di distribuzione Azure Resource Manager. Il vecchio modello di distribuzione di Azure, comunemente noto come ASM, non è supportato per questa soluzione.

La rete virtuale di Azure creata deve disporre di almeno una subnet del tenant e una subnet del gateway. Alle subnet devono essere assegnati gli intervalli di indirizzi IP specificati e inviati a Microsoft.

> [!IMPORTANT] 
> Assegnare solo i blocchi di indirizzi del tenant e del gateway alla rete virtuale nella sottoscrizione di Azure. I blocchi degli indirizzi P2P e del pool NAT devono essere separati dagli spazi degli indirizzi della rete virtuale e della subnet, in quanto esistono al di fuori della sottoscrizione di Azure.

Possono essere usate più subnet del tenant (anche con intervalli di indirizzi non contigui) ma, come indicato in precedenza, tali intervalli devono essere inviati preventivamente a Microsoft.

È possibile usare qualsiasi standard di denominazione per le subnet del tenant. Tuttavia, **è fondamentale che ci sia sempre una sola subnet del gateway per ciasuna rete virtuale** per la connessione al circuito ExpressRoute di SAP HANA in Azure (istanze grandi). **Questa subnet del gateway dovrà chiamarsi &quot;GatewaySubnet&quot;** per assicurare un posizionamento appropriato del gateway di ExpressRoute.

> [!WARNING] 
> È fondamentale che la subnet del gateway sia sempre denominata &quot;GatewaySubnet&quot;.

La rete virtuale può essere creata mediante il portale di Azure, PowerShell, il modello di Azure o l'interfaccia della riga di comando di Azure (vedere [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="creating-a-gateway-subnet"></a>Creazione di una subnet del gateway

Una volta creata la rete virtuale di Azure, è necessario creare un gateway ExpressRoute sulla rete per collegarla al circuito ExpressRoute che collega il tenant del cliente allo stamp di istanze di grandi dimensioni.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti; il nuovo gateway viene creato nella sottoscrizione Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

Se esiste già un gateway, controllare se sia o meno un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, deve essere eliminato e ricreato come tale. Se è già presente un gateway ExpressRoute, poiché la rete virtuale di Azure è già connessa al circuito ExpressRoute per la connettività locale, passare alla sezione Collegamento di reti virtuali di seguito.

- Usare il (nuovo) [portale di Azure](https://portal.azure.com/) o Powershell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se, invece, si usa PowerShell, scaricare e usare innanzitutto la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) per garantire un'esperienza ottimale. I seguenti comandi creano un gateway ExpressRoute. Il testo preceduto da un _$_ consiste in variabili definite dall'utente, da aggiornare in base alle proprie informazioni specifiche.

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In questo esempio, è stato usato l'SKU del gateway HighPerformance. Le opzioni disponibili sono HighPerformance o UltraPerformance, trattasi degli uniti SKU del gateway supportati per SAP HANA in Azure (istanze grandi).

## <a name="linking-vnets"></a>Collegamento delle reti virtuali

Ora che la rete virtuale di Azure dispone di un gateway ExpressRoute, usare le informazioni di autorizzazione ottenute da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute di SAP HANA in Azure (istanze grandi) creato per questa connettività. Questa operazione può essere eseguita solo con PowerShell (non è attualmente supportata tramite il portale di Azure).

- Eseguire le operazioni seguenti per ogni gateway di rete virtuale usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate di seguito provengono dalle informazioni ottenute da Microsoft. Inoltre, AuthGUID è specifico per ogni rete virtuale e il relativo gateway.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Potrebbe essere necessario eseguire più volte questo passaggio se si desidera collegare il gateway a più circuiti ExpressRoute diversi associati alla sottoscrizione.

## <a name="adding-more-ip-addresses-or-subnets"></a>Aggiunta di più indirizzi IP o subnet

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per aggiungere più indirizzi IP o subnet.

Se l'intervallo di spazio degli indirizzi IP aggiuntivi non è stato ancora dichiarato con SAP HANA nella gestione del servizio Azure, aprire una richiesta di supporto di Azure per aggiungerlo. Dopo aver ricevuto la conferma, eseguire i passaggi successivi.

Per creare una subnet aggiuntiva dal portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json); per creare da PowerShell, vedere [Creare una rete virtuale usando PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Aggiunta di reti virtuali

Dopo aver collegato inizialmente una o più reti virtuali Azure, si potrebbe voler aggiungerne di ulteriori in grado di accedere a SAP HANA in Azure (istanze grandi). In primo luogo, inviare una richiesta di supporto di Azure includendo le informazioni specifiche sulla distribuzione di Azure in questione e gli intervalli di spazi degli indirizzi IP per la/le subnet del tenant e del gateway delle reti virtuali Azure aggiuntive. SAP HANA nella gestione del servizio Azure indica a quel punto le informazioni necessarie per connettere le reti virtuali aggiuntive ed ExpressRoute.

Procedura per aggiungere una nuova rete virtuale di Azure:

1. Completare il primo passaggio nel processo introduttivo, vedere la sezione _Creazione di una rete virtuale di Azure_ precedente.
2. Completare il secondo passaggio nel processo introduttivo, vedere la sezione _Creazione di una subnet del gateway_ precedente.
3. Aprire una richiesta di supporto di Azure con informazioni sulla nuova rete virtuale e richiedere una nuova chiave di autorizzazione per connettere le reti virtuali aggiuntive al circuito ExpressRoute delle istanze HANA di grandi dimensioni.
4. Una volta ricevuta la notifica di completamento dell'autorizzazione, usare le informazioni di autorizzazioni ottenute da Microsoft per completare il terzo passaggio nel processo introduttivo, vedere la sezione _Collegamento delle reti virtuali_ precedente.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento della larghezza di banda del circuito ExpressRoute

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene consigliato di aumentare la larghezza di banda del circuito ExpressRoute di SAP HANA in Azure (istanze grandi), creare una richiesta di supporto di Azure. (è possibile richiedere un aumento per una larghezza di banda a circuito singolo fino a un massimo di 10 Gbps). Si riceverà la notifica al termine dell'operazione; non sono necessarie azioni aggiuntive per abilitare la velocità più elevata in Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Aggiunta di un circuito ExpressRoute aggiuntivo

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene comunicata la necessità di un circuito ExpressRoute aggiuntivo, inviare una richiesta di supporto di Azure per creare un nuovo circuito ExpressRoute (e per ottenere le informazioni di autorizzazione necessarie per la connessione). È necessario definire lo spazio degli indirizzi che verrà usato sulle reti virtuali prima di inviare la richiesta, al fine di ricevere l'autorizzazione dal team di gestione dei servizi di SAP HANA in Azure.

Una volta creato il nuovo circuito e completata la configurazione da parte del team di gestione dei servizi di SAP HANA in Azure, si riceverà una notifica contenente le informazioni necessarie per continuare. Seguire i passaggi descritti in precedenza per creare e collegare la nuova rete virtuale a questo circuito aggiuntivo. Non sarà possibile connettere reti virtuali di Azure a questo circuito aggiuntivo se sono già connesse a un altro circuito ExpressRoute.

## <a name="deleting-a-subnet"></a>Eliminazione di una subnet

Per rimuovere una subnet di rete virtuale, è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando. Se viene rimosso uno spazio degli indirizzi, è consigliabile informare il team di gestione dei servizi di SAP HANA in Azure riguardo alla modifica, per rimuoverlo anche dagli intervalli con cui SAP HANA in Azure (istanze grandi) è autorizzato a comunicare.

Azure.com non contiene ancora linee guida specifiche su come rimuovere le subnet; tuttavia, la procedura di rimozione è opposta a quella di aggiunta. Vedere l'articolo [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) sul portale di Azure per ulteriori informazioni sulla creazione di subnet.

## <a name="deleting-a-vnet"></a>Eliminazione di una rete virtuale

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per eliminare una rete virtuale. Il team di gestione dei servizi di SAP HANA in Azure rimuove le autorizzazioni esistenti sul circuito ExpressRoute di SAP HANA in Azure (istanze grandi) e gli intervalli di indirizzi IP (tenant e gateway) per la comunicazione con HANA (istanze grandi).

Una volta rimossa la rete virtuale, aprire una richiesta di supporto di Azure per indicare gli intervalli degli spazi di indirizzi IP da rimuovere.

Azure.com non contiene ancora linee guida specifiche su come rimuovere le reti virtuali; tuttavia, la procedura di rimozione è opposta a quella di aggiunta descritta in precedenza. Vedere gli articoli [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Creare una rete virtuale usando PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per ulteriori informazioni sulla creazione di reti virtuali.

Per garantire una rimozione completa, rimuovere gli elementi seguenti:

- **Per Azure Resource Manager:** la connessione ExpressRoute, il gateway della rete virtuale, l'indirizzo IP pubblico del gateway della rete virtuale e la rete virtuale
- **Per le macchina virtuali classiche:** il gateway della rete virtuale e la rete virtuale

## <a name="deleting-an-expressroute-circuit"></a>Eliminazione di un circuito ExpressRoute

Per rimuovere un circuito ExpressRoute aggiuntivo di SAP HANA in Azure (istanze grandi), aprire una richiesta di supporto di Azure con SAP HANA nella gestione del servizio Azure e richiedere l'eliminazione del circuito. È possibile eliminare o mantenere la rete virtuale nella sottoscrizione di Azure in base alle necessità. Tuttavia, è necessario eliminare (con Azure Resource Manager) o scollegare (con macchine virtuali classiche) la connessione tra il circuito ExpressRoute delle istanze HANA di grandi dimensioni e il gateway della rete virtuale.

Se si desidera rimuovere una rete virtuale, seguire le linee guida indicate in Eliminazione di una rete virtuale nella sezione precedente.



