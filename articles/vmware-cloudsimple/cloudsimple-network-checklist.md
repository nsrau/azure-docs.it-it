---
title: Soluzione VMware di Azure di CloudSimple-elenco di controllo di rete
description: Elenco di controllo per l'allocazione di CIDR di rete in una soluzione VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a83112c1555d50976e5a20106b877f5c0099a5f3
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178150"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Prerequisiti di rete per la soluzione VMware di Azure di CloudSimple

La soluzione VMware di Azure di CloudSimple offre un ambiente di cloud privato VMware accessibile per utenti e applicazioni da ambienti locali, dispositivi gestiti dall'organizzazione e risorse di Azure. La connettività viene fornita tramite servizi di rete, ad esempio VPN e connessioni Azure ExpressRoute. Per alcuni di questi servizi di rete è necessario specificare gli intervalli di indirizzi di rete per l'abilitazione dei servizi. 

Le tabelle in questo articolo descrivono il set di intervalli di indirizzi e i servizi corrispondenti che usano gli indirizzi specificati. Alcuni indirizzi sono obbligatori e alcuni dipendono dai servizi che si desidera distribuire. Questi spazi di indirizzi non devono sovrapporsi ad alcuna subnet locale, subnet della rete virtuale di Azure o subnet del carico di lavoro CloudSimple pianificato.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Intervalli di indirizzi di rete necessari per la creazione di un cloud privato

Durante la creazione di un servizio CloudSimple e di un cloud privato, è necessario essere conformi agli intervalli CIDR (Inter-Domain Routing) senza classe di rete specificati, come indicato di seguito.

| Nome/utilizzato per     | Descrizione                                                                                                                            | Intervallo di indirizzi            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR del gateway      | Obbligatorio per servizi perimetrali (gateway VPN).  Questo CIDR è necessario durante la creazione del servizio CloudSimple e deve provenire dallo spazio RFC 1918. | /28                      |
| CIDR vSphere/rete VSAN | Obbligatorio per le reti di gestione VMware. Questo CIDR deve essere specificato durante la creazione del cloud privato.                                    | /24 o/23 o/22 o/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per la connessione di rete di Azure a una rete locale

La connessione da una [rete locale alla rete cloud privata tramite ExpressRoute](on-premises-connection.md) stabilisce una connessione copertura globale.  La connessione USA Border Gateway Protocol (BGP) per scambiare le route tra la rete locale, la rete cloud privata e le reti di Azure.

| Nome/utilizzato per             | Descrizione                                                                                                                                                                             | Intervallo di indirizzi |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| CIDR peering ExpressRoute | Obbligatorio quando si usa ExpressRoute Copertura globale per la connettività locale. Questo CIDR deve essere specificato quando viene effettuata una richiesta di connessione Copertura globale tramite un ticket di supporto. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per l'uso di una connessione VPN da sito a sito a una rete locale

Per la connessione da una [rete locale alla rete cloud privata con VPN da sito a sito](vpn-gateway.md) sono necessari i seguenti indirizzi IP, la rete locale e gli identificatori. 

| Indirizzo/intervallo di indirizzi | Descrizione                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IP peer               | Indirizzo IP pubblico del gateway VPN locale. Necessaria per stabilire una connessione VPN da sito a sito tra un Data Center locale e l'area del servizio CloudSimple. Questo indirizzo IP è obbligatorio durante la creazione del gateway VPN da sito a sito.                                         |
| Identificatore peer       | Identificatore peer del gateway VPN locale. Questo è in genere uguale all' **indirizzo IP peer**.  Se nel gateway VPN locale viene specificato un identificatore univoco, è necessario specificare l'identificatore.  Il peer ID è obbligatorio durante la creazione del gateway VPN da sito a sito.   |
| Reti locali   | Prefissi locali che richiedono l'accesso alle reti CloudSimple nell'area.  Includere tutti i prefissi di una rete locale che accedono alla rete CloudSimple, inclusa la rete client da cui gli utenti accedono alla rete.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervallo di indirizzi di rete necessario per l'uso di connessioni VPN da punto a sito

Una connessione VPN da punto a sito consente l'accesso alla rete CloudSimple da un computer client.  [Per configurare la VPN da punto a sito](vpn-gateway.md), è necessario specificare l'intervallo di indirizzi di rete seguente.

| Indirizzo/intervallo di indirizzi | Descrizione                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subnet client         | Gli indirizzi DHCP vengono forniti dalla subnet client quando ci si connette tramite una VPN da punto a sito. Questa subnet è necessaria durante la creazione di un gateway VPN da punto a sito in un portale di CloudSimple.  La rete è divisa in due subnet. una per la connessione UDP e l'altra per le connessioni TCP. |

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva-creare un servizio CloudSimple](quickstart-create-cloudsimple-service.md)
* [Guida introduttiva-configurare un cloud privato](quickstart-create-private-cloud.md)
* Altre informazioni sulle [connessioni di rete di Azure](cloudsimple-azure-network-connection.md)
* Altre informazioni sui [gateway VPN](cloudsimple-vpn-gateways.md)
