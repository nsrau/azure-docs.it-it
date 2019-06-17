---
title: Connettività configurata dalla rete virtuale per SAP HANA in Azure (istanze large) | Microsoft Docs
description: Connettività configurata dalla rete virtuale da usare SAP HANA in Azure (istanze large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239457"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Collegare una rete virtuale alle istanze Large di HANA

Dopo aver creato una rete virtuale di Azure, è possibile connettere la rete a SAP HANA in istanze Large di Azure. Creare un gateway ExpressRoute di Azure nella rete virtuale. Il gateway consente di collegare la rete virtuale al circuito ExpressRoute che si connette al tenant del cliente nel modulo per istanze Large di HANA.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti. Il nuovo gateway viene creato nella sottoscrizione di Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se esiste già un gateway, controllare che sia un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, eliminare il gateway e crearlo nuovamente come un gateway ExpressRoute. Se è già stato definito un gateway ExpressRoute, vedere la sezione seguente di questo articolo: "Collegare reti virtuali". 

- Usare il [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se si usa PowerShell, è necessario prima scaricare e usare la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
I seguenti comandi creano un gateway ExpressRoute. Il testo preceduto da un _$_ sono variabili definite dall'utente che devono essere aggiornate con le informazioni specifiche.

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

La rete virtuale di Azure dispone ora di un gateway ExpressRoute. Usare le informazioni di autorizzazione fornite da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute delle istanze Large di SAP HANA. Questa operazione può essere eseguita nel portale di Azure o con PowerShell. Di seguito sono riportate le istruzioni di PowerShell. 

Eseguire i comandi seguenti per ogni gateway ExpressRoute usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Il codice AuthGUID, inoltre, è specifico per ogni rete virtuale e il relativo gateway. Se si desidera aggiungere un'altra rete virtuale di Azure, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette istanze large di HANA in Azure da Microsoft. 

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
> L'ultimo parametro nel comando New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** è un nuovo parametro che consente a percorso rapido di ExpressRoute. Una funzionalità che consente di ridurre la latenza di rete tra le unità di istanze Large di HANA e macchine virtuali di Azure. Le funzionalità sono state aggiunte nella maggio 2019. Per altri dettagli, vedere l'articolo [architettura di rete di SAP HANA (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Assicurarsi di eseguire la versione più recente dei cmdlet di PowerShell prima di eseguire i comandi.

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Applicazione di percorso veloce ExpressRoute a circuiti ExpressRoute per istanze Large HANA esistenti
La documentazione finora è stato spiegato come connettersi a un nuovo circuito ExpressRoute creato con una distribuzione di istanze Large di HANA in un gateway ExpressRoute di Azure di una delle reti virtuali di Azure. Ma molti clienti hanno già la relativa impostazione di circuiti ExpressRoute già e hanno le proprie reti virtuali alle istanze Large di HANA già connessa. Come il percorso rapido nuove di ExpressRoute è riducendo la latenza di rete, si consiglia di applicare la modifica per usare questa funzionalità. I comandi per collegare un nuovo circuito ExpreesRoute e per modificare un circuito ExpressRoute esistente sono uguali. Di conseguenza è necessario eseguire questa sequenza di comandi di PowerShell per modificare un circuito esistente da usare 

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

È importante aggiungere l'ultimo parametro come visualizzato sopra per abilitare le funzionalità di ExpressRoute Fast percorso


## <a name="expressroute-global-reach"></a>Copertura globale di ExpressRoute
Come si desidera abilitare raggiungere globale per uno o entrambi i due scenari:

 - Replica di sistema HANA senza altri proxy o firewall
 - Copia i backup tra le unità di istanze Large di HANA in due aree diverse per eseguire copie system o gli aggiornamenti di sistema

è necessario considerare quanto segue:

- È necessario specificare un intervallo di indirizzi di/29 spazio degli indirizzi. Che intervallo di indirizzi non si sovrappongano con uno qualsiasi degli altri intervalli di indirizzi dello spazio utilizzato finora ci si connette istanze Large di HANA in Azure e non possono sovrapporsi con altri di intervalli di indirizzi IP è usata altrove in Azure o in locale.
- È una limitazione per i codici ASN (Autonomous System Number) che può essere utilizzato per annunciare le route locali alle istanze Large di HANA. On-premises necessario non annunciare le route con ASN privato nell'intervallo di 65000 – 65020 o 65515. 
- Per lo scenario di connessione in locale l'accesso diretto a istanze Large di HANA, è necessario calcolare una tariffa per il circuito che si connette ad Azure. Per i prezzi, vedere i prezzi per [componente aggiuntivo di raggiungere globale](https://azure.microsoft.com/pricing/details/expressroute/).

Per ottenere uno o entrambi gli scenari applicati alla distribuzione, inviare un messaggio all'assistenza con Azure come descritto in [aprire una richiesta di supporto per istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Dati necessari e le parole chiave che è necessario usare Microsoft per essere in grado di indirizzare ed eseguire su richiesta dell'utente, sarà simile a:

- Servizio: Istanza Large di SAP HANA
- Tipo di problema: Installazione e configurazione
- Sottotipo di problema: Il problema non è incluso in questo elenco
- Subject 'Modify mia rete - Aggiungi raggiungere globale'
- Dettagli: ' Aggiungi raggiungere globali per istanze Large di HANA per tenant per istanze Large di HANA o ' Aggiungi globale raggiungere a livello locale al tenant di istanze Large di HANA.
- Dettagli aggiuntivi per le istanze Large di HANA per istanze Large di HANA del tenant case: È necessario definire il **due aree di Azure** in cui si trovano i due tenant da connettere **AND** devi inviare la   **/29 intervallo di indirizzi IP**
- Dettagli aggiuntivi per il primo per istanze Large di HANA del tenant case: È necessario definire il **area di Azure** in cui viene distribuito il tenant di istanze Large di HANA si vuole accedere direttamente. Inoltre è necessario fornire il **GUID Auth** e **Peer Circuit ID** che è stato ricevuto al momento della definizione del circuito ExpressRoute tra locale e Azure. Inoltre, è necessario assegnare un nome di **ASN**. L'ultimo risultato finale è un **/29 intervallo di indirizzi IP** per raggiungere globale di ExpressRoute.

> [!NOTE]
> Se si desidera disporre di entrambi i casi gestiti, è necessario fornire due diverse/29 intervalli di indirizzi IP che non si sovrappongano con qualsiasi altro indirizzo IP indirizzo intervallo usato finora. 




## <a name="next-steps"></a>Passaggi successivi

- [Requisiti aggiuntivi della rete per HLI](hana-additional-network-requirements.md)
