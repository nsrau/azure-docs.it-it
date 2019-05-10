---
title: Rete virtuale per servizi di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sui vantaggi della distribuzione delle risorse in una rete virtuale. Le risorse nelle reti virtuali possono comunicare tra loro e con le risorse locali senza che il traffico attraversi Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409498"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrazione della rete virtuale per i servizi di Azure

L'integrazione dei servizi di Azure in una rete virtuale di Azure consente di accedere privatamente ai servizi dalle macchine virtuali o da risorse di calcolo nella rete virtuale.
È possibile integrare i servizi di Azure nella rete virtuale con le opzioni seguenti:
- Distribuendo istanze dedicate del servizio in una rete virtuale. I servizi sono accessibili privatamente all'interno della rete virtuale e da reti locali.
- Estendendo una rete virtuale al servizio, tramite endpoint di servizio che consentono la protezione delle singole risorse del servizio nella rete virtuale.

Per integrare più servizi di Azure nella rete virtuale, è possibile combinare uno o più dei criteri precedenti. Ad esempio, è possibile distribuire HDInsight nella rete virtuale e integrare in modo sicuro un account di archiviazione nella subnet di HDInsight tramite gli endpoint di servizio.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuire servizi di Azure in reti virtuali

Quando si distribuiscono i servizi di Azure in una [rete virtuale](virtual-networks-overview.md), è possibile comunicare con le risorse dei servizi privatamente, tramite indirizzi IP privati.

![Servizi distribuiti in una rete virtuale](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La distribuzione di servizi all'interno di una rete virtuale offre le funzionalità seguenti:

- Le risorse all'interno della rete virtuale possono comunicare tra loro privatamente, tramite indirizzi IP privati, ad esempio trasferendo direttamente i dati tra HDInsight e SQL Server in esecuzione in una macchina virtuale, nella rete virtuale.
- Le risorse locali possono accedere alle risorse in una rete virtuale usando indirizzi IP privati su una [VPN da sito a sito (gateway VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Le reti virtuali possono essere [associate tramite peering](virtual-network-peering-overview.md) per consentire alle risorse delle reti virtuali di comunicare tra loro, usando indirizzi IP privati.
- Le istanze del servizio in una rete virtuale sono in genere è completamente gestite dal servizio di Azure. Ciò include il monitoraggio dell'integrità delle risorse e il ridimensionamento con il carico.
- Le istanze dei servizi vengono distribuite in una subnet in una rete virtuale. Connessioni in entrata e l'accesso alla rete in uscita per la subnet deve essere consentito tramite [gruppi di sicurezza di rete](security-overview.md#network-security-groups), per indicazioni fornite dal servizio.
- Inoltre, determinati servizi impongono restrizioni sulla subnet che vengono distribuiti in a, limitando l'applicazione di criteri, le route o combinare le macchine virtuali e le risorse del servizio all'interno della stessa subnet. Controllare con ogni servizio sulle restrizioni specifiche perché queste potrebbero cambiare nel corso del tempo. Esempi di tali servizi sono istanze di contenitore di file di Azure NetApp HSM dedicati, Azure, servizio App. 
- Facoltativamente, i servizi potrebbero richiedere una [subnet delegata](virtual-network-manage-subnet.md#add-a-subnet) come identificatore esplicito del fatto che una subnet possa ospitare un servizio specifico. Mediante la delega, servizi di ottengono le autorizzazioni esplicite per creare le risorse specifiche del servizio nella subnet di delegato.
- Vedere un esempio di una risposta dell'API REST su un [rete virtuale con una subnet delegata](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). Un elenco completo di servizi che usano il modello di delegato subnet può essere ottenuto tramite il [deleghe disponibili](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Servizi distribuibili in una rete virtuale

|Category|Service| Subnet Dedicated¹
|-|-|-|
| Calcolo | Macchine virtuali: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Servizio cloud](https://msdn.microsoft.com/library/azure/jj156091): solo rete virtuale (classico)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| N. <br/> N. <br/> N. <br/> No²
| Rete | [Gateway applicazione (WAF)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall di Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Appliance virtuali di rete](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Sì <br/> Sì <br/> Sì <br/> N.
|Dati|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sì <br/> Sì <br/> 
|Analisi | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identità | [Servizi di dominio Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |N. <br/>
| Contenitori | [Servizio Azure Kubernetes](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Istanza di contenitore di Azure](https://www.aka.ms/acivnet)<br/>[Motore del servizio Azure Container](https://github.com/Azure/acs-engine) con il [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI della Rete virtuale di Azure|No²<br/> Sì <br/><br/> N.
| Web | [Gestione API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente del servizio app](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sì <br/> Sì <br/> Sì
| Ospitato | [Modulo di protezione hardware dedicato di Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sì <br/> Sì <br/>
| | |

¹ 'Dedicati' implica che solo risorse specifiche del servizio possono essere distribuite in questa subnet e non possono essere combinate con cliente VM/VMSSs <br/> ² consigliato, ma non un requisito obbligatorio imposte dal servizio.


## <a name="service-endpoints-for-azure-services"></a>Endpoint di servizio per i servizi di Azure

Alcuni servizi di Azure non possono essere distribuiti nelle reti virtuali. È possibile limitare l'accesso ad alcune delle risorse dei servizi solo a subnet specifiche della rete virtuale, se si vuole, abilitando un endpoint servizio di rete virtuale.  Vedere altre informazioni sugli [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) e sui servizi per i quali è possibile abilitare gli endpoint.
