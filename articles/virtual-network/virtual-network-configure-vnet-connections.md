---
title: Configurare e convalidare la rete virtuale o le connessioni VPN
description: Istruzioni dettagliate per la configurazione e la convalida di varie distribuzioni di reti virtuali e VPN di Azure
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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099066"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configurare e convalidare la rete virtuale o le connessioni VPN

Questa procedura dettagliata include istruzioni dettagliate per la configurazione e la convalida di varie distribuzioni di reti virtuali e VPN di Azure. Gli scenari includono il routing di transito, le connessioni da rete a rete, il Border Gateway Protocol (BGP), le connessioni multisito e le connessioni da punto a sito.

I gateway VPN di Azure consentono la flessibilità nella disposizione di quasi tutti i tipi di topologia di rete virtuale connessa in Azure. Ad esempio, è possibile connettere reti virtuali:

- Tra le aree.
- Tra i tipi di rete virtuale (Azure Resource Manager rispetto alla versione classica).
- All'interno di Azure o in un ambiente ibrido locale.
- In sottoscrizioni diverse. 

## <a name="network-to-network-vpn-connection"></a>Connessione VPN da rete a rete

La connessione di una rete virtuale a un'altra rete virtuale (da rete a rete) tramite VPN è simile alla connessione di una rete virtuale a una posizione del sito locale. Entrambi i tipi di connettività usano un gateway VPN per fornire un tunnel sicuro tramite IPsec e IKE. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse.

![Connessione da rete a rete con IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se le reti virtuali si trovano nella stessa area, è consigliabile consentirne la connessione usando il peering di rete virtuale. Il peering di rete virtuale non usa un gateway VPN. Aumenta la velocità effettiva e riduce la latenza. Per configurare una connessione di peering di rete virtuale, selezionare **Configura e convalida peering VNet**.

Se le reti virtuali sono state create tramite il modello di distribuzione di Azure Resource Manager, selezionare **Configura e convalidare un gestione risorse VNet a una connessione gestione risorse VNet** per configurare una connessione VPN.

Se una delle reti virtuali è stata creata tramite il modello di distribuzione classica di Azure e l'altra è stata creata tramite Gestione risorse, selezionare **Configura e convalidare una VNet classica con una connessione gestione risorse VNet** per configurare una connessione VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configurare il peering di rete virtuale per due reti virtuali nella stessa area

Prima di iniziare a implementare e configurare il peering di rete virtuale di Azure, assicurarsi di soddisfare i prerequisiti seguenti:

* Le reti virtuali con peering devono trovarsi nella stessa area di Azure.
* Le reti virtuali con peering devono avere spazi di indirizzi IP che non si sovrappongono.
* Il peering viene eseguito tra due reti virtuali Non esiste alcuna relazione transitiva derivata tra i peering. Ad esempio, se VNetA è associato a VNetB e VNetB è associato a vengono eseguiti, VNetA *non* è associato a vengono eseguiti.

Quando si soddisfano i requisiti, è possibile [seguire l'esercitazione: Connettere reti virtuali con il peering di rete virtuale usando il](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) portale di Azure per creare e configurare il peering.

Per controllare la configurazione del peering, usare il metodo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account che disponga dei [ruoli e delle autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. Nella finestra che contiene il testo **Cerca risorse**, nella parte superiore del portale, digitare **reti virtuali**. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **reti virtuali** che viene visualizzato selezionare la rete virtuale per cui si vuole creare un peering.
4. Nel riquadro visualizzato per la rete virtuale selezionare **peering** nella sezione **Impostazioni** .
5. Selezionare un peering e visualizzare i risultati della configurazione.

![Selezioni per il controllo della configurazione del peering di rete virtuale](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Per Azure PowerShell, eseguire il comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) per ottenere il peering di rete virtuale. Di seguito è riportato un esempio:

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Connettere una rete virtuale Gestione risorse a un'altra rete virtuale Gestione risorse

È possibile configurare una connessione da una Gestione risorse rete virtuale a un'altra Gestione risorse rete virtuale direttamente. In alternativa, è possibile configurare la connessione utilizzando IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configurare una connessione VPN tra Gestione risorse reti virtuali

Per configurare una connessione tra Gestione risorse reti virtuali senza IPsec, vedere [configurare una connessione gateway VPN da rete a rete usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Per configurare una connessione con IPsec tra due reti virtuali Gestione risorse, seguire i passaggi da 1 a 5 in [creare una connessione da sito a sito nel portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) per ogni rete virtuale.

> [!Note]
> Questi passaggi funzionano solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in sottoscrizioni diverse, è necessario usare PowerShell per effettuare la connessione. Vedere l'articolo su [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Convalidare la connessione VPN tra Gestione risorse reti virtuali

![Connessione alla rete virtuale classica a una rete virtuale Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Per verificare che la connessione VPN sia configurata correttamente, seguire queste istruzioni.

> [!Note] 
> I numeri dopo i componenti della rete virtuale in questa procedura corrispondono ai numeri nel diagramma precedente.

1. Assicurarsi che non vi siano spazi di indirizzi sovrapposti nelle reti virtuali connesse.
2. Verificare che l'intervallo di indirizzi per la rete virtuale Azure Resource Manager (1) sia definito accuratamente nell'istanza dell' **oggetto connessione** (4).
3. Verificare che l'intervallo di indirizzi per la rete virtuale Azure Resource Manager (6) sia definito accuratamente nell'istanza dell' **oggetto connessione** (3).
4. Verificare che le chiavi precondivise corrispondano agli oggetti connessione.
5. Verificare che il Azure Resource Manager VIP del gateway di rete virtuale (2) sia definito accuratamente nell'istanza dell' **oggetto connessione** (4).
6. Verificare che il Azure Resource Manager VIP del gateway di rete virtuale (5) sia definito in modo accurato nell'istanza dell' **oggetto connessione** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Connettere una rete virtuale classica a una rete virtuale Gestione risorse

È possibile creare una connessione tra reti virtuali che si trovano in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che già dispongono di connessioni a reti locali, purché sia stato configurato il tipo di gateway come basato su route.

Per configurare una connessione tra una rete virtuale classica e una rete virtuale Gestione risorse, vedere [connettere reti virtuali da modelli di distribuzione diversi usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Connessione alla rete virtuale classica a una rete virtuale Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Per controllare la configurazione quando si connette una rete virtuale classica a una rete virtuale Azure Resource Manager, seguire queste istruzioni.

> [!Note] 
> I numeri dopo i componenti della rete virtuale in questa procedura corrispondono ai numeri nel diagramma precedente. 

1. Assicurarsi che non vi siano spazi di indirizzi sovrapposti nelle reti virtuali connesse.
2. Verificare che l'intervallo di indirizzi per la rete virtuale Azure Resource Manager (6) sia definito in modo accurato nella definizione della rete locale classica (3).
3. Verificare che l'intervallo di indirizzi per la rete virtuale classica (1) sia definito in modo accurato nell'istanza dell' **oggetto connessione** Azure Resource Manager (4).
4. Verificare che l'indirizzo VIP del gateway di rete virtuale classico (2) sia definito accuratamente nell'istanza dell' **oggetto connessione** Azure Resource Manager (4).
5. Verificare che il gateway di rete virtuale Azure Resource Manager (5) sia definito accuratamente nell'istanza della **definizione di rete locale** classica (3).
6. Verificare che le chiavi precondivise siano corrispondenti in entrambe le reti virtuali connesse:
   - Rete virtuale classica: **Definizione di rete locale** 3
   - Azure Resource Manager rete virtuale: **Oggetto Connection** 4

## <a name="create-a-point-to-site-vpn-connection"></a>Creare una connessione VPN da punto a sito

Una configurazione da punto a sito (*P2S* nel diagramma seguente) consente di creare una connessione sicura da un singolo computer client a una rete virtuale. Le connessioni da punto a sito sono utili quando si desidera connettersi alla rete virtuale da una posizione remota, ad esempio da casa o da una conferenza. Sono utili anche quando solo pochi client devono connettersi a una rete virtuale. 

La connessione VPN da punto a sito viene avviata dal computer client tramite il client VPN di Windows nativo. I client che si connettono usano i certificati per eseguire l'autenticazione.

![Connessione da punto a sito](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Le connessioni da punto a sito non richiedono un dispositivo VPN. Creano la connessione VPN tramite il protocollo SSTP (Secure Socket Tunneling Protocol). È possibile connettere una connessione da punto a sito a una rete virtuale usando vari strumenti di distribuzione e modelli di distribuzione:

* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configurare una connessione da punto a sito a una rete virtuale tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Convalidare la connessione da punto a sito

Articolo [sulla risoluzione dei problemi: Problemi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) di connessione da punto a sito di Azure illustrano i problemi comuni con le connessioni da punto a sito.

## <a name="create-a-multisite-vpn-connection"></a>Creare una connessione VPN multisito

È possibile aggiungere una connessione da sito a sito (*S2S* nel diagramma seguente) a una rete virtuale che dispone già di una connessione da sito a sito, una connessione da punto a sito o una connessione da rete a rete. Questo tipo di connessione è spesso definito configurazione *multisito* . 

![Connessione multisito](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure attualmente funziona con due modelli di distribuzione: Gestione risorse e classica. I due modelli non sono completamente compatibili tra loro. Per configurare una connessione multisito con modelli diversi, vedere gli articoli seguenti:

* [Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> I passaggi descritti in questi articoli non si applicano alle configurazioni di connessione coesistente di Azure ExpressRoute e da sito a sito. Per altre informazioni, vedere [ExpressRoute e connessioni coesistenti da sito a sito](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Configurare il routing di transito

Il routing di transito è uno scenario di routing specifico in cui è possibile connettere più reti in una topologia a catena di margherite. Questo routing consente alle risorse nelle reti virtuali di una delle due estremità della catena di comunicare tra loro tramite reti virtuali. Senza routing di transito, le reti o i dispositivi con peering tramite un hub non possono raggiungere l'uno dall'altro.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configurare il routing di transito in una connessione da punto a sito

Si immagini uno scenario in cui si vuole configurare una connessione VPN da sito a sito tra VNetA e VNetB. Si vuole anche configurare una VPN da punto a sito per consentire al client di connettersi al gateway di VNetA. Si vuole quindi abilitare il routing di transito per i client da punto a sito per la connessione a VNetB, che passa attraverso VNetA. 

Questo scenario è supportato quando BGP è abilitato nella VPN da sito a sito tra VNetA e VNetB. Per ulteriori informazioni, vedere [informazioni sul routing VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configurare il routing di transito in una connessione ExpressRoute

Azure ExpressRoute consente di estendere le reti locali in Microsoft Cloud tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365. Per altre informazioni, vedere [Panoramica di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Connessione peering privato ExpressRoute alle reti virtuali di Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Se VNetA e VNetB si trovano nella stessa area geopolitica, è consigliabile [collegare entrambe le reti virtuali al circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) anziché configurare il routing di transito. Se le reti virtuali si trovano in aree geopolitiche diverse, è anche possibile collegarle direttamente al circuito se si dispone di [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se si dispone di ExpressRoute e di coesistenza da sito a sito, il routing di transito non è supportato. Per altre informazioni, vedere [configurare ExpressRoute e da sito a sito tramite PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Se è stato abilitato ExpressRoute per connettere le reti locali a una rete virtuale di Azure, è possibile abilitare il peering tra le reti virtuali in cui si vuole avere il routing di transito. Per consentire alle reti locali di connettersi alla rete virtuale remota, è necessario configurare il [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Il peering di rete virtuale è disponibile solo per le reti virtuali nella stessa area.

Per verificare se è stato configurato il routing di transito per il peering di rete virtuale, seguire queste istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account che disponga dei [ruoli e delle autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. [Creare un peering tra VNetA e VNetB](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) , come illustrato nel diagramma precedente. 
3. Nel riquadro visualizzato per la rete virtuale selezionare **peering** nella sezione **Impostazioni** .
4. Selezionare il peering che si desidera visualizzare. Selezionare quindi **configurazione** per verificare che sia stato abilitato **Consenti transito gateway** sulla rete VNetA connessa al circuito ExpressRoute e **usare il gateway remoto** nella rete VNetB remota non connessa al ExpressRoute circuito.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configurare il routing di transito in una connessione di peering di rete virtuale

Dopo aver eseguito il peering delle reti virtuali, gli utenti possono anche configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. Per configurare una route di transito nel peering di rete virtuale, vedere [connessioni da rete a rete](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Il transito del gateway non è supportato nella relazione di peering tra le reti virtuali create tramite modelli di distribuzione diversi. Entrambe le reti virtuali nella relazione di peering devono essere state create tramite Gestione risorse per il funzionamento del transito del gateway.

Per verificare se è stata configurata una route di transito per il peering di rete virtuale, seguire queste istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account che disponga dei [ruoli e delle autorizzazioni](virtual-network-manage-peering.md#permissions)necessari.
2. Nella finestra che contiene il testo **Cerca risorse**, nella parte superiore del portale, digitare **reti virtuali**. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **reti virtuali** che viene visualizzato selezionare la rete virtuale per cui si vuole controllare l'impostazione del peering.
4. Nel riquadro visualizzato per la rete virtuale selezionata selezionare **peering** nella sezione **Impostazioni** .
5. Selezionare il peering che si desidera visualizzare. Verificare di aver abilitato **Consenti transito gateway** e **usare i gateway remoti** in **configurazione**.

![Selezioni per verificare che sia stata configurata una route di transito per il peering di rete virtuale](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configurare il routing di transito in una connessione da rete a rete

Per configurare il routing di transito tra reti virtuali, è necessario abilitare BGP su tutte le connessioni da rete a rete intermedie usando il modello di distribuzione Gestione risorse e PowerShell. Per istruzioni, vedere [come configurare BGP nei gateway VPN di Azure tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Il traffico di transito attraverso i gateway VPN di Azure è possibile tramite il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi di indirizzi di transito è soggetta a errori e non è consigliata.

> [!Note]
> È possibile configurare connessioni di rete classica tramite il portale di Azure classico oppure usando un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione Azure Resource Manager o l'portale di Azure. Per altre informazioni sul routing di transito per le reti virtuali classiche, vedere il [Blog per sviluppatori Microsoft](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configurare il routing di transito in una connessione da sito a sito

Per configurare il routing di transito tra la rete locale e una rete virtuale con una connessione da sito a sito, è necessario abilitare BGP in tutte le connessioni da sito a sito intermedie usando il modello di distribuzione Gestione risorse e PowerShell. Per istruzioni, vedere [come configurare BGP nei gateway VPN di Azure usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) .

Il traffico di transito attraverso i gateway VPN di Azure è possibile tramite il modello di distribuzione classica, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN tramite il modello di distribuzione classica. Senza BGP, la definizione manuale degli spazi di indirizzi di transito è soggetta a errori e non è consigliata.

> [!Note]
> Per configurare le connessioni da sito a sito classiche è possibile usare il portale di Azure classico o un file di configurazione di rete nel portale classico. Non è possibile creare o modificare una rete virtuale classica tramite il modello di distribuzione Azure Resource Manager o l'portale di Azure. Per altre informazioni sul routing di transito per le reti virtuali classiche, vedere il [Blog per sviluppatori Microsoft](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurare BGP per un gateway VPN

BGP è il protocollo di routing standard utilizzato su Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. Quando BGP viene usato nel contesto di reti virtuali di Azure, Abilita i gateway VPN di Azure e i dispositivi VPN locali, noti come peer BGP o router adiacenti. Scambiano le "Route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per attraversare i gateway o i router necessari. 

BGP può anche abilitare il routing di transito tra più reti propagando le route che un gateway BGP apprende da un peer BGP a tutti gli altri peer BGP. Per altre informazioni, vedere [Panoramica di BGP con il gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurare BGP per una connessione VPN

Per configurare una connessione VPN che usa BGP, vedere [come configurare BGP nei gateway VPN di Azure tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Abilitare BGP nel gateway di rete virtuale creando un numero di sistema autonomo (come). I gateway di base non supportano BGP. Per controllare lo SKU del gateway, passare alla sezione **Panoramica** del pannello **gateway VPN** nel portale di Azure. Se lo SKU è di **base**, è necessario modificare lo SKU (vedere [ridimensionamento del gateway](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) in **VpnGw1**. 

Il controllo dello SKU provocherà da 20 a 30 minuti di inattività. Non appena il gateway ha lo SKU corretto, è possibile aggiungere il numero AS usando il cmdlet di PowerShell [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) . Dopo aver configurato il numero AS, un IP peer BGP per il gateway verrà fornito automaticamente.

È necessario specificare `LocalNetworkGateway` manualmente con un numero As e un indirizzo peer BGP. È possibile impostare i `ASN` valori `-BgpPeeringAddress` e usando il [nuovo AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) o [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell cmdlet. Alcuni numeri AS sono riservati ad Azure e non è possibile usarli come descritto in [informazioni su BGP con il gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Per l'oggetto Connection deve essere abilitato BGP. È possibile impostare il `-EnableBGP` valore su `$True` tramite [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) o [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Convalidare la configurazione BGP

Per verificare se BGP è configurato correttamente, è possibile eseguire `get-AzureRmVirtualNetworkGateway` e `get-AzureRmLocalNetworkGateway` cmdlet. Si noterà quindi l' `BgpSettingsText` output correlato a BGP nella parte. Esempio:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Creare una connessione VPN attiva/attiva a disponibilità elevata

Le differenze principali tra i gateway attivo/attivo e attivo/standby sono:

* È necessario creare due configurazioni IP del gateway con due indirizzi IP pubblici.
* È necessario impostare il flag **contrassegno enableactiveactivefeature** .
* Lo SKU del gateway deve essere **VpnGw1**, **VpnGw2**o **VpnGw3**.

Per ottenere la disponibilità elevata per la connettività cross-premise e da rete a rete, è consigliabile distribuire più gateway VPN e stabilire più connessioni parallele tra le reti e Azure. Per una panoramica delle opzioni di connettività e della topologia, vedere [connettività tra più sedi locali e rete a disponibilità elevata](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Per creare connessioni cross-premise e da rete a rete attive/attive, seguire le istruzioni in [configurare connessioni VPN S2S attive/attive con i gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) per configurare un gateway VPN di Azure in modalità attivo/attivo.

> [!Note]  
> * Quando si aggiungono indirizzi al gateway di rete locale per la modalità attiva/attiva BGP abilitata, *aggiungere solo gli indirizzi/32 dei peer BGP*. Se si aggiungono altri indirizzi, verranno considerati route statiche e avranno la precedenza sulle route BGP.
> * Per le reti locali che si connettono ad Azure, è necessario usare diversi numeri BGP come numeri. Se sono uguali, è necessario modificare la rete virtuale come numero se il dispositivo VPN locale usa già l'ASN per il peer con altri vicini BGP.

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Modificare un tipo di gateway VPN di Azure dopo la distribuzione

Non è possibile modificare un tipo di gateway di rete virtuale di Azure da criteri basati su Route o viceversa. Prima di tutto è necessario eliminare il gateway. Successivamente, l'indirizzo IP e la chiave precondivisa non verranno conservati. È quindi possibile creare un nuovo gateway del tipo desiderato. 

Per eliminare e creare un gateway, seguire questa procedura:

1. Eliminare tutte le connessioni associate al gateway originale.
2. Eliminare il gateway usando il portale di Azure, PowerShell o PowerShell classico: 
   * [Eliminare un gateway di rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Eliminare un gateway di rete virtuale usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Eliminare un gateway di rete virtuale usando PowerShell (versione classica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Seguire la procedura descritta in [creare il gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) per creare il nuovo gateway del tipo desiderato e completare la configurazione della VPN.

> [!Note]
> Questo processo comporterà circa 60 minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di connettività tra macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

