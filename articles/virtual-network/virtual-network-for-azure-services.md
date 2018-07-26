---
title: Rete virtuale per i servizi di Azure | Microsoft Docs
description: Informazioni sui vantaggi della distribuzione delle risorse in una rete virtuale. Le risorse nelle reti virtuali possono comunicare tra loro e con le risorse locali senza che il traffico attraversi Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c92a986d06deb9f7de10f0682fe46804e6ebb6e7
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069877"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrazione della rete virtuale per i servizi di Azure

L'integrazione dei servizi di Azure in una rete virtuale di Azure consente l'accesso privato dalle istanze di un servizio distribuite nella rete virtuale.

È possibile integrare i servizi di Azure con la rete virtuale con le opzioni seguenti:
- Distribuendo direttamente istanze dedicate del servizio in una rete virtuale. Le istanze dedicate di questi servizi sono accessibili privatamente all'interno della rete virtuale e da reti locali.
- Estendendo una rete virtuale al servizio, tramite endpoint di servizio che consentono la protezione delle singole risorse del servizio nella rete virtuale.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuire servizi di Azure in reti virtuali

È possibile comunicare con la maggior parte delle risorse di Azure su Internet attraverso indirizzi IP pubblici. Quando si distribuiscono i servizi di Azure in una [rete virtuale](virtual-networks-overview.md), è possibile comunicare con le risorse dei servizi privatamente, tramite indirizzi IP privati.

![Servizi distribuiti in una rete virtuale](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La distribuzione di servizi all'interno di una rete virtuale offre le funzionalità seguenti:

- Le risorse all'interno della rete virtuale possono comunicare tra loro privatamente, tramite indirizzi IP privati, ad esempio trasferendo direttamente i dati tra HDInsight e SQL Server in esecuzione in una macchina virtuale, nella rete virtuale.
- Le risorse locali possono accedere alle risorse in una rete virtuale usando indirizzi IP privati su una [VPN da sito a sito (gateway VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Le reti virtuali possono essere [associate tramite peering](virtual-network-peering-overview.md) per consentire alle risorse delle reti virtuali di comunicare tra loro, usando indirizzi IP privati.
- Le istanze dei servizi in una rete virtuale sono completamente gestite dal servizio di Azure, per monitorare l'integrità delle istanze e garantire la scalabilità richiesta in base al carico.
- Le istanze dei servizi vengono distribuite in una subnet dedicata di una rete virtuale. L'accesso alla rete in ingresso e in uscita deve essere consentito tramite [gruppi di sicurezza di rete](security-overview.md#network-security-groups) per la subnet, in base alle indicazioni fornite dai servizi.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Servizi distribuibili in una rete virtuale

Ogni servizio distribuito direttamente in una rete virtuale ha requisiti specifici per il routing e i tipi di traffico consentiti in ingresso e in uscita dalle subnet. Per altre informazioni, vedere: 
 
- Macchine virtuali: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ambiente del servizio app](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gestione API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione (interno)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine) con il [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI di Rete virtuale di Azure
- [Servizi di dominio Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Servizi cloud](https://msdn.microsoft.com/library/azure/jj156091): solo per rete virtuale (versione classica)

È possibile distribuire un [servizio di bilanciamento del carico interno di Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per bilanciare il carico di molte risorse dell'elenco precedente. In alcuni casi, il servizio crea e distribuisce automaticamente un servizio di bilanciamento del carico quando si crea una risorsa.

## <a name="service-endpoints-for-azure-services"></a>Endpoint di servizio per i servizi di Azure

Alcuni servizi di Azure non possono essere distribuiti nelle reti virtuali. È possibile limitare l'accesso ad alcune delle risorse dei servizi solo a subnet specifiche della rete virtuale, se si vuole, abilitando un endpoint del servizio di rete virtuale. Vedere altre informazioni sugli [endpoint del servizio di rete virtuale](virtual-network-service-endpoints-overview.md) e sui servizi per i quali è possibile abilitare gli endpoint.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integrazione della rete virtuale tra più servizi di Azure

È possibile distribuire un servizio di Azure in una subnet all'interno di una rete virtuale e integrare le risorse del servizio critico in tale subnet in modo sicuro. Ad esempio, è possibile distribuire HDInsight nella rete virtuale e integrare in modo sicuro un account di archiviazione nella subnet di HDInsight.





