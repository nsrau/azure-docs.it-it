---
title: Connettersi ad Azure Data Lake Store da reti virtuali | Microsoft Docs
description: Connettersi ad Azure Data Lake Store da reti virtuali di Azure
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9bd7a76ef644df3db72ce95a3ef89a04bec1381f


---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Accedere ad Azure Data Lake Store dalle macchine virtuali di una rete virtuale di Azure
Azure Data Lake Store è un servizio PaaS eseguito su indirizzi IP Internet pubblici. Qualunque server in grado di connettersi alla rete Internet pubblica può in genere connettersi anche agli endpoint di Azure Data Lake Store. Per impostazione predefinita, tutte le macchine virtuali delle reti virtuali di Azure possono accedere a Internet e quindi anche ad Azure Data Lake Store. Tuttavia, è possibile configurare le macchine virtuali di una rete virtuale per l'accesso a Internet. Per queste macchine virtuali, è limitato anche l'accesso ad Azure Data Lake Store. L'accesso a Internet pubblico per le macchine virtuali delle reti virtuali di Azure può essere bloccato tramite uno degli approcci seguenti.

* Configurando gruppi di sicurezza di rete
* Configurando route definite dall'utente
* Scambiando le route tramite il protocollo BGP (il protocollo di routing dinamico standard del settore), quando l'accesso a Internet è bloccato dall'uso di ExpressRoute

Questo articolo spiega come abilitare l'accesso ad Azure Data Lake Store dalle macchine virtuali di Azure con accesso alle risorse limitato a uno dei tre metodi descritti in precedenza.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Abilitare la connettività ad Azure Data Lake Store dalle macchine virtuali con connettività limitata
Per accedere ad Azure Data Lake Store da queste macchine virtuali, è necessario configurarne l'accesso all'indirizzo IP in cui è disponibile l'account di Azure Data Lake Store. Gli indirizzi IP degli account Data Lake Store sono identificabili risolvendo i nomi DNS degli account in uso (`<account>.azuredatalakestore.net`). A questo scopo, è possibile usare strumenti come **nslookup**. Aprire il prompt dei comandi sul computer ed eseguire il comando seguente.

    nslookup mydatastore.azuredatalakestore.net

L'output sarà simile a quanto segue. Il valore della proprietà **Indirizzo** è l'indirizzo IP associato all'account Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di NSG
Quando l'accesso a Internet è bloccato tramite una regola NSG, è possibile creare un altro NSG che consenta di accedere all'indirizzo IP di Data Lake Store. Altre informazioni sulle regole NSG sono disponibili nell'articolo [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per istruzioni su come creare un NSG, vedere [How to manage NSGs using the Azure portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) (Come gestire NSG tramite il Portale di Azure).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Abilitare la connettività da macchine virtuali limitatamente all'uso di UDR o ExpressRoute
Quando l'accesso a Internet è bloccato da route di tipo UDR o scambiate tramite BGP, è necessario configurare una route speciale in modo che le macchine virtuali in queste subnet possano accedere agli endpoint Data Lake Store. Per altre informazioni, vedere [Cosa sono le route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md). Per istruzioni sulla creazione di UDR, vedere [Creare route definite dall'utente in Resource Manager](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Abilitare la connettività dalle macchine virtuali con ExpressRoute
Quando viene configurato un circuito ExpressRoute, i server locali possono accedere a Data Lake Store tramite peering pubblico. Per altre informazioni sulla configurazione di ExpressRoute per il peering pubblico, consultare le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Vedere anche
* [Panoramica di Data Lake Store di Azure](data-lake-store-overview.md)
* [Sicurezza in Azure Data Lake Store](data-lake-store-security-overview.md)




<!--HONumber=Nov16_HO3-->


