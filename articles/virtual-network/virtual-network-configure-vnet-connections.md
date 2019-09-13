---
title: Configurare e convalidare connessioni VPN o di reti virtuali
description: Istruzioni dettagliate per la configurazione e la convalida di varie distribuzioni VPN e VNet di Azure
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
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901893"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>Configurare e convalidare connessioni VPN o di reti virtuali

Questa procedura dettagliata guidata fornisce istruzioni dettagliate per la configurazione e la convalida di varie distribuzioni VPN e VNet di Azure in scenari come, routing di transito, VNet-to-VNet, BGP, multisito, da punto a sito e così via.

I gateway VPN di Azure consentono la flessibilità nella disposizione di quasi tutti i tipi di topologia di rete virtuale connessa (VNet) in Azure: è possibile connettere reti virtuali tra più aree, tra i tipi VNet (Azure Resource Manager vs. Classica), in Azure o con ambiente ibrido locale, in sottoscrizioni diverse e così via. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Scenario 1: Connessione VPN da VNet a VNet

La connessione di una rete virtuale a un'altra rete virtuale (da VNet a VNet) tramite VPN è simile alla connessione di una VNet a un percorso di sito locale. Entrambi i tipi di connettività usano un gateway VPN per fornire un tunnel sicuro tramite **IPSec/IKE**. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse.

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Figura 1-VNet-VNet con IPsec

Se i reti virtuali si trovano nella stessa area, è consigliabile prendere in considerazione la connessione tramite il peering VNet, che non usa un gateway VPN, incrementa la velocità effettiva e riduce la latenza, selezionare **Configura e convalida peering VNet** per configurare un peering VNet connessione.

Se i reti virtuali vengono creati usando il modello di distribuzione di Azure Resource Manager, selezionare **Configura e convalidare un gestione risorse VNet a una connessione gestione risorse VNet** per configurare una connessione VPN.

Se uno dei reti virtuali viene creato usando il modello di distribuzione classica di Azure, l'altro viene creato da Gestione risorse, selezionare **Configura e convalidare una VNet classica con una connessione gestione risorse VNet** per configurare una connessione VPN.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Configurazione 1: Configurare il peering VNet per due reti virtuali nella stessa area

Prima di iniziare l'implementazione del peering di Azure VNet, assicurarsi di soddisfare i prerequisiti seguenti per configurare il peering VNet:

* Le reti virtuali con peering devono trovarsi nella stessa area di Azure.
* Le reti virtuali con peering devono avere spazi di indirizzi IP di non sovrapposte.
* Il peering viene eseguito tra due reti virtuali Non esiste alcuna relazione transitiva derivata tra i peering. Se, ad esempio, vengono eseguiti il peering della rete virtuale A con la rete virtuale B e il peering della rete virtuale B con la rete virtuale C, il peering della rete virtuale A con la rete virtuale C *non* viene eseguito.

Quando si soddisfano i requisiti, è possibile seguire l' [esercitazione creare un peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) per creare e configurare il peering di VNet.

Per controllare la configurazione del peering VNet, usare i metodi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account che disponga dei [ruoli e delle autorizzazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessari.
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **Reti virtuali** che viene visualizzato fare clic sulla rete virtuale per cui si vuole creare un peering.
4. Nel riquadro visualizzato per la rete virtuale selezionata fare clic su **peering** nella sezione **Impostazioni** .
5. Fare clic sul peering di cui si vuole controllare la configurazione.

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Usando Azure PowerShell, eseguire il comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) per ottenere il peering di rete virtuale, ad esempio:

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

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Tipo di connessione 1: Connettere un Gestione risorse VNet a Gestione risorse VNet (Azure Resource Manager Azure Resource Manager)

È possibile configurare una connessione da uno Gestione risorse VNet a un altro Gestione risorse VNet direttamente o configurare la connessione con IPsec.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Configurazione 2: Configurare la connessione VPN tra Gestione risorse reti virtuali

Per configurare una connessione tra Gestione risorse reti virtuali senza IPsec, vedere [configurare una connessione gateway VPN da VNet a VNet usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Per configurare una connessione con IPsec tra due Gestione risorse reti virtuali, seguire i passaggi 1-5 in [creare una connessione da sito a sito nel portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) per ogni VNet.

> [!Note]
> Questa procedura funziona solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in diverse sottoscrizioni, è necessario usare PowerShell per creare la connessione. Vedere l'articolo su [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Convalidare la connessione VPN tra Gestione risorse reti virtuali

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Figura 4-connessione VNet classica a Azure Resource Manager VNet

Per verificare che la connessione VPN sia configurata correttamente, seguire le istruzioni:

> [!Note]
> Il numero dopo i componenti della rete virtuale, ad esempio "VNET 1", nei passaggi seguenti corrisponde ai numeri nella figura 4.

1. Verificare la presenza di spazi degli indirizzi sovrapposti nella reti virtuali connessa.

   > [!Note]
   > Non possono essere presenti spazi degli indirizzi sovrapposti in VNET 1 e VNET 6. 

2. Verificare Azure Resource Manager intervallo di indirizzi VNET 1 sia definito accuratamente nell' **oggetto connessione** 4.
3. Verificare Azure Resource Manager intervallo di indirizzi VNET 6 sia definito accuratamente nell' **oggetto Connection** 3.
4. Verificare che le chiavi precondivise siano corrispondenti in entrambi gli oggetti connessione 3 e 4.
5. Verificare che Azure Resource Manager VNET Gateway 2 VIP sia definito accuratamente nell' **oggetto connessione** 4.
6. Verificare che il Azure Resource Manager VNET Gateway 5 VIP sia definito accuratamente nell' **oggetto Connection** 3.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Tipo di connessione 2: Connettere una rete virtuale classica a una rete virtuale di Resource Manager

È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che dispongono già di connessioni a reti locali, purché il tipo di gateway sia stato configurato come basato su route.

Per configurare una connessione tra una VNet classica e una Gestione risorse VNet, vedere [connettere reti virtuali da modelli di distribuzione diversi usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) per altre informazioni.

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Figura 5-connessione VNet classica a Azure Resource Manager VNet

Per controllare la configurazione quando si connette una VNet classica a un Azure Resource Manager VNet, seguire le istruzioni:

> [!Note]
> Il numero dopo i componenti della rete virtuale come "VNET 1" nei passaggi seguenti corrisponde ai numeri nella figura 5.

1. Verificare la presenza di spazi degli indirizzi sovrapposti nella reti virtuali connessa.

   > [!Note]
   > Non possono essere presenti spazi degli indirizzi sovrapposti in VNET 1 e VNET 6

2. Verificare Azure Resource Manager intervallo di indirizzi VNet 6 sia definito accuratamente nella definizione della rete locale classica 3.
3. Verificare che l'intervallo di indirizzi di VNet 1 classico sia definito accuratamente nell' **oggetto di connessione** Azure Resource Manager 4.
4. Verificare che l'indirizzo VIP del gateway VNet 2 classico sia definito accuratamente nell' **oggetto di connessione** Azure Resource Manager 4.
5. Verificare che il Azure Resource Manager VNet Gateway 5 VIP sia definito accuratamente nella definizione di **rete locale** classica 3.
6. Verificare che le chiavi precondivise siano corrispondenti in entrambe le reti virtuali connesse:
   1. VNET classica-definizione di rete locale 3
   2. Azure Resource Manager VNET-oggetto connessione 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Scenario 2: Connessione VPN da punto a sito

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. Una soluzione Da punto a sito è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale. La connessione VPN da punto a sito viene avviata dal computer client usando il client VPN di Windows nativo. I client che si connettono usano i certificati per eseguire l'autenticazione.

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Figura 2-connessione da punto a sito

Le connessioni da punto a sito non richiedono un dispositivo VPN. La modalità Da punto a sito crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). È possibile connettere una connessione da punto a sito a una VNet usando un modello di distribuzione e strumenti di distribuzione diversi:

* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurare una connessione da punto a sito a una VNet usando il portale di Azure (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configurare una connessione da punto a sito a una rete virtuale usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Convalidare le connessioni da punto a sito

Articolo [sulla risoluzione dei problemi: Problemi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) di connessione da punto a sito di Azure illustrano i problemi comuni con le connessioni da punto a sito.

## <a name="scenario-3-multi-site-vpn-connection"></a>Scenario 3: Connessione VPN multisito

È possibile aggiungere una connessione da sito a sito (S2S) a una VNet che dispone già di una connessione S2S, una connessione da punto a sito o una connessione VNet a VNet, questo tipo di connessione è spesso noto come configurazione **multisito** . 

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Figura 3-connessione multisito

Azure attualmente funziona con due modelli di distribuzione: Gestione risorse e classica. I due modelli non sono completamente compatibili tra loro. Per configurare una connessione **multisito** con modelli diversi, vedere gli articoli seguenti:

* [Aggiungere una connessione da sito a sito a una VNet con una connessione gateway VPN esistente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Aggiungere una connessione da sito a sito a una VNet con una connessione gateway VPN esistente (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Questi passaggi non si applicano a ExpressRoute e alle configurazioni di connessione coesistente da sito a sito. Per altre informazioni sulle connessioni coesistenti, vedere [connessioni coesistenti ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Scenario 4: Configurare il routing di transito

Il routing transitivo è uno scenario di routing specifico in cui si connettono più reti in una topologia "Daisy Chain". Questo routing consente alle risorse in reti virtuali a entrambe le estremità della "catena" di comunicare tra loro tramite reti virtuali tra le due. Senza routing transitivo, le reti o i dispositivi con peering tramite un hub non possono raggiungere l'uno dall'altro.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Configurazione 1: Configurare il routing di transito in una connessione da punto a sito

In questo scenario viene configurata una connessione VPN da sito a sito tra VNetA e VNetB, viene anche configurata una VPN da punto a sito per la connessione del client al gateway di VNetA. Si vuole quindi abilitare il routing di transito per i client da punto a sito per la connessione a VNetB, che passa attraverso VNetA. Questo scenario è supportato quando BGP è abilitato nella VPN da sito a sito tra VNetA e VNetB. Per ulteriori informazioni, vedere [informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Configurazione 2: Configurare il routing di transito in una connessione ExpressRoute

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.  Per altre informazioni, vedere [Panoramica di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Figura 6: connessione del peering privato di ExpressRoute ad Azure reti virtuali

> [!Note]
> Se VNetA e VNetB si trovano nella stessa area geopolitica, è consigliabile [collegare entrambi reti virtuali al circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) anziché configurare il routing transitivo. Se le reti virtuali si trovano in aree geopolitiche diverse, è anche possibile collegarle direttamente al circuito se si dispone di [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se si dispone di ExpressRoute e di coesistenza da sito a sito, il routing di transito non è supportato. Per altre informazioni, vedere [configurare connessioni coesistenti ExpressRoute e da sito a sito](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Se è stato abilitato ExpressRoute per connettere le reti locali a una rete virtuale di Azure, è possibile abilitare il peering VNet tra la reti virtuali che si vuole usare per il routing transitivo. Per consentire alle reti locali di connettersi alla VNet remota, è necessario configurare il [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Il peering di VNet è disponibile solo per reti virtuali nella stessa area.

Per verificare se è stata configurata la route di transito per il peering VNet, seguire le istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account che disponga dei [ruoli e delle autorizzazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessari.
2. [Creare un peering tra VNet a e B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) come nel diagramma precedente (Figura 6). 
3. Nel riquadro visualizzato per la rete virtuale selezionata fare clic su **peering** nella sezione **Impostazioni** .
4. Fare clic sul peering che si vuole visualizzare e sulla **configurazione** per verificare che sia stata abilitata l' **autorizzazione Consenti transito gateway** sulla VNetA connessa al circuito ExpressRoute e **usare il gateway remoto** nel VNetB remoto non connesso al ExpressRoute circuito.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Configurazione 3: Configurare il routing di transito in una connessione peering VNet

Dopo aver eseguito il peering delle reti virtuali, gli utenti possono anche configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. Per configurare la route di transito nel peering VNet, controllare le [connessioni da rete virtuale a rete](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)virtuale.

> [!Note]
> Il transito del gateway non è supportato nella relazione di peering tra le reti virtuali create tramite modelli di distribuzione diversi. Perché un transito gateway possa funzionare, entrambe le reti virtuali nella relazione di peering devono essere state create tramite Resource Manager.

Per verificare se è stata configurata la route di transito per il peering VNet, seguire le istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account che disponga dei [ruoli e delle autorizzazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessari.
2. Nella casella contenente le risorse di ricerca del testo nella parte superiore del portale di Azure digitare *reti virtuali*. Fare clic su **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **reti virtuali** che viene visualizzato fare clic sulla rete virtuale per cui si vuole controllare l'impostazione del peering.
4. Nel riquadro visualizzato per la rete virtuale selezionata fare clic su **peering** nella sezione **Impostazioni** .
5. Fare clic sul peering che si vuole visualizzare e verificare di avere abilitato **Consenti transito gateway** e **usare il gateway remoto** in **configurazione**.

![IMMAGINE](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Configurazione 4: Configurare il routing di transito in una connessione da VNet a VNet

Per configurare il routing di transito tra reti virtuali, è necessario abilitare BGP in tutte le connessioni da VNet a VNet intermedie usando il modello di distribuzione Gestione risorse e PowerShell. Per istruzioni, vedere [come configurare BGP nei gateway VPN di Azure tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Il traffico di transito attraverso il gateway VPN di Azure è possibile usando il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi di indirizzi di transito è soggetta a errori e non è consigliata.

> [!Note]
> Le connessioni da VNet a VNet classiche vengono configurate usando il portale di Azure (classico) o usando un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione Azure Resource Manager o portale di Azure. Per altre informazioni sul routing di transito per le reti virtuali classiche, vedere [Hub & spoke, Daisy Chain e topologie VNET a maglia intera in Azure ARM con VPN (V1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Configurazione 5: Configurare il routing di transito in una connessione da sito a sito

Per configurare il routing di transito tra la rete locale e una VNet con una connessione da sito a sito, è necessario abilitare BGP su tutte le connessioni da sito a sito intermedie usando il modello di distribuzione Gestione risorse e PowerShell, vedere [come configurare BGP nei gateway VPN di Azure usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) per le istruzioni.

Il traffico di transito attraverso il gateway VPN di Azure è possibile usando il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi di indirizzi di transito è soggetta a errori e non è consigliata.

> [!Note]
> Le connessioni classiche da sito a sito vengono configurate usando il portale di Azure (classico) o usando un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione Azure Resource Manager o portale di Azure. Per altre informazioni sul routing di transito per le reti virtuali classiche, vedere [Hub & spoke, Daisy Chain e topologie VNET a maglia intera in Azure ARM con VPN (V1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Scenario 5: Configurare BGP per un gateway VPN

BGP è il protocollo di routing standard utilizzato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. Quando BGP viene usato nel contesto di reti virtuali di Azure, BGP Abilita i gateway VPN di Azure e i dispositivi VPN locali, noti come peer BGP o router adiacenti. Scambiano le "Route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per attraversare i gateway o i router necessari. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP. Per altre informazioni, vedere [Panoramica di BGP con i gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurare BGP per una connessione VPN

Per configurare una connessione VPN che usa BGP, vedere [come configurare BGP nei gateway VPN di Azure tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Abilitare BGP nel gateway di rete virtuale creando un numero AS. I gateway di base non supportano BGP. Per verificare lo SKU del gateway, passare alla sezione Panoramica del pannello gateway VPN nel portale di Azure. Se lo SKU è di **base**, è necessario modificare lo SKU ([ridimensionamento del gateway](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) nello SKU **VpnGw1** . Il controllo dello SKU provocherà un tempo di inattività di 20-30 minuti. Non appena il gateway ha lo SKU corretto, è possibile aggiungerlo tramite il comando [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) di PowerShell-Let. Dopo aver configurato il numero AS, un IP peer BGP per il gateway verrà fornito automaticamente.
> 2. LocalNetworkGateway deve essere fornito **manualmente** con un numero As e un indirizzo peer BGP. È possibile impostare i valori **ASN** e **-Indirizzo peer BGP** usando il comando [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) o [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) di PowerShell-Let. Alcuni numeri AS sono riservati ad Azure e non è possibile usarli come descritto in [informazioni su BGP con il gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Infine, per l'oggetto Connection deve essere abilitato BGP. È possibile impostare il valore **-EnableBGP** su `$True` tramite [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) o [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Convalidare la configurazione BGP

Per verificare se BGP è configurato correttamente, è possibile eseguire il cmdlet `get-AzureRmVirtualNetworkGateway` e `get-AzureRmLocalNetworkGateway`, quindi si noterà l'output correlato a BGP nella parte BgpSettingsText. Ad esempio:  BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Scenario 6: Connessione VPN attivo-attivo a disponibilità elevata

Le differenze principali tra i gateway active-active e active-standby sono:

* È necessario creare due configurazioni IP del gateway con due indirizzi IP pubblici
* È necessario impostare il flag *contrassegno enableactiveactivefeature*
* Lo SKU del gateway deve essere VpnGw1, VpnGw2, VpnGw3

Per ottenere una disponibilità elevata per la connettività cross-premise e da rete virtuale a rete virtuale, occorre distribuire più gateway VPN e stabilire più connessioni parallele tra le reti e Azure. Per una panoramica delle opzioni di connettività e della topologia, vedere [connettività cross-premise a disponibilità elevata e da VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Per creare connessioni cross-premise e da VNet a VNet di tipo attivo-attivo, seguire le istruzioni in [configurare connessioni VPN S2S Active-Active con i gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) per configurare il gateway VPN di Azure in modalità attiva/attiva.

> [!Note]  
> 1. Quando si aggiungono indirizzi al gateway di rete locale per BGP abilitato, la modalità da attivo a attivo *aggiunge solo gli indirizzi/32 dei peer BGP*. Se si aggiungono altri indirizzi, verranno considerati route statiche e avranno la precedenza sulle route BGP.
> 2. È necessario usare ASN BGP diversi per le reti locali che si connettono ad Azure. Se sono uguali, è necessario modificare il VNet ASN se il dispositivo VPN locale usa già l'ASN per il peer con altri vicini BGP.

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Scenario 7: Modificare un tipo di gateway VPN di Azure dopo la distribuzione

Non è possibile modificare un tipo di gateway Azure VNet da criteri a basati su Route o viceversa. È necessario eliminare il gateway, dopo che l'indirizzo IP e la chiave precondivisa (PSK) desiderovenir mantenuti. È quindi possibile creare un nuovo gateway di tipo desiderato. Per eliminare e creare un gateway, seguire questa procedura:

1. Eliminare tutte le connessioni associate al gateway originale.
2. Eliminare il gateway usando portale di Azure, PowerShell o PowerShell classico: 
   * [Eliminare un gateway di rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Eliminare un gateway di rete virtuale usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Eliminare un gateway di rete virtuale usando PowerShell (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Seguire la procedura descritta in [creare il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) per creare il nuovo gateway del tipo desiderato e completare la configurazione della VPN.

> [!Note]
> Questo processo comporterà circa 60 minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

