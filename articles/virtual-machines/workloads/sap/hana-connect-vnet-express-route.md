---
title: Connettività configurata dalla rete virtuale al SAP HANA in Azure (istanze large) | Microsoft Docs
description: Connettività configurata dalla rete virtuale per l'uso di SAP HANA in Azure (istanze large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 547640ab1a6dd948cf5d17279d784e1b4a37b35e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101243"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Collegare una rete virtuale alle istanze Large di HANA

Dopo aver creato una rete virtuale di Azure, è possibile connettere la rete a SAP HANA in istanze Large di Azure. Creare un gateway ExpressRoute di Azure nella rete virtuale. Questo gateway consente di collegare la rete virtuale al circuito ExpressRoute che si connette al tenant del cliente sul timbro di istanze large di HANA.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti. Il nuovo gateway viene creato nella sottoscrizione di Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se esiste già un gateway, controllare che sia un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, eliminare il gateway e ricrearlo come gateway ExpressRoute. Se è già stato definito un gateway ExpressRoute, vedere la sezione seguente di questo articolo: "Collegare reti virtuali". 

- Usare il [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se si usa PowerShell, è necessario prima scaricare e usare la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
I seguenti comandi creano un gateway ExpressRoute. I testi preceduti da un _$_ sono variabili definite dall'utente che devono essere aggiornate con le informazioni specifiche.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In questo esempio è stato usato lo SKU del gateway HighPerformance. Le opzioni disponibili sono HighPerformance o UltraPerformance e sono le uniche SKU del gateway supportate per SAP HANA in Azure (istanze Large).

> [!IMPORTANT]
> Per le SKU Classe di tipo II delle istanze Large di HANA, è necessario usare UltraPerformance Gateway SKU.

## <a name="link-virtual-networks"></a>Collegare reti virtuali

La rete virtuale di Azure dispone ora di un gateway ExpressRoute. Usare le informazioni di autorizzazione fornite da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute SAP HANA in istanze Large. Questa operazione può essere eseguita nel portale di Azure o con PowerShell. Di seguito sono riportate le istruzioni di PowerShell. 

Eseguire i comandi seguenti per ogni gateway ExpressRoute usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Il codice AuthGUID, inoltre, è specifico per ogni rete virtuale e il relativo gateway. Se si vuole aggiungere un'altra rete virtuale di Azure, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette le istanze large di HANA in Azure da Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> L'ultimo parametro nel comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** è un nuovo parametro che Abilita il percorso veloce ExpressRoute. Funzionalità che riduce la latenza di rete tra le unità di istanze large di HANA e le VM di Azure. La funzionalità è stata aggiunta nel 2019 maggio. Per altri dettagli, vedere l'articolo [SAP Hana architettura di rete (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Assicurarsi di eseguire la versione più recente dei cmdlet di PowerShell prima di eseguire i comandi.

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Applicazione del percorso rapido ExpressRoute ai circuiti ExpressRoute di istanze large di HANA esistenti
La documentazione ha illustrato finora come connettere un nuovo circuito ExpressRoute creato con una distribuzione di istanze large di HANA a un gateway ExpressRoute di Azure di una delle reti virtuali di Azure. Tuttavia, molti clienti hanno già installato i circuiti ExpressRoute e le reti virtuali sono già connesse alle istanze large di HANA. Poiché il nuovo percorso rapido ExpressRoute riduce la latenza di rete, è consigliabile applicare la modifica per usare questa funzionalità. I comandi per connettere un nuovo circuito ExpreesRoute e per modificare un circuito ExpressRoute esistente sono gli stessi. Di conseguenza, è necessario eseguire questa sequenza di comandi di PowerShell per modificare un circuito esistente da usare 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

È importante aggiungere l'ultimo parametro visualizzato sopra per abilitare la funzionalità del percorso rapido ExpressRoute


## <a name="expressroute-global-reach"></a>Copertura globale ExpressRoute
Quando si desidera abilitare Copertura globale per uno o entrambi i due scenari:

 - Replica di sistema HANA senza proxy o firewall aggiuntivi
 - Copia di backup tra unità di istanze large di HANA in due aree diverse per eseguire copie di sistema o aggiornamenti del sistema

è necessario considerare quanto segue:

- È necessario specificare un intervallo di spazio degli indirizzi di uno spazio di indirizzi/29. Tale intervallo di indirizzi non può sovrapporsi ad altri intervalli di spazio degli indirizzi usati finora per la connessione di istanze large di HANA ad Azure e non può sovrapporsi ad altri intervalli di indirizzi IP usati altrove in Azure o in locale.
- Esiste una limitazione per il ASN (numero sistema autonomo) che può essere usato per annunciare le route locali alle istanze large di HANA. Il sito locale non deve annunciare alcuna route con ASN privato nell'intervallo 65000 – 65020 o 65515. 
- Per lo scenario di connessione dell'accesso diretto locale alle istanze large di HANA, è necessario calcolare una tariffa per il circuito che connette ad Azure. Per i prezzi, verificare i prezzi per [copertura globale componente](https://azure.microsoft.com/pricing/details/expressroute/)aggiuntivo.

Per ottenere uno o entrambi gli scenari applicati alla distribuzione, aprire un messaggio di supporto con Azure come descritto in [aprire una richiesta di supporto per le istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

I dati necessari e le parole chiave che è necessario usare per consentire a Microsoft di eseguire il routing e l'esecuzione sulla richiesta sono simili ai seguenti:

- Servizio: Istanza Large di SAP Hana
- Tipo di problema: Installazione e configurazione
- Sottotipo del problema: Il problema non è incluso in questo elenco
- Oggetto ' modifica la rete-Aggiungi Copertura globale '
- Dettagli: ' Aggiungi Copertura globale a un'istanza large di HANA al tenant di istanze large di HANA o ' Aggiungi Copertura globale al tenant di istanze large di Hana in locale.
- Ulteriori dettagli per l'istanza large di HANA in caso di tenant di istanze large di HANA: È necessario definire le **due aree di Azure** in cui si trovano i due tenant per la connessione **ed** è necessario inviare l' **intervallo di indirizzi IP/29**
- Ulteriori dettagli per il caso tenant in istanze large di HANA in locale: È necessario definire l' **area di Azure** in cui viene distribuito il tenant di istanze large di Hana a cui si vuole connettersi direttamente. Inoltre, è necessario fornire il **GUID di autenticazione** e l' **ID del peer del circuito** ricevuti quando è stato stabilito il circuito ExpressRoute tra l'ambiente locale e Azure. Inoltre, è necessario assegnare un nome all' **ASN**. L'ultimo risultato finale è un **intervallo di indirizzi IP/29** per ExpressRoute copertura globale.

> [!NOTE]
> Per gestire entrambi i casi, è necessario fornire due diversi intervalli di indirizzi IP che non si sovrappongono a nessun altro intervallo di indirizzi IP usato finora. 




## <a name="next-steps"></a>Passaggi successivi

- [Requisiti aggiuntivi della rete per HLI](hana-additional-network-requirements.md)
