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
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279663"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrazione della rete virtuale per i servizi di Azure

L'integrazione dei servizi di Azure in una rete virtuale di Azure consente di accedere privatamente ai servizi dalle macchine virtuali o da risorse di calcolo nella rete virtuale.
È possibile integrare i servizi di Azure nella rete virtuale con le opzioni seguenti:
- Distribuendo istanze dedicate del servizio in una rete virtuale. I servizi sono accessibili privatamente all'interno della rete virtuale e da reti locali.
- Utilizzo di [Private Link](../private-link/private-link-overview.md) per accedere privatamente a un'istanza specifica del servizio dalla rete virtuale e dalle reti locali.

È inoltre possibile accedere al servizio tramite endpoint pubblici estendendo una rete virtuale al servizio, tramite endpoint del [servizio.](virtual-network-service-endpoints-overview.md) Gli endpoint del servizio consentono di proteggere le risorse del servizio nella rete virtuale.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuire servizi di Azure in reti virtuali

Quando si distribuiscono i servizi di Azure in una [rete virtuale](virtual-networks-overview.md), è possibile comunicare con le risorse dei servizi privatamente, tramite indirizzi IP privati.

![Servizi distribuiti in una rete virtuale](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La distribuzione di servizi all'interno di una rete virtuale offre le funzionalità seguenti:

- Le risorse all'interno della rete virtuale possono comunicare tra loro privatamente, tramite indirizzi IP privati, ad esempio trasferendo direttamente i dati tra HDInsight e SQL Server in esecuzione in una macchina virtuale, nella rete virtuale.
- Le risorse locali possono accedere alle risorse in una rete virtuale usando indirizzi IP privati su una [VPN da sito a sito (gateway VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Le reti virtuali possono essere [associate tramite peering](virtual-network-peering-overview.md) per consentire alle risorse delle reti virtuali di comunicare tra loro, usando indirizzi IP privati.
- Le istanze del servizio in una rete virtuale sono in genere completamente gestite dal servizio azure.Service instances in a virtual network are typically fully managed by the Azure service. Ciò include il monitoraggio dell'integrità delle risorse e la scalabilità con carico.
- Le istanze dei servizi vengono distribuite in una subnet in una rete virtuale. L'accesso di rete in ingresso e in uscita per la subnet deve essere aperto tramite i gruppi di sicurezza di [rete,](security-overview.md#network-security-groups)in base alle indicazioni fornite dal servizio.
- Alcuni servizi impongono anche restrizioni alla subnet in cui sono distribuiti, limitando l'applicazione di criteri, route o combinando macchine virtuali e risorse del servizio all'interno della stessa subnet. Verificare con ogni servizio le restrizioni specifiche in quanto possono cambiare nel tempo. Esempi di tali servizi sono File di Rete di Azure, HSM dedicato, Istanze del contenitore di Azure, Servizio app.Examples of such services are Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- Facoltativamente, i servizi potrebbero richiedere una [subnet delegata](virtual-network-manage-subnet.md#add-a-subnet) come identificatore esplicito del fatto che una subnet possa ospitare un servizio specifico. Delegando, i servizi ottengono autorizzazioni esplicite per creare risorse specifiche del servizio nella subnet delegata.
- Vedere un esempio di risposta dell'API REST in una [rete virtuale con una subnet delegata.](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet) È possibile ottenere un elenco completo dei servizi che utilizzano il modello di subnet delegata tramite l'API [Delega disponibili.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Servizi distribuibili in una rete virtuale

|Category|Service| Subnet dedicata1
|-|-|-|
| Calcolo | Macchine virtuali: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Set di scalabilità di macchine virtualiVirtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Servizi cloud](https://msdn.microsoft.com/library/azure/jj156091): solo per rete virtuale (versione classica)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| No <br/> No <br/> No <br/> No2
| Rete | [Gateway applicazione (WAF)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall di Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Network Virtual Appliances](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) (Appliance virtuali di rete) | Sì <br/> Sì <br/> Sì <br/> No
|Dati|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Istanza gestita del database SQL di AzureAzure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sì <br/> Sì <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No2 <br/> No2 <br/> 
| Identità | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No <br/>
| Contenitori | [Servizio Azure Kubernetes](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Istanza contenitore di Azure (ACI)Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Motore del servizio Azure Container](https://github.com/Azure/acs-engine) con il [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI della Rete virtuale di Azure|No2<br/> Sì <br/><br/> No
| Web | [Gestione API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente del servizio app](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App per la logica di AzureAzure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sì <br/> Sì <br/> Sì
| Ospitato | [Modulo di protezione hardware dedicato di Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sì <br/> Sì <br/>
| | |

1 "Dedicato" implica che solo le risorse specifiche del servizio possono essere distribuite in questa subnet e non possono essere combinate con VM/VMSS del cliente <br/> 2 È consigliabile disporre di questi servizi in una subnet dedicata, ma non in un requisito obbligatorio imposto dal servizio.
