---
title: Azure VMware Solutions (AVS)-elenco di controllo di rete
description: Elenco di controllo per l'allocazione di CIDR di rete in una soluzione VMware di Azure tramite AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025011"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Prerequisiti di rete per la soluzione VMware di Azure con AVS

Azure VMware Solutions (AVS) offre un ambiente di cloud privato VMware accessibile per utenti e applicazioni da ambienti locali, dispositivi gestiti dall'organizzazione e risorse di Azure. La connettività viene fornita tramite servizi di rete, ad esempio VPN e connessioni Azure ExpressRoute. Per alcuni di questi servizi di rete è necessario specificare gli intervalli di indirizzi di rete per l'abilitazione dei servizi. 

Le tabelle in questo articolo descrivono il set di intervalli di indirizzi e i servizi corrispondenti che usano gli indirizzi specificati. Alcuni indirizzi sono obbligatori e alcuni dipendono dai servizi che si desidera distribuire. Questi spazi di indirizzi non devono sovrapporsi ad alcuna subnet locale, subnet della rete virtuale di Azure o subnet del carico di lavoro AVS pianificato.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Intervalli di indirizzi di rete necessari per la creazione di un cloud privato AVS

Durante la creazione di un servizio AVS e di un cloud privato AVS, è necessario rispettare gli intervalli di rete CIDR (Inter-Domain Routing) specificati, come indicato di seguito.

| Nome/utilizzato per     | Description                                                                                                                            | Intervallo di indirizzi            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR del gateway      | Obbligatorio per servizi perimetrali (gateway VPN). Questo CIDR è necessario durante la creazione del servizio AVS e deve provenire dallo spazio RFC 1918. | /28                      |
| CIDR vSphere/rete VSAN | Obbligatorio per le reti di gestione VMware. Questo CIDR deve essere specificato durante la creazione del cloud privato AVS.                                    | /24 o/23 o/22 o/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per la connessione di rete di Azure a una rete locale

La connessione da una [rete locale alla rete del cloud privato AVS tramite ExpressRoute](on-premises-connection.md) stabilisce una connessione copertura globale. La connessione USA Border Gateway Protocol (BGP) per scambiare le route tra la rete locale, la rete cloud privata AVS e le reti di Azure.

| Nome/utilizzato per             | Description                                                                                                                                                                             | Intervallo di indirizzi |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| CIDR peering ExpressRoute | Obbligatorio quando si usa ExpressRoute Copertura globale per la connettività locale. Questo CIDR deve essere specificato quando viene effettuata una richiesta di connessione Copertura globale tramite un ticket di supporto. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Intervallo di indirizzi di rete necessario per l'uso di una connessione VPN da sito a sito a una rete locale

La connessione da una [rete locale alla rete del cloud privato AVS con la VPN da sito a sito](vpn-gateway.md) richiede i seguenti indirizzi IP, la rete locale e gli identificatori. 

| Indirizzo/intervallo di indirizzi | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IP peer               | Indirizzo IP pubblico del gateway VPN locale. Necessaria per stabilire una connessione VPN da sito a sito tra un Data Center locale e l'area del servizio AVS. Questo indirizzo IP è obbligatorio durante la creazione del gateway VPN da sito a sito.                                         |
| Identificatore peer       | Identificatore peer del gateway VPN locale. Questo è in genere uguale all' **indirizzo IP peer**.  Se nel gateway VPN locale viene specificato un identificatore univoco, è necessario specificare l'identificatore.  Il peer ID è obbligatorio durante la creazione del gateway VPN da sito a sito.   |
| Reti locali   | Prefissi locali che richiedono l'accesso alle reti AVS nell'area.  Includere tutti i prefissi di una rete locale che accedono alla rete AVS, inclusa la rete client da cui gli utenti accedono alla rete.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervallo di indirizzi di rete necessario per l'uso di connessioni VPN da punto a sito

Una connessione VPN da punto a sito consente di accedere alla rete AVS da un computer client. [Per configurare la VPN da punto a sito](vpn-gateway.md), è necessario specificare l'intervallo di indirizzi di rete seguente.

| Indirizzo/intervallo di indirizzi | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subnet client         | Gli indirizzi DHCP vengono forniti dalla subnet client quando ci si connette tramite una VPN da punto a sito. Questa subnet è necessaria durante la creazione di un gateway VPN da punto a sito in un portale AVS. La rete è divisa in due subnet, una per la connessione UDP e l'altra per le connessioni TCP. |

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione del firewall locale per l'accesso al cloud privato AVS](on-premises-firewall-configuration.md)
* [Guida introduttiva-creare un servizio AVS](quickstart-create-cloudsimple-service.md)
* [Guida introduttiva-configurare un cloud privato AVS](quickstart-create-private-cloud.md)
* Altre informazioni sulle [connessioni di rete di Azure](cloudsimple-azure-network-connection.md)
* Altre informazioni sui [gateway VPN](cloudsimple-vpn-gateways.md)
