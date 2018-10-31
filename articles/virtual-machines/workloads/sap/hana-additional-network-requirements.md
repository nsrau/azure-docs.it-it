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
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392999"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisiti di rete aggiuntivi per istanze Large

Nell'ambito della distribuzione di istanze Large di SAP HANA in Azure, è possibile che si presentino requisiti di rete aggiuntivi.

## <a name="add-more-ip-addresses-or-subnets"></a>Aggiungere più indirizzi IP o subnet

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per aggiungere più indirizzi IP o subnet.

Aggiungere il nuovo intervallo di indirizzi IP allo spazio di indirizzi della rete virtuale come nuovo intervallo anziché generare un nuovo intervallo aggregato. Inviare la modifica a Microsoft. In questo modo è possibile connettersi dal nuovo intervallo di indirizzi IP alle unità di istanze Large di HANA nel client. È possibile aprire una richiesta di supporto tecnico di Azure per ottenere il nuovo spazio di indirizzi della rete virtuale aggiunto. Dopo aver ricevuto la conferma, eseguire i passaggi successivi.

Per creare una subnet aggiuntiva dal portale di Azure, vedere [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Per crearne una da PowerShell, vedere [Creare una rete virtuale usando PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Aggiungere reti virtuali

Dopo aver inizialmente connesso una o più reti virtuali di Azure, è possibile che se ne voglia connettere altre in grado di accedere a SAP HANA in Azure (istanze Large). Per prima cosa, inviare una richiesta di supporto tecnico di Azure. Nella richiesta, includere le informazioni specifiche che identificano la particolare distribuzione di Azure. Includere anche l'intervallo dello spazio di indirizzi IP o gli intervalli dello spazio di indirizzi della rete virtuale di Azure. Il team di gestione dei servizi SAP HANA in Microsoft fornisce quindi le informazioni necessarie per connettere le reti virtuali aggiuntive e Azure ExpressRoute. Per ogni rete virtuale è necessaria una chiave di autorizzazione univoca per connettersi al circuito ExpressRoute e alle istanze Large di HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentare la larghezza di banda del circuito ExpressRoute

Consultarsi con il team di gestione dei servizi SAP HANA in Microsoft. Se viene consigliato di aumentare la larghezza di banda del circuito ExpressRoute di SAP HANA in Azure (istanze Large), creare una richiesta di supporto di Azure (è possibile richiedere un aumento per una larghezza di banda a circuito singolo fino a un massimo di 10 Gbps). Si riceverà una notifica al termine dell'operazione; non sono necessarie azioni aggiuntive per abilitare la velocità più elevata in Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Aggiungere un circuito ExpressRoute supplementare

Consultarsi con il team di gestione dei servizi SAP HANA in Microsoft. Se viene consigliato di aggiungere un circuito ExpressRoute supplementare, creare una richiesta di supporto di Azure (anche per ottenere le informazioni di autorizzazione necessarie per connettersi al nuovo circuito). Prima di creare la richiesta, tuttavia, è necessario definire lo spazio di indirizzi usato nelle reti virtuali. A questo punto, il team di gestione dei servizi SAP HANA in Microsoft può concedere l'autorizzazione.

Una volta creato il nuovo circuito e completata la configurazione da parte del team di gestione dei servizi di SAP HANA in Microsoft, si riceverà una notifica contenente le informazioni necessarie per continuare. Non è possibile connettere reti virtuali di Azure a questo circuito aggiuntivo se sono già connesse a un altro circuito ExpressRoute per SAP HANA (istanze Large) nella stessa area di Azure.

## <a name="delete-a-subnet"></a>Eliminare una subnet

Per rimuovere una subnet di rete virtuale, è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Se l'intervallo di indirizzi IP o lo spazio di indirizzi della rete virtuale di Azure è un intervallo aggregato, non è necessario contattare Microsoft. Tenere presente, tuttavia, che la rete virtuale sta ancora propagando lo spazio di indirizzi della route BGP che include la subnet eliminata. Se l'intervallo di indirizzi o lo spazio di indirizzi della rete virtuale di Azure è stato definito come più intervalli di indirizzi IP e uno di essi era assegnato alla subnet eliminata, assicurarsi di eliminarlo dallo spazio di indirizzi della rete virtuale. Comunicare quindi al team di gestione dei servizi di SAP HANA in Microsoft di rimuoverlo anche dagli intervalli con cui SAP HANA in Azure (istanze Large) può comunicare.

Per altre informazioni, vedere [Eliminare una subnet](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Eliminare una rete virtuale

Per informazioni, vedere [Eliminare una rete virtuale](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Il team di gestione dei servizi di SAP HANA in Microsoft rimuove le autorizzazioni esistenti nel circuito ExpressRoute per SAP HANA in Azure (istanze Large) e l'intervallo di indirizzi IP o lo spazio di indirizzi della rete virtuale di Azure per le comunicazioni con istanze Large di HANA.

Dopo aver rimosso la rete virtuale, aprire una richiesta di supporto di Azure per indicare gli intervalli degli spazi di indirizzi IP da rimuovere.

Per garantire una rimozione completa, eliminare la connessione ExpressRoute, il gateway di rete virtuale, l'indirizzo IP pubblico del gateway di rete virtuale e la rete virtuale.

## <a name="delete-an-expressroute-circuit"></a>Eliminare un circuito ExpressRoute

Per rimuovere un circuito ExpressRoute aggiuntivo di SAP HANA in Azure (istanze Large), aprire una richiesta di supporto di Azure con il team di gestione dei servizi di SAP HANA in Microsoft e richiedere l'eliminazione del circuito. È possibile eliminare o mantenere la rete virtuale nella sottoscrizione di Azure in base alle necessità. È necessario, tuttavia, eliminare la connessione tra il circuito ExpressRoute per istanze Large di HANA e il gateway di rete virtuale collegato.

## <a name="next-steps"></a>Passaggi successivi

- [Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure](hana-installation.md)
