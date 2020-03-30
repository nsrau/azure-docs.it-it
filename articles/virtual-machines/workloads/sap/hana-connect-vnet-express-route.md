---
title: Configurazione della connettività dalla rete virtuale a SAP HANA in Azure (istanze di grandi dimensioni) Documenti Microsoft
description: Configurazione della connettività dalla rete virtuale per l'uso di SAP HANA in Azure (istanze di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617193"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Collegare una rete virtuale alle istanze Large di HANA

Dopo aver creato una rete virtuale di Azure, è possibile connettere la rete a SAP HANA in istanze Large di Azure. Creare un gateway ExpressRoute di Azure nella rete virtuale. Questo gateway consente di collegare la rete virtuale al circuito ExpressRoute che si connette al tenant del cliente nel timbro Istanza di grandi dimensioni HANA.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti. Il nuovo gateway viene creato nella sottoscrizione di Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se esiste già un gateway, controllare che sia un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, eliminare il gateway e ricrearlo come gateway ExpressRoute.If it is not an ExpressRoute gateway, delete the gateway, and re-create it as an ExpressRoute gateway. Se è già stato definito un gateway ExpressRoute, vedere la sezione seguente di questo articolo: "Collegare reti virtuali". 

- Usare il [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **gateway di rete virtuale**e quindi selezionare **ExpressRoute** come tipo di gateway.
  - Se si usa PowerShell, è necessario prima scaricare e usare la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
I seguenti comandi creano un gateway ExpressRoute. I testi preceduti _$_ da un sono variabili definite dall'utente che devono essere aggiornate con le informazioni specifiche.

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

La rete virtuale di Azure dispone ora di un gateway ExpressRoute. Usare le informazioni di autorizzazione fornite da Microsoft per connettere il gateway ExpressRoute al circuito SAP HANA Large Instances ExpressRoute. Questa operazione può essere eseguita nel portale di Azure o con PowerShell. Le istruzioni di PowerShell sono le seguenti. 

Eseguire i comandi seguenti per ogni gateway ExpressRoute usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Il codice AuthGUID, inoltre, è specifico per ogni rete virtuale e il relativo gateway. Se si vuole aggiungere un'altra rete virtuale di Azure, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette le istanze di grandi dimensioni HANA in Azure da Microsoft.If you want to add another Azure virtual network, you need to get another AuthID for your ExpressRoute circuit that connects HANA large instances into Azure from Microsoft. 

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
> L'ultimo parametro nel comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** è un nuovo parametro che abilita ExpressRoute Fast Path. Funzionalità che riduce la latenza di rete tra le unità DI istanza di grandi dimensioni HANA e le macchine virtuali di Azure.A functionality that reduces network latency between your HANA Large Instance units and Azure VMs. La funzionalità è stata aggiunta nel maggio 2019. Per ulteriori informazioni, vedere l'articolo Architettura di [rete SAP HANA (Large Instances).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Assicurarsi di eseguire la versione più recente dei cmdlet di PowerShell prima di eseguire i comandi.

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Applicazione di ExpressRoute Fast Path ai circuiti HANA Large Instance ExpressRoute esistentiApplying ExpressRoute Fast Path to existing HANA Large Instance ExpressRoute circuits
La documentazione finora è stata illustrata come connettere un nuovo circuito ExpressRoute creato con una distribuzione di un'istanza di grandi dimensioni HANA a un gateway ExpressRoute di una delle reti virtuali di Azure.The documentation finora explained how to connect a new ExpressRoute circuit that got created with a HANA Large Instance deployment to an Azure ExpressRoute gateway of one of your Azure virtual networks. Tuttavia, molti clienti hanno già configurato i circuiti ExpressRoute e le reti virtuali sono già connesse a istanze di grandi dimensioni HANA. Poiché il nuovo percorso rapido ExpressRoute riduce la latenza di rete, è consigliabile applicare la modifica per usare questa funzionalità. I comandi per connettere un nuovo circuito ExpreesRoute e per modificare un circuito ExpressRoute esistente sono gli stessi. Di conseguenza, è necessario eseguire questa sequenza di comandi di PowerShell per modificare un circuito esistente da usare 

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

È importante aggiungere l'ultimo parametro visualizzato in precedenza per abilitare la funzionalità Percorso rapido ExpressRoute


## <a name="expressroute-global-reach"></a>Copertura globale di ExpressRoute
Come si desidera abilitare la copertura globale per uno o entrambi i due scenari:

 - Replica del sistema HANA senza proxy o firewall aggiuntivi
 - Copia di backup tra unità di istanza di grandi dimensioni HANA in due aree diverse per eseguire copie di sistema o aggiornamenti di sistema

è necessario considerare che:

- È necessario fornire un intervallo di indirizzi di uno spazio di indirizzi /29. Tale intervallo di indirizzi potrebbe non sovrapporsi a nessuno degli altri intervalli di spazi di indirizzi usati finora che hanno usato finora la connessione di istanze di grandi dimensioni HANA ad Azure e non possono sovrapporsi a nessuno degli intervalli di indirizzi IP usati in un'altra posizione in Azure o in locale.
- Esiste una limitazione degli ASN (Numero di sistema autonomo) che può essere utilizzata per annunciare le route locali a istanze di grandi dimensioni HANA. L'utente locale non deve annunciare route con ASN privati nell'intervallo da 65000 – 65020 o 65515. 
- For the scenario of connecting on-premises direct access to HANA Large instances, you need to calculate a fee for the circuit that connects you to Azure. Per i prezzi, controlla i prezzi per [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Per ottenere uno o entrambi gli scenari applicati alla distribuzione, aprire un messaggio di supporto con Azure come descritto in Aprire una richiesta di [supporto per istanze HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances) di grandi dimensioniTo get one or both of the scenarios applied to your deployment, open a support message with Azure as described in Open a support request for HANA large Instances

I dati necessari e le parole chiave che è necessario utilizzare per Microsoft per essere in grado di instradare ed eseguire sulla richiesta, si presenta come:

- Servizio: istanza di grandi dimensioni SAP HANAService: SAP HANA Large Instance
- Tipo di problema: Configurazione e configurazione
- Sottotipo di problema: il problema non è elencato sopra
- Oggetto 'Modifica rete - aggiungi copertura globale'
- Dettagli: 'Aggiungi copertura globale a un'istanza di grandi dimensioni HANA al tenant di istanza di grandi dimensioni HANA o 'Aggiungi copertura globale a tenant di istanza di grandi dimensioni HANA.
- Ulteriori dettagli per il caso del tenant di HANA Large Instance to HANA Large Instance: È necessario definire le **due aree** di Azure in cui si trovano i due tenant da connettere **E** è necessario inviare l'intervallo di **indirizzi IP /29**
- Dettagli aggiuntivi per il caso del tenant da locale a istanza di grandi dimensioni HANA: è necessario definire l'area di **Azure** in cui viene distribuito il tenant dell'istanza di grandi dimensioni HANA a cui si vuole connettersi direttamente. Inoltre, è necessario fornire il GUID di **autenticazione** e **l'ID peer del circuito** ricevuti quando è stato stabilito il circuito ExpressRoute tra l'ambiente locale e Azure. Inoltre, è necessario denominare il proprio **ASN**. L'ultimo risultato finale è un intervallo di indirizzi IP /29 per la copertura globale ExpressRoute.The last deliverable is a **/29 IP address range** for ExpressRoute Global Reach.

> [!NOTE]
> Se si desidera che entrambi i casi vengano gestiti, è necessario fornire due diversi intervalli di indirizzi IP /29 che non si sovrappongono ad altri intervalli di indirizzi IP utilizzati finora. 




## <a name="next-steps"></a>Passaggi successivi

- [Requisiti aggiuntivi della rete per HLI](hana-additional-network-requirements.md)
