---
title: Rete virtuale per i servizi di Azure | Microsoft Docs
description: Informazioni sui vantaggi della distribuzione delle risorse in una rete virtuale. Le risorse nelle reti virtuali possono comunicare tra loro e con le risorse locali senza che il traffico attraversi Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 36ff77a4205132d1dcc3b743f11791fc5e98e9e7
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-integration-for-azure-services"></a>Integrazione della rete virtuale per i servizi di Azure

L'integrazione dei servizi di Azure in una rete virtuale di Azure (VNet) assicura che i servizi siano accessibili privatamente alle istanze distribuite in tale rete virtuale.

È possibile integrare i servizi di Azure con la rete virtuale tramite le opzioni seguenti:
- Distribuendo direttamente istanze dedicate del servizio in una rete virtuale. Le istanze dedicate di questi servizi sono accessibili privatamente all'interno della rete virtuale e in locale.
- Estendendo una rete virtuale al servizio, tramite endpoint di servizio che garantiscono l'integrazione delle singole risorse del servizio nella rete virtuale in modo sicuro.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuire servizi di Azure in reti virtuali

È possibile comunicare con la maggior parte delle risorse di Azure su Internet attraverso indirizzi IP pubblici. Quando si distribuiscono i servizi di Azure in una [rete virtuale](virtual-networks-overview.md), è possibile comunicare con le risorse dei servizi privatamente, tramite indirizzi IP privati.


![Servizi distribuiti in una rete virtuale](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La distribuzione di servizi all'interno di una rete virtuale offre le funzionalità seguenti:

- Le risorse all'interno della rete virtuale possono comunicare tra loro privatamente, tramite indirizzi IP privati, ad esempio trasferendo direttamente i dati tra HDInsight e SQL Server in esecuzione in una macchina virtuale, all'interno della rete virtuale.
- Le risorse locali possono accedere alle risorse in una rete virtuale usando indirizzi IP privati attraverso [una connessione VPN da sito a sito (Gateway VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Le reti virtuali possono essere [associate tramite peering](virtual-network-peering-overview.md) per consentire alle risorse delle reti virtuali di comunicare tra loro, usando indirizzi IP privati.
- Le istanze dei servizi in una rete virtuale sono completamente gestite dal servizio di Azure, per monitorare l'integrità delle istanze e fornire la scalabilità richiesta in base al carico.
- Le istanze dei servizi vengono distribuite in una subnet dedicata nella rete virtuale del cliente. L'accesso in ingresso e in uscita dovrebbe essere aperto tramite gruppi di sicurezza di rete (NSG) per la subnet, in base alle indicazioni fornite dai servizi.


### <a name="list-of-services-that-can-be-deployed-into-a-virtual-network"></a>Elenco di servizi distribuibili in una rete virtuale

Ogni servizio distribuito direttamente in una rete virtuale ha requisiti specifici per il routing e i tipi di traffico consentiti in ingresso e in uscita dalle subnet. Fare riferimento ai collegamenti seguenti per maggiori dettagli: 
 
- Macchine virtuali: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ambiente del servizio app](../app-service-web/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gestione API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione (interno)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Motore del servizio contenitore di Azure](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): il servizio contenitore di Azure crea una rete virtuale predefinita. È possibile creare una rete virtuale personalizzata da usare con il [motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): solo per rete virtuale (versione classica)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): solo per rete virtuale (versione classica)
- [Servizi cloud](https://msdn.microsoft.com/library/azure/jj156091): solo per rete virtuale (versione classica)

È possibile distribuire un [Azure Load Balancer interno](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per bilanciare il carico di molte risorse dell'elenco precedente. In alcuni casi il servizio crea e distribuisce automaticamente un servizio di bilanciamento del carico quando si crea una risorsa.

## <a name="service-endpoints-for-azure-services"></a>Endpoint di servizio per i servizi di Azure

Alcuni servizi di Azure non possono essere distribuiti nelle reti virtuali. È possibile limitare l'accesso ad alcune delle risorse dei servizi solo a subnet specifiche della rete virtuale, se si vuole, abilitando un endpoint del servizio di rete virtuale. Per altre informazioni, vedere [Virtual Network service endpoints](virtual-network-service-endpoints-overview.md) (Endpoint del servizio Rete virtuale).

Attualmente gli endpoint di servizio sono supportati per i servizi seguenti: 
- Archiviazione di Azure: [Associazione di account di archiviazione di Azure a reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- Database SQL di Azure: [Associazione del database SQL di Azure a reti virtuali](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integrazione della rete virtuale tra più servizi di Azure

È possibile distribuire un servizio di Azure in una subnet all'interno di una rete virtuale e integrare le risorse del servizio critico in tale subnet in modo sicuro. 

Ad esempio, è possibile distribuire HDInsight nella rete virtuale e integrare in modo sicuro un account di archiviazione nella subnet di HDInsight.






