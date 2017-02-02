---
title: 'Configurare una connessione VPN di Azure da sito a sito cross-premise: portale | Documentazione Microsoft'
description: Come creare una rete virtuale con il modello di distribuzione Resource Manager e connetterla alla rete locale con una connessione del gateway VPN da sito a sito.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0258e1093926e6239650a8b1ecbb7c7a778616c6
ms.openlocfilehash: d9af99dc3b24315d3876228dc926d433c4b692e9


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Creare una rete virtuale con una connessione da sito a sito usando il portale di Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Classica - Portale classico](vpn-gateway-site-to-site-create.md)
> 
> 

Questo articolo illustra la creazione di una rete virtuale e una connessione gateway VPN da sito a sito alla rete locale con il modello di distribuzione Azure Resource Manager e il portale di Azure. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride.

![Diagramma](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Metodi e modelli di distribuzione per le connessioni da sito a sito
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La tabella seguente descrive i metodi e i modelli di distribuzione attualmente disponibili per le configurazioni da sito a sito. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurazioni aggiuntive
Se si vogliono connettere più reti virtuali senza creare una connessione a un percorso locale, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md). Per aggiungere una connessione da sito a sito a una rete virtuale che ha già una connessione, vedere [Add a S2S connection to a VNet with an existing VPN gateway connection](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente).

## <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare la configurazione, verificare la disponibilità degli elementi seguenti:

* Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](http://azure.microsoft.com/pricing/free-trial).

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>Valori di configurazione di esempio per questo esercizio
Quando si segue questa procedura come esercizio, è possibile usare i valori della configurazione di esempio:

* **Nome della rete virtuale:** TestVNet1
* **Spazio di indirizzi:** 10.11.0.0/16 e 10.12.0.0/16
* **Subnet:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **Gruppo di risorse:** TestRG1
* **Località:** Stati Uniti orientali
* **Server DNS:** 8.8.8.8
* **Nome gateway:** VNet1GW
* **IP pubblico:** VNet1GWIP
* **Tipo VPN:** Basato su route
* **Tipo di connessione:** Da sito a sito (IPSec)
* **Tipo di gateway:** VPN
* **Nome del gateway di rete locale:** Site2
* **Nome connessione:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Crea rete virtuale
Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti. Se sono presenti subnet che si sovrappongono, la connessione non funziona correttamente. Se la rete virtuale è configurata con le impostazioni corrette, è possibile iniziare la procedura descritta nella sezione [Specificare un server DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. Aggiungere un altri spazi degli indirizzi e subnet
È possibile aggiungere altri spazi degli indirizzi e subnet alla rete virtuale dopo che è stata creata.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. Specificare un server DNS
### <a name="to-specify-a-dns-server"></a>Per specificare un server DNS
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. Creare una subnet del gateway
Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale con cui si vuole stabilire la connessione. Se possibile, è consigliabile creare una subnet del gateway con un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficienti a soddisfare altri requisiti di configurazione futuri.

Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si crea la subnet del gateway.

### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet del gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Creare un gateway di rete virtuale
Se si crea questa configurazione come esercizio, è possibile fare riferimento ai [valori di configurazione di esempio](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. Creare un gateway di rete locale
Il 'gateway di rete locale' fa riferimento al percorso locale. Assegnare un nome al gateway di rete locale a cui Azure potrà fare riferimento. 

Se si crea questa configurazione come esercizio, è possibile fare riferimento ai [valori di configurazione di esempio](#values).

### <a name="to-create-a-local-network-gateway"></a>Per creare un gateway di rete locale
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. Configurare il dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. Una connessione VPN da sito a sito
Creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori con i propri. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN. 

Prima di iniziare questa sezione, verificare che la creazione del gateway di rete virtuale e del gateway di rete locale sia stata completata. Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si crea la connessione.

### <a name="to-create-the-vpn-connection"></a>Per creare la connessione VPN
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. Verificare la connessione VPN
Per verificare la connessione VPN è possibile usare il portale o PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi
*  Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Jan17_HO4-->


