---
title: Azure VMware Solution by CloudSimple - Elenco di controllo della rete
description: Elenco di controllo per l'allocazione di CIDR di rete nella soluzione Azure VMware di CloudSimpleChecklist for allocating network CIDR on Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025011"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Prerequisiti di rete per Azure VMware Solution by CloudSimple

Azure VMware Solution by CloudSimple offre un ambiente cloud privato VMware accessibile per utenti e applicazioni da ambienti locali, dispositivi gestiti dall'organizzazione e risorse di Azure.Azure VMware Solution by CloudSimple offers a VMware private cloud environment that's accessible for users and applications from on-premises environments, enterprise-managed devices, and Azure resources. La connettività viene fornita tramite servizi di rete come VPN e connessioni Di Azure ExpressRoute.The connectivity is delivered through networking services such as VPNns and Azure ExpressRoute connections. Alcuni di questi servizi di rete richiedono di specificare intervalli di indirizzi di rete per l'abilitazione dei servizi. 

Nelle tabelle di questo articolo viene descritto il set di intervalli di indirizzi e i servizi corrispondenti che utilizzano gli indirizzi specificati. Alcuni degli indirizzi sono obbligatori e altri dipendono dai servizi che si desidera distribuire. Questi spazi di indirizzi non devono sovrapporsi a subnet locali, subnet di rete virtuale di Azure o subnet del carico di lavoro CloudSimple pianificate.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Intervalli di indirizzi di rete necessari per la creazione di un cloud privatoNetwork address ranges required for creating a private cloud

Durante la creazione di un servizio CloudSimple e di un cloud privato, è necessario rispettare gli intervalli CIDR (Classless Routing) di rete specificati, come indicato di seguito.

| Nome/utilizzato per     | Descrizione                                                                                                                            | Intervallo di indirizzi            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Obbligatorio per i servizi perimetrali (gateway VPN).  Questo CIDR è necessario durante la creazione del servizio CloudSimple e deve provenire dallo spazio RFC 1918. | /28                      |
| CIDR vSphere/vSAN | Obbligatorio per le reti di gestione VMware. Questo CIDR deve essere specificato durante la creazione del cloud privato.                                    | /24 o /23 o /22 o /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per la connessione di rete di Azure a una rete localeNetwork address range required for Azure network connection to an on-premises network

La connessione da una [rete locale alla rete cloud privata tramite ExpressRoute](on-premises-connection.md) stabilisce una connessione Di copertura globale.  La connessione usa il protocollo BGP (Border Gateway Protocol) per scambiare route tra la rete locale, la rete cloud privata e le reti di Azure.The connection uses Border Gateway Protocol (BGP) to exchange routes between your on-premises network, your private cloud network, and your Azure networks.

| Nome/utilizzato per             | Descrizione                                                                                                                                                                             | Intervallo di indirizzi |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Obbligatorio quando si usa la copertura globale ExpressRoute per la connettività locale. Questo CIDR deve essere fornito quando viene effettuata una richiesta di connessione Global Reach tramite un ticket di supporto. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per l'utilizzo di una connessione VPN da sito a sito a una rete locale

La connessione da una [rete locale alla rete cloud privata tramite vpn](vpn-gateway.md) da sito a sito richiede gli indirizzi IP, la rete locale e gli identificatori seguenti. 

| Intervallo indirizzo/indirizzo | Descrizione                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer IP               | Indirizzo IP pubblico del gateway VPN locale. Necessario per stabilire una connessione VPN da sito a sito tra un data center locale e l'area del servizio CloudSimple. Questo indirizzo IP è necessario durante la creazione del gateway VPN da sito a sito.                                         |
| Identificatore peer       | Identificatore peer del gateway VPN locale. Questo è in genere lo stesso **IP peer**.  Se nel gateway VPN locale viene specificato un identificatore univoco, è necessario specificare l'identificatore.  L'ID peer è necessario durante la creazione del gateway VPN da sito a sito.   |
| Reti locali   | Prefissi locali che richiedono l'accesso alle reti CloudSimple nell'area.  Includere tutti i prefissi di una rete locale che accederà alla rete CloudSimple, inclusa la rete client da cui gli utenti accederanno alla rete.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervallo di indirizzi di rete necessario per l'utilizzo di connessioni VPN da punto a sito

Una connessione VPN da punto a sito consente l'accesso alla rete CloudSimple da un computer client.  [Per configurare](vpn-gateway.md)la VPN da punto a sito, è necessario specificare il seguente intervallo di indirizzi di rete.

| Intervallo indirizzo/indirizzo | Descrizione                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subnet client         | Gli indirizzi DHCP vengono forniti dalla subnet client quando ci si connette utilizzando una VPN da punto a sito. Questa subnet è necessaria durante la creazione di un gateway VPN da punto a sito in un portale CloudSimple.This subnet is required while you're creating a point-to-site VPN gateway on a CloudSimple portal.  La rete è divisa in due subnet; uno per la connessione UDP e l'altro per le connessioni TCP. |

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione del firewall locale per l'accesso al cloud privato](on-premises-firewall-configuration.md)
* [Guida introduttiva - Creare un servizio CloudSimpleQuickstart - Create a CloudSimple service](quickstart-create-cloudsimple-service.md)
* [Guida introduttiva- Configurare un cloud privato](quickstart-create-private-cloud.md)
* Altre informazioni sulle connessioni di rete di [AzureLearn](cloudsimple-azure-network-connection.md) more about Azure network connections
* Ulteriori informazioni sui [gateway VPN](cloudsimple-vpn-gateways.md)
