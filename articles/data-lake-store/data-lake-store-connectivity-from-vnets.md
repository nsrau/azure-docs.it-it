---
title: Connettersi ad Azure Data Lake Storage Gen1 da reti virtuali | Microsoft Docs
description: Connettersi ad Azure Data Lake Storage Gen1 da reti virtuali di Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878061"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Accedere ad Azure Data Lake Storage Gen1 dalle macchine virtuali di una rete virtuale di Azure
Azure Data Lake Storage Gen1 è un servizio PaaS eseguito su indirizzi IP Internet pubblici. Qualunque server in grado di connettersi alla rete Internet pubblica può generalmente connettersi anche agli endpoint di Azure Data Lake Storage Gen1. Per impostazione predefinita, tutte le macchine virtuali delle reti virtuali di Azure possono accedere a Internet e quindi anche ad Azure Data Lake Storage Gen1. Tuttavia, è possibile configurare le macchine virtuali di una rete virtuale per l'accesso a Internet. Anche per queste macchine virtuali, l'accesso ad Azure Data Lake Storage Gen1 è limitato. L'accesso a Internet pubblico per le macchine virtuali delle reti virtuali di Azure può essere bloccato tramite uno degli approcci seguenti:

* Configurando gruppi di sicurezza di rete
* Configurando route definite dall'utente
* Scambiando le route tramite il protocollo BGP (il protocollo di routing dinamico standard del settore), quando l'accesso a Internet è bloccato dall'uso di ExpressRoute

In questo articolo si apprenderà come abilitare l'accesso ad Azure Data Lake Storage Gen1 dalle macchine virtuali di Azure, che sono state limitate per accedere alle risorse usando uno dei tre metodi descritti in precedenza.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Abilitare la connettività ad Azure Data Lake Storage Gen1 dalle macchine virtuali con connettività limitata
Per accedere ad Azure Data Lake Storage Gen1 da queste macchine virtuali, è necessario configurarle in modo da accedere all'indirizzo IP per l'area in cui è disponibile l'account di Azure Data Lake Storage Gen1. Gli indirizzi IP delle aree degli account Data Lake Storage Gen1 sono identificabili risolvendo i nomi DNS degli account in uso (`<account>.azuredatalakestore.net`). Per risolvere i nomi DNS degli account in uso, è possibile usare strumenti come **nslookup**. Aprire il prompt dei comandi nel computer ed eseguire il comando seguente:

    nslookup mydatastore.azuredatalakestore.net

L'output sarà simile a quanto segue. Il valore rispetto alla proprietà **Indirizzo** è l'indirizzo IP associato all'account Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di NSG
Quando l'accesso a Internet è bloccato tramite una regola NSG, è possibile creare un altro NSG che consenta di accedere all'indirizzo IP di Data Lake Storage Gen1. Per altre informazioni sulle regole NSG, vedere [Network security groups overview](../virtual-network/security-overview.md) (Panoramica sui gruppi di sicurezza di rete). Per istruzioni su come creare un NSG, vedere [Creare un gruppo di sicurezza di rete tramite il portale di Azure](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di UDR o ExpressRoute
Quando l'accesso a Internet è bloccato da route di tipo UDR o scambiate tramite BGP, è necessario configurare una route speciale in modo che le macchine virtuali in queste subnet possano accedere agli endpoint di Data Lake Storage Gen1. Per altre informazioni, vedere [User-defined routes overview](../virtual-network/virtual-networks-udr-overview.md) (Panoramica delle route definite dall'utente). Per istruzioni sulla creazione di UDR, vedere [Creare route definite dall'utente in Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Abilitare la connettività dalle macchine virtuali con ExpressRoute
Quando viene configurato un circuito ExpressRoute, i server locali possono accedere a Data Lake Storage Gen1 tramite peering pubblico. Per altre informazioni sulla configurazione di ExpressRoute per il peering pubblico, consultare le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

