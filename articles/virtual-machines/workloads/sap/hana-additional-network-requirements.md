---
title: Requisiti di rete aggiuntivi per SAP HANA su Azure (istanze Large) | Microsoft Docs
description: Requisiti di rete aggiuntivi per SAP HANA su Azure (istanze Large).
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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167600"
---
# <a name="additional-network-requirements"></a>Requisiti di rete aggiuntivi

In alcuni casi potrebbero essere presenti requisiti di rete aggiuntivi come parte della distribuzione di istanze Large di HANA. Questo articolo illustra i requisiti di rete seguenti:
- [Aggiungere più indirizzi IP o subnet ](#adding-more-ip-addresses-or-subnets)
- [Aggiungere vetwork virtuali](#adding-vnets)
- [Aumentare la larghezza di banda del circuito di express route](#increasing-expressroute-circuit-bandwidth)
- [Aggiungere un circuito express route aggiuntivo](#adding-an-additional-expressroute-circuit)
- [Eliminazione di una subnet](#deleting-a-subnet)
- [Eliminazione di una rete virtuale](#deleting-a-vnet)
- [Eliminazione di un circuito express route](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Aggiunta di più indirizzi IP o subnet

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per aggiungere più indirizzi IP o subnet.

In questo caso, è consigliabile aggiungere il nuovo intervallo di indirizzi IP allo spazio di indirizzi della rete virtuale come nuovo intervallo anziché generare un nuovo intervallo aggregato. In entrambi i casi è necessario inviare la modifica a Microsoft per consentire la connettività in uscita dal nuovo intervallo di indirizzi IP alle unità di istanze Large di HANA nel client. È possibile aprire una richiesta di supporto tecnico di Azure per ottenere il nuovo spazio di indirizzi della rete virtuale aggiunto. Dopo aver ricevuto la conferma, eseguire i passaggi successivi.

Per creare una subnet aggiuntiva dal portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network); per creare da PowerShell, vedere [Creare una rete virtuale usando PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Aggiunta di reti virtuali

Dopo aver collegato inizialmente una o più reti virtuali Azure, si potrebbe voler aggiungerne di ulteriori in grado di accedere a SAP HANA in Azure (istanze grandi). In primo luogo, inviare una richiesta di supporto di Azure includendo le informazioni specifiche sulla distribuzione di Azure in questione e gli intervalli di spazi di indirizzi IP per lo spazio di indirizzi della rete virtuale di Azure. SAP HANA nella gestione del servizio Azure indica a quel punto le informazioni necessarie per connettere le reti virtuali aggiuntive ed ExpressRoute. Per ogni rete virtuale è necessaria una chiave di autorizzazione univoca per connettersi al circuito ExpressRoute e alle istanze Large di HANA.

Procedura per aggiungere una nuova rete virtuale di Azure:

1. Completare il primo passaggio del processo introduttivo: vedere la sezione _Creazione di una rete virtuale di Azure_.
2. Completare il secondo passaggio del processo introduttivo: vedere la sezione _Creazione di una subnet del gateway_.
3. Per connettere le reti virtuali aggiuntive al circuito ExpressRoute per istanze Large di HANA, aprire una richiesta di supporto di Azure con informazioni sulla nuova rete virtuale e richiedere una nuova chiave di autorizzazione.
4. Al termine del processo di autorizzazione, usare le informazioni di autorizzazioni ottenute da Microsoft per completare il terzo passaggio del processo introduttivo: vedere la sezione _Collegamento delle reti virtuali_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento della larghezza di banda del circuito ExpressRoute

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene consigliato di aumentare la larghezza di banda del circuito ExpressRoute di SAP HANA in Azure (istanze grandi), creare una richiesta di supporto di Azure. (è possibile richiedere un aumento per una larghezza di banda a circuito singolo fino a un massimo di 10 Gbps). Si riceverà la notifica al termine dell'operazione; non sono necessarie azioni aggiuntive per abilitare la velocità più elevata in Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Aggiunta di un circuito ExpressRoute aggiuntivo

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene comunicata la necessità di un circuito ExpressRoute aggiuntivo, inviare una richiesta di supporto di Azure per creare un nuovo circuito ExpressRoute (e per ottenere le informazioni di autorizzazione necessarie per la connessione). Lo spazio degli indirizzi che verrà usato nelle reti virtuali deve essere definito prima di inviare la richiesta, in modo da poter ricevere l'autorizzazione dal team di gestione dei servizi di SAP HANA in Azure.

Una volta creato il nuovo circuito e completata la configurazione da parte del team di gestione dei servizi di SAP HANA in Azure, si riceverà una notifica contenente le informazioni necessarie per continuare. Seguire i passaggi descritti in precedenza per creare e collegare la nuova rete virtuale a questo circuito aggiuntivo. Non è possibile connettere reti virtuali di Azure a questo circuito aggiuntivo se sono già connesse a un altro circuito ExpressRoute per SAP HANA (istanze Large) nella stessa area di Azure.

## <a name="deleting-a-subnet"></a>Eliminazione di una subnet

Per rimuovere una subnet di rete virtuale, è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando. Se l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure è un intervallo aggregato, non è necessario contattare Microsoft. È tuttavia necessario tenere presente che la rete virtuale sta ancora propagando lo spazio di indirizzi della route BGP che include la subnet eliminata. Se l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure è stato definito come più intervalli di indirizzi IP, uno dei quali era assegnato alla subnet eliminata, è necessario eliminare quest'ultimo dallo spazio di indirizzi della rete virtuale e quindi indicare a SAP HANA in gestione dei servizi di Azure che va rimosso dagli intervalli con cui SAP HANA in Azure (istanze Large) può comunicare.

Per eliminare una subnet, vedere [Eliminare una subnet](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) per altre informazioni.

## <a name="deleting-a-vnet"></a>Eliminazione di una rete virtuale

Per eliminare una rete virtuale, vedere [Delete a virtual network](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network) (Eliminare una rete virtuale). SAP HANA in gestione dei servizi di Azure consente di rimuovere le autorizzazioni esistenti nel circuito ExpressRoute per SAP HANA in Azure (istanze Large) e di rimuovere l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure per la comunicazione con istanze Large di HANA.

Dopo aver rimosso la rete virtuale, aprire una richiesta di supporto di Azure per specificare gli intervalli di spazi di indirizzi IP da rimuovere.

Per garantire una rimozione completa, rimuovere gli elementi seguenti:

- Connessione ExpressRoute, gateway di rete virtuale, indirizzo IP pubblico del gateway di rete virtuale e rete virtuale

## <a name="deleting-an-expressroute-circuit"></a>Eliminazione di un circuito ExpressRoute

Per rimuovere un circuito ExpressRoute aggiuntivo per SAP HANA in Azure (istanze Large), aprire una richiesta di supporto di Azure con SAP HANA in Azure Service Management e richiedere l'eliminazione del circuito. È possibile eliminare o mantenere la rete virtuale nella sottoscrizione di Azure in base alle necessità. Tuttavia, è necessario eliminare la connessione tra il circuito ExpressRoute per istanze Large di HANA e il gateway di rete virtuale collegato.

Se si desidera rimuovere una rete virtuale, seguire le linee guida indicate in Eliminazione di una rete virtuale nella sezione precedente.

**Passaggi successivi**

- Consultare [Come installare e configurare SAP HANA (istanze Large) su Azure](hana-installation.md).
