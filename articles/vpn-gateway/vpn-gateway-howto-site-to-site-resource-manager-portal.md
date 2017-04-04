---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito: portale | Documentazione Microsoft'
description: "Passaggi per creare una connessione IPsec dalla rete locale a una rete virtuale di Azure tramite Internet pubblico. Questa procedura consentirà di creare una connessione gateway VPN da sito a sito cross-premise usando il portale."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Creare una connessione da sito a sito nel portale di Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Classica - Portale di Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Classica - Portale classico](vpn-gateway-site-to-site-create.md)
>
>


Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico assegnato e non dietro una NAT. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride.

Questo articolo illustra la creazione di una rete virtuale e una connessione gateway VPN da sito a sito alla rete locale con il modello di distribuzione Azure Resource Manager e il portale di Azure. 

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Metodi e modelli di distribuzione per le connessioni da sito a sito
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La tabella seguente descrive i metodi e i modelli di distribuzione attualmente disponibili per le configurazioni da sito a sito. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurazioni aggiuntive
Se si vogliono connettere più reti virtuali senza creare una connessione a un percorso locale, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md). Per aggiungere una connessione da sito a sito a una rete virtuale che ha già una connessione, vedere [Add a S2S connection to a VNet with an existing VPN gateway connection](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Aggiungere una connessione da sito a sito a una rete virtuale con una connessione gateway VPN esistente).

## <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare la configurazione, verificare la disponibilità degli elementi seguenti:

* Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Se non si ha familiarità con gli spazi degli indirizzi IP disponibili nella rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Le connessioni da sito a sito non possono avere spazi degli indirizzi sovrapposti.
* Un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Valori di esempio
Quando si segue questa procedura come esercizio, è possibile usare i valori di esempio seguenti:

* **Nome della rete virtuale:** TestVNet1
* **Spazio di indirizzi:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (facoltativo per questo esercizio)
* **Subnet:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (facoltativo per questo esercizio)
  * GatewaySubnet: 10.11.255.0/27
* **Gruppo di risorse:** TestRG1
* **Località:** Stati Uniti orientali
* **Server DNS:** indirizzo IP del server DNS
* **Nome gateway di rete virtuale:** VNet1GW
* **IP pubblico:** VNet1GWIP
* **Tipo VPN:** Basato su route
* **Tipo di connessione:** Da sito a sito (IPSec)
* **Tipo di gateway:** VPN
* **Nome del gateway di rete locale:** Site2
* **Nome connessione:** VNet1toSite2

## <a name="CreatVNet"></a>1. Crea rete virtuale

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Specificare un server DNS
Il server DNS non è necessario per le connessioni da sito a sito. Se tuttavia si vuole ottenere la risoluzione dei nomi per le risorse distribuite nella rete virtuale, è necessario specificare un server DNS. Questa impostazione consente di specificare il server DNS da usare per la risoluzione dei nomi nella rete virtuale. Non comporta la creazione di un server DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Creare una subnet del gateway
È necessario creare una subnet del gateway per il gateway VPN. La subnet del gateway contiene gli indirizzi IP usati dai servizi del gateway VPN. Se possibile, creare una subnet di gateway usando un blocco CIDR di tipo /28 o /27. In questo modo gli indirizzi IP disponibili saranno sufficienti a soddisfare eventuali esigenze di configurazione successive per il gateway.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Creare un gateway di rete virtuale

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Creare un gateway di rete locale
Il gateway di rete locale fa riferimento al percorso locale. Le impostazioni specificate per il gateway di rete locale determinano gli spazi degli indirizzi che vengono indirizzati al dispositivo VPN locale.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurare il dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Una connessione VPN da sito a sito

In questo passaggio verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale. Prima di iniziare questa sezione, verificare che la creazione del gateway di rete virtuale e del gateway di rete locale sia stata completata.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verificare la connessione VPN

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi
*  Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).


