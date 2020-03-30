---
title: Configurare e convalidare le connessioni di rete virtuale o VPN
description: Istruzioni dettagliate per configurare e convalidare varie distribuzioni di reti VPN e virtuali di AzureStep-by-step guidance to configure and validate various Azure VPN and virtual network deployments
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099066"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configurare e convalidare le connessioni di rete virtuale o VPN

Questa procedura dettagliata fornisce istruzioni dettagliate per configurare e convalidare varie distribuzioni di VPN e reti virtuali di Azure.This walkthrough provides step-by-step guidance to configure and validate various Azure VPN and virtual network deployments. Gli scenari includono il routing di transito, le connessioni da rete a rete, il protocollo BGP (Border Gateway Protocol), le connessioni multisito e le connessioni da punto a sito.

Azure VPN gateways enable flexibility in arranging almost any kind of connected virtual network topology in Azure. Ad esempio, è possibile connettere reti virtuali:For example, you can connect virtual networks:

- In tutte le regioni.
- Tra tipi di rete virtuale (Azure Resource Manager e classico).
- All'interno di Azure o all'interno di un ambiente ibrido locale.
- In diversi abbonamenti. 

## <a name="network-to-network-vpn-connection"></a>Connessione VPN da rete a rete

La connessione di una rete virtuale a un'altra rete virtuale (da rete a rete) tramite VPN è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec e IKE. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse.

![Connessione da rete a rete con IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se le reti virtuali si trovano nella stessa area, è consigliabile connetterle usando il peering della rete virtuale. Il peering della rete virtuale non usa un gateway VPN. Aumenta la velocità effettiva e diminuisce la latenza. Per configurare una connessione peering di rete virtuale, selezionare **Configura e convalida peering rete virtuale**.

Se le reti virtuali sono state create tramite il modello di distribuzione di Azure Resource Manager, selezionare Configura e convalida una rete virtuale di **Resource Manager in una connessione a rete virtuale** di Resource Manager per configurare una connessione VPN.

Se una delle reti virtuali è stata creata tramite il modello di distribuzione classica di Azure e l'altra è stata creata tramite Resource Manager, selezionare **Configura e convalida una rete virtuale classica in una connessione a una** rete di reti virtuali di Resource Manager per configurare una connessione VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configurare il peering della rete virtuale per due reti virtuali nella stessa areaConfigure virtual network peering for two virtual networks in the same region

Prima di iniziare a implementare e configurare il peering della rete virtuale di Azure, verificare che siano soddisfatti i prerequisiti seguenti:Before you start to implement and configure Azure virtual network peering, make sure that you meet the following prerequisites:

* Le reti virtuali con peering devono trovarsi nella stessa area di Azure.
* Le reti virtuali con peered devono avere spazi di indirizzi IP che non si sovrappongono.
* Il peering viene eseguito tra due reti virtuali Non esiste una relazione transitiva derivata tra i peering. Ad esempio, se VNetA è sottoposto a peered con VNetB e VNetB è sottoposto a peered con VNetC, VNetA *non* è sottoposto a peered con VNetC.

Quando si soddisfano i requisiti, è possibile seguire [esercitazione: Connettere reti virtuali con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) di rete virtuale usando il portale di Azure per creare e configurare il peering.

Per controllare la configurazione del peering, utilizzare il metodo seguente:To check the peering configuration, use the following method:

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account con i [ruoli e le autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. Nella finestra che contiene il testo **Cerca risorse**, nella parte superiore del portale, digitare **reti virtuali**. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** visualizzato selezionare la rete virtuale per cui si vuole creare un peering.
4. Nel riquadro visualizzato per la rete virtuale selezionare Peerings nella sezione **Impostazioni.In** the pane that appears for the virtual network, select **Peerings** in the Settings section.
5. Selezionare un peering e visualizzare i risultati della configurazione.

![Selezioni per il controllo della configurazione del peering della rete virtuale](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Per Azure PowerShell, eseguire il comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) per ottenere il peering della rete virtuale. Ad esempio:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Connettere una rete virtuale di Resource Manager a un'altra rete virtuale di Resource ManagerConnect a Resource Manager virtual network to another Resource Manager virtual network

È possibile configurare direttamente una connessione da una rete virtuale di Resource Manager a un'altra rete virtuale di Resource Manager.You can configure a connection from one Resource Manager virtual network to another Resource Manager virtual network directly. In alternativa, è possibile configurare la connessione utilizzando IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configurare una connessione VPN tra reti virtuali di Resource ManagerConfigure a VPN connection between Resource Manager virtual networks

Per configurare una connessione tra reti virtuali di Resource Manager senza IPsec, vedere Configurare una connessione gateway VPN da [rete a rete tramite il portale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)di Azure.

Per configurare una connessione con IPsec tra due reti virtuali di Resource Manager, seguire i passaggi da 1 a 5 in [Creare una connessione da sito a sito nel portale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) di Azure per ogni rete virtuale.

> [!Note]
> Questi passaggi funzionano solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali sono in sottoscrizioni diverse, è necessario usare PowerShell per effettuare la connessione. Vedere l'articolo su [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Convalidare la connessione VPN tra le reti virtuali di Resource ManagerValidate the VPN connection between Resource Manager virtual networks

![Connessione classica della rete virtuale a una rete virtuale di Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Per verificare che la connessione VPN sia configurata correttamente, seguire queste istruzioni.

> [!Note] 
> I numeri dopo i componenti della rete virtuale in questi passaggi corrispondono ai numeri nel diagramma precedente.

1. Assicurarsi che non vi siano spazi di indirizzi sovrapposti nelle reti virtuali connesse.
2. Verificare che l'intervallo di indirizzi per la rete virtuale di Azure Resource Manager (1) sia definito in modo accurato nell'istanza **dell'oggetto Connessione** (4).
3. Verificare che l'intervallo di indirizzi per la rete virtuale di Azure Resource Manager (6) sia definito in modo accurato nell'istanza **dell'oggetto connessione** (3).
4. Verificare che le chiavi precondivise corrispondano agli oggetti connessione.
5. Verificare che l'indirizzo VIP del gateway di rete virtuale di Azure Resource Manager sia definito in modo accurato nell'istanza **dell'oggetto Connessione** (4).
6. Verificare che l'indirizzo VIP (5) del gateway di rete virtuale di Azure Resource Manager sia definito in modo accurato nell'istanza **dell'oggetto Connessione** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Connettere una rete virtuale classica a una rete virtuale di Resource ManagerConnect a classic virtual network to a Resource Manager virtual network

È possibile creare una connessione tra reti virtuali che si trovano in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che dispongono già di connessioni a reti locali, purché il tipo di gateway sia stato configurato come basato su route.

Per configurare una connessione tra una rete virtuale classica e una rete virtuale di Resource Manager, vedere [Connettere reti virtuali da modelli](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)di distribuzione diversi tramite il portale di Azure.

![Connessione classica della rete virtuale a una rete virtuale di Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Per controllare la configurazione quando si connette una rete virtuale classica a una rete virtuale di Azure Resource Manager, seguire queste istruzioni.

> [!Note] 
> I numeri dopo i componenti della rete virtuale in questi passaggi corrispondono ai numeri nel diagramma precedente. 

1. Assicurarsi che non vi siano spazi di indirizzi sovrapposti nelle reti virtuali connesse.
2. Verificare che l'intervallo di indirizzi per la rete virtuale di Azure Resource Manager (6) sia definito in modo accurato nella definizione classica della rete locale (3).
3. Verificare che l'intervallo di indirizzi per la rete virtuale classica (1) sia definito in modo accurato nell'istanza **dell'oggetto connessione** di Azure Resource Manager (4).
4. Verificare che l'indirizzo VIP del gateway di rete virtuale classico (2) sia definito in modo accurato nell'istanza **dell'oggetto Connessione** di Azure Resource Manager (4).
5. Verificare che il gateway di rete virtuale di Azure Resource Manager (5) sia definito in modo accurato nell'istanza classica della **definizione** di rete locale (3).
6. Verificare che le chiavi precondivise corrispondano a entrambe le reti virtuali connesse:Verify that the pre-shared keys are matching on both connected virtual networks:
   - Rete virtuale classica: **Definizione rete locale** (3)Classic virtual network: Local Network Definition (3)
   - Rete virtuale di Azure Resource Manager: oggetto connessione (4)Azure Resource Manager virtual network: **Connection object** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Creare una connessione VPN da punto a sitoCreate a point-to-site VPN connection

Una configurazione da punto a sito *(P2S* nel diagramma seguente) consente di creare una connessione protetta da un singolo computer client a una rete virtuale. Le connessioni da punto a sito sono utili quando si desidera connettersi alla rete virtuale da una postazione remota, ad esempio da casa o da una conferenza. Sono utili anche quando si dispone solo di pochi client che devono connettersi a una rete virtuale. 

La connessione VPN da punto a sito viene avviata dal computer client tramite il client VPN di Windows nativo. I client che si connettono usano i certificati per eseguire l'autenticazione.

![Connessione da punto a sito](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Le connessioni da punto a sito non richiedono un dispositivo VPN. Creano la connessione VPN tramite Secure Socket Tunneling Protocol (SSTP). È possibile connettere una connessione da punto a sito a una rete virtuale usando vari strumenti di distribuzione e modelli di distribuzione:You can connect a point-to-site connection to a virtual network by using various deployment tools and deployment models:

* [Configurare una connessione da punto a sito a una rete virtuale tramite il portale di AzureConfigure a point-to-site connection to a virtual network by using the Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurare una connessione da punto a sito a una rete virtuale tramite il portale di Azure (classico)Configure a point-to-site connection to a virtual network by using the Azure portal (classic)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configurare una connessione da punto a sito a una rete virtuale tramite PowerShellConfigure a point-to-site connection to a virtual network by using PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Convalidare la connessione da punto a sito

L'articolo [Risoluzione dei problemi: Problemi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) di connessione da punto ad azure illustra i problemi comuni relativi alle connessioni da punto a sito.

## <a name="create-a-multisite-vpn-connection"></a>Creare una connessione VPN multisito

È possibile aggiungere una connessione da sito a sito (*S2S* nel diagramma seguente) a una rete virtuale che dispone già di una connessione da sito a sito, connessione da punto a sito o di rete a rete. Questo tipo di connessione è spesso chiamato configurazione *multisito.* 

![Connessione multisito](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure attualmente funziona con due modelli di distribuzione: Azure Resource Manager e classica. I due modelli non sono completamente compatibili tra loro. Per configurare una connessione multisito con modelli diversi, vedere gli articoli seguenti:To configure a multisite connection with different models, see the following articles:

* [Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente (classica)Add a site-to-site connection to a virtual network with an existing VPN gateway connection (classic)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> I passaggi in questi articoli non si applicano ad Azure ExpressRoute e alle configurazioni di connessione coesistente da sito a sito. Per altre informazioni, vedere [ExpressRoute e connessioni coesistenti](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)da sito a sito .

## <a name="configure-transit-routing"></a>Configurare il routing di transito

Il routing di transito è uno scenario di routing specifico in cui si connettono più reti in una topologia a catena di margherite. Questo routing consente alle risorse nelle reti virtuali a entrambe le estremità della catena di comunicare tra loro tramite reti virtuali intermedie. Senza il routing di transito, le reti o i dispositivi sottoposti a peero tramite un hub non possono raggiungerlo.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configurare il routing di transito in una connessione da punto a sito

Si immagini uno scenario in cui si desidera configurare una connessione VPN da sito a sito tra VNetA e VNetB. Si desidera inoltre configurare una VPN da punto a sito per il client per connettersi al gateway di VNetA. Quindi, si desidera abilitare il routing di transito per i client da punto a sito per connettersi a VNetB, che passa attraverso VNetA. 

Questo scenario è supportato quando BGP è abilitato nella VPN da sito a sito tra VNetA e VNetB. Per ulteriori informazioni, vedere [Informazioni sul routing VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)da punto a sito .

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configurare il routing di transito in una connessione ExpressRouteConfigure transit routing in an ExpressRoute connection

Azure ExpressRoute consente di estendere le reti locali in Microsoft Cloud tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365. Per altre informazioni, vedere [Panoramica di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Connessione di peering privato ExpressRoute alle reti virtuali di AzureExpressRoute private peering connection to Azure virtual networks](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Se VNetA e VNetB si trovano nella stessa area geopolitica, è consigliabile [collegare entrambe le reti virtuali al circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) anziché configurare il routing di transito. Se le reti virtuali si trovano in aree geopolitiche diverse, è anche possibile collegarle direttamente al circuito se si dispone di [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se si dispone di ExpressRoute e la coesistenza da sito a sito, il routing di transito non è supportato. Per altre informazioni, vedere [Configurare ExpressRoute e da sito a sito tramite PowerShell.](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)

Se ExpressRoute è stato abilitato per connettere le reti locali a una rete virtuale di Azure, è possibile abilitare il peering tra le reti virtuali in cui si vuole avere il routing di transito. Per consentire alle reti locali di connettersi alla rete virtuale remota, è necessario configurare il [peering della rete virtuale.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity) 

> [!Note]
> Il peering della rete virtuale è disponibile solo per le reti virtuali nella stessa area.

Per verificare se è stato configurato il routing di transito per il peering della rete virtuale, seguire queste istruzioni:To check whether you have configured transit routing for virtual network peering, follow these instructions:

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account con i [ruoli e le autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. [Creare un peering tra VNetA e VNetB](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) come illustrato nel diagramma precedente. 
3. Nel riquadro visualizzato per la rete virtuale selezionare Peerings nella sezione **Impostazioni.In** the pane that appears for the virtual network, select **Peerings** in the Settings section.
4. Selezionare il peering che si desidera visualizzare. Selezionare quindi Configurazione per verificare di aver abilitato Consenti transito gateway sulla rete VNetA connessa al circuito ExpressRoute e Usa gateway remoto sulla rete VNetB remota non connessa al circuito ExpressRoute.Then, select **Configuration** to validate that you have enabled **Allow Gateway Transit** on the VNetA network connected to the ExpressRoute circuit and Use Remote **Gateway** on the remote VNetB network not connected to the ExpressRoute circuit.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configurare il routing di transito in una connessione di peering di rete virtualeConfigure transit routing in a virtual network peering connection

Dopo aver eseguito il peering delle reti virtuali, gli utenti possono anche configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. Per configurare una route di transito nel peering di rete virtuale, vedere Connessioni da [rete a rete](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Il transito del gateway non è supportato nella relazione di peering tra reti virtuali create tramite modelli di distribuzione diversi. Entrambe le reti virtuali nella relazione di peering devono essere state create tramite Resource Manager affinché il transito del gateway funzioni.

Per verificare se è stata configurata una route di transito per il peering della rete virtuale, seguire queste istruzioni:To check whether you have configured a transit route for virtual network peering, follow these instructions:

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account con i [ruoli e le autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. Nella finestra che contiene il testo **Cerca risorse**, nella parte superiore del portale, digitare **reti virtuali**. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** visualizzato selezionare la rete virtuale per la quale si vuole controllare l'impostazione del peering.
4. Nel riquadro visualizzato per la rete virtuale selezionato selezionare Peerings nella sezione **Impostazioni.In** the pane that appears for the virtual network that you selected, select **Peerings** in the Settings section.
5. Selezionare il peering che si desidera visualizzare. Verificare di aver abilitato **Consenti transito gateway** e Usa gateway **remoti** in **Configurazione**.

![Selezioni per verificare che sia stata configurata una route di transito per il peering della rete virtuale](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configurare il routing di transito in una connessione da rete a rete

Per configurare il routing di transito tra reti virtuali, è necessario abilitare BGP in tutte le connessioni intermedie da rete a rete utilizzando il modello di distribuzione di Resource Manager e PowerShell.To configure transit routing between virtual networks, you must enable BGP on all intermediate network-to-network connections by using the Resource Manager deployment model and PowerShell. Per istruzioni, vedere [Come configurare BGP nei gateway VPN di Azure tramite PowerShell.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Il traffico di transito tramite i gateway VPN di Azure è possibile tramite il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti staticamente nel file di configurazione di rete. BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi degli indirizzi di transito è soggetta a errori e non è consigliabile.

> [!Note]
> Configurare le connessioni classiche da rete a rete usando il portale classico di Azure o un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione di Azure Resource Manager o il portale di Azure.You can't create or modify a classic virtual network through the Azure Resource Manager deployment model or the Azure portal. Per ulteriori informazioni sul routing di transito per le reti virtuali classiche, vedere il blog Microsoft [Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configurare il routing di transito in una connessione da sito a sito

Per configurare il routing di transito tra la rete locale e una rete virtuale con una connessione da sito a sito, è necessario abilitare BGP in tutte le connessioni intermedie da sito a sito utilizzando il modello di distribuzione di Resource Manager e PowerShell. Per istruzioni, vedere [Come configurare BGP nei gateway VPN di Azure usando PowerShell.See How to configure BGP on Azure VPN gateways by using PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) for instructions.

Il traffico di transito tramite i gateway VPN di Azure è possibile tramite il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti staticamente nel file di configurazione di rete. BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi degli indirizzi di transito è soggetta a errori e non è consigliabile.

> [!Note]
> Configurare le connessioni classiche da sito a sito usando il portale classico di Azure o un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione di Azure Resource Manager o il portale di Azure.You can't create or modify a classic virtual network through the Azure Resource Manager deployment model or the Azure portal. Per ulteriori informazioni sul routing di transito per le reti virtuali classiche, vedere il blog Microsoft [Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurare BGP per un gateway VPN

BGP è il protocollo di routing standard utilizzato su Internet per scambiare informazioni di routing e raggiungibilità tra due o più reti. Quando BGP viene usato nel contesto delle reti virtuali di Azure, abilita i gateway VPN di Azure e i dispositivi VPN locali, noti come peer BGP o vicini. Si scambiano "route" che informeranno entrambi i gateway sulla disponibilità e la raggiungibilità per quei prefissi di passare attraverso i gateway o router coinvolti. 

BGP può anche abilitare il routing di transito tra più reti propagando route che un gateway BGP apprende da un peer BGP a tutti gli altri peer BGP. Per altre informazioni, vedere [Panoramica di BGP con](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)gateway VPN di Azure.For more information, see Overview of BGP with Azure VPN Gateway .

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurare BGP per una connessione VPN

Per configurare una connessione VPN che usa BGP, vedere [Come configurare BGP nei gateway VPN di Azure tramite PowerShell.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Abilitare BGP sul gateway di rete virtuale creando un numero di sistema autonomo (AS) per esso. I gateway di base non supportano BGP. Per controllare lo SKU del gateway, passare alla sezione **Panoramica** del pannello Gateway VPN nel portale di Azure.To check the SKU of the gateway, go to the Overview section of the **VPN Gateway** blade in the Azure portal. Se lo SKU è **Basic**, è necessario modificare lo SKU (vedere [Ridimensionamento del gateway)](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)in **VpnGw1**. 

Il controllo dello SKU causerà da 20 a 30 minuti di inattività. Non appena il gateway ha lo SKU corretto, è possibile aggiungere il numero AS usando il commandlet di PowerShell [Set-AzureRmVirtualNetworkGateway.As](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) as the gateway has the correct SKU, you can add the AS number by using the Set-AzureRmVirtualNetworkGateway PowerShell commandlet. Dopo aver configurato il numero AS, verrà fornito automaticamente un indirizzo IP peer BGP per il gateway.

È necessario `LocalNetworkGateway` fornire manualmente un numero AS e un indirizzo peer BGP. È possibile `ASN` impostare i valori e usando il commandlet di PowerShell New-AzureRmLocalNetworkGateway o `-BgpPeeringAddress` [Set-AzureRmLocalNetworkGateway.You](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) can set the e values by using either the [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) or the Set-AzureRmLocalNetworkGateway PowerShell commandlet. Alcuni numeri AS sono riservati per Azure e non è possibile usarli come descritto in [Informazioni su BGP con](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)il gateway VPN di Azure.

L'oggetto connessione deve avere BGP abilitato. È possibile `-EnableBGP` impostare `$True` il valore su [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) o [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Convalidare la configurazione BGP

Per verificare se BGP è configurato `get-AzureRmVirtualNetworkGateway` `get-AzureRmLocalNetworkGateway` correttamente, è possibile eseguire i commandlet e . Si noterà quindi l'output correlato `BgpSettingsText` a BGP nella parte. Ad esempio:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Creare una connessione VPN attiva/attiva a disponibilità elevata

Le principali differenze tra i gateway attivo/attivo e attivo/standby sono:

* È necessario creare due configurazioni IP gateway con due indirizzi IP pubblici.
* È necessario impostare il flag **EnableActiveActiveFeature.**
* Lo SKU del gateway deve essere **VpnGw1**, **VpnGw2**o **VpnGw3.**

Per ottenere la disponibilità elevata per la connettività cross-premise e da rete a rete, è necessario distribuire più gateway VPN e stabilire più connessioni parallele tra le reti e Azure.To achieve high availability for cross-premises and network-to-network connectivity, you should deploy multiple VPN gateways and establish multiple parallel connections between your networks and Azure. Per una panoramica delle opzioni di connettività e della topologia, vedere [Connettività cross-premise](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)e da rete a rete a disponibilità elevata.

Per creare connessioni cross-premise attive/attive da rete a rete, seguire le istruzioni in Configurare le [connessioni VPN S2S attive/attive con gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) di Azure per configurare un gateway VPN di Azure in modalità attiva/attiva.

> [!Note]  
> * Quando si aggiungono indirizzi al gateway di rete locale per la modalità attiva/attivata abilitata per BGP, *aggiungere solo gli indirizzi /32 dei peer BGP*. Se si aggiungono altri indirizzi, questi verranno considerati route statiche e avranno la precedenza sulle route BGP.
> * È necessario usare numeri BGP AS diversi per le reti locali che si connettono ad Azure.You must use different BGP AS numbers for your on-premises networks that are connecting to Azure. Se sono uguali, è necessario modificare il numero AS della rete virtuale se il dispositivo VPN locale usa già l'ASN per peer con altri vicini BGP.

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Modificare un tipo di gateway VPN di Azure dopo la distribuzioneChange an Azure VPN gateway type after deployment

Non è possibile modificare un tipo di gateway di rete virtuale di Azure da basato su criteri a basato su route o direttamente in modo diverso. È innanzitutto necessario eliminare il gateway. Dopo di che, l'indirizzo IP e la chiave precondivisa non verranno mantenuti. È quindi possibile creare un nuovo gateway del tipo desiderato. 

Per eliminare e creare un gateway, attenersi alla seguente procedura:

1. Eliminare tutte le connessioni associate al gateway originale.
2. Eliminare il gateway tramite il portale di Azure, PowerShell o PowerShell classico:Delete the gateway by using the Azure portal, PowerShell, or classic PowerShell: 
   * [Eliminare un gateway di rete virtuale tramite il portale di AzureDelete a virtual network gateway by using the Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Eliminare un gateway di rete virtuale tramite PowerShellDelete a virtual network gateway by using PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Eliminare un gateway di rete virtuale tramite PowerShell (classico)Delete a virtual network gateway by using PowerShell (classic)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Seguire i passaggi descritti in [Creare il gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) per creare il nuovo gateway del tipo desiderato e completare la configurazione VPN.

> [!Note]
> Questo processo richiederà circa 60 minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

