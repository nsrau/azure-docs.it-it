---
title: Connettersi ad Azure Data Lake Store da reti virtuali | Microsoft Docs
description: Connettersi ad Azure Data Lake Store da reti virtuali di Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 4086ef6ce2a95e0467eda61116ac002cf53610b5
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300804"
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Accedere ad Azure Data Lake Store dalle macchine virtuali di una rete virtuale di Azure
Azure Data Lake Store è un servizio PaaS eseguito su indirizzi IP Internet pubblici. Qualunque server in grado di connettersi alla rete Internet pubblica può in genere connettersi anche agli endpoint di Azure Data Lake Store. Per impostazione predefinita, tutte le macchine virtuali delle reti virtuali di Azure possono accedere a Internet e quindi anche ad Azure Data Lake Store. Tuttavia, è possibile configurare le macchine virtuali di una rete virtuale per l'accesso a Internet. Per queste macchine virtuali, è limitato anche l'accesso ad Azure Data Lake Store. L'accesso a Internet pubblico per le macchine virtuali delle reti virtuali di Azure può essere bloccato tramite uno degli approcci seguenti:

* Configurando gruppi di sicurezza di rete
* Configurando route definite dall'utente
* Scambiando le route tramite il protocollo BGP (il protocollo di routing dinamico standard del settore), quando l'accesso a Internet è bloccato dall'uso di ExpressRoute

Questo articolo spiega come abilitare l'accesso ad Azure Data Lake Store dalle macchine virtuali di Azure con accesso alle risorse limitato a uno dei tre metodi descritti in precedenza.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Abilitare la connettività ad Azure Data Lake Store dalle macchine virtuali con connettività limitata
Per accedere ad Azure Data Lake Store da queste macchine virtuali, è necessario configurarne l'accesso all'indirizzo IP in cui è disponibile l'account di Azure Data Lake Store. Gli indirizzi IP degli account Data Lake Store sono identificabili risolvendo i nomi DNS degli account in uso (`<account>.azuredatalakestore.net`). Per risolvere i nomi DNS degli account in uso, è possibile usare strumenti come **nslookup**. Aprire il prompt dei comandi nel computer ed eseguire il comando seguente:

    nslookup mydatastore.azuredatalakestore.net

L'output sarà simile a quanto segue. Il valore della proprietà **Indirizzo** è l'indirizzo IP associato all'account Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di NSG
Quando l'accesso a Internet è bloccato tramite una regola NSG, è possibile creare un altro NSG che consenta di accedere all'indirizzo IP di Data Lake Store. Per altre informazioni sulle regole NSG, vedere [Network security groups overview](../virtual-network/security-overview.md) (Panoramica sui gruppi di sicurezza di rete). Per istruzioni su come creare un NSG, vedere [Creare un gruppo di sicurezza di rete tramite il portale di Azure](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di UDR o ExpressRoute
Quando l'accesso a Internet è bloccato da route di tipo UDR o scambiate tramite BGP, è necessario configurare una route speciale in modo che le macchine virtuali in queste subnet possano accedere agli endpoint Data Lake Store. Per altre informazioni, vedere [User-defined routes overview](../virtual-network/virtual-networks-udr-overview.md) (Panoramica delle route definite dall'utente). Per istruzioni sulla creazione di UDR, vedere [Creare route definite dall'utente in Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Abilitare la connettività dalle macchine virtuali con ExpressRoute
Quando viene configurato un circuito ExpressRoute, i server locali possono accedere a Data Lake Store tramite peering pubblico. Per altre informazioni sulla configurazione di ExpressRoute per il peering pubblico, consultare le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Data Lake Store di Azure](data-lake-store-overview.md)
* [Sicurezza in Azure Data Lake Store](data-lake-store-security-overview.md)

