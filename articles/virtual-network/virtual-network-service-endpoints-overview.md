---
title: Endpoint servizio di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come abilitare l'accesso diretto alle risorse di Azure da una rete virtuale tramite gli endpoint del servizio.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378683"
---
# <a name="virtual-network-service-endpoints"></a>Endpoint servizio di rete virtuale

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**Disponibile a livello generale**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**Anteprima pubblica**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

Per le notifiche più aggiornate, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Vantaggi principali

Gli endpoint di servizio offrono i vantaggi seguenti:

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Gli endpoint di servizio forniscono il routing ottimale per il traffico di Azure. 

  Gli endpoint instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Microsoft Azure. Mantenendo il traffico nella rete backbone di Azure è possibile continuare a monitorare e verificare il traffico Internet in uscita dalle reti virtuali, tramite il tunneling forzato, senza conseguenze per il traffico del servizio. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Semplice da configurare con un minor overhead di gestione**: non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure attraverso il firewall IP. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>Limitazioni

- La funzionalità è disponibile solo per le reti virtuali distribuite con il modello di distribuzione Azure Resource Manager.
- Gli endpoint vengono abilitati nelle subnet configurate nelle reti virtuali di Azure. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- Per SQL di Azure, un endpoint di servizio si applica solo al traffico del servizio di Azure nell'area della rete virtuale. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. Per altre informazioni, vedere [Aree abbinate di Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account Data Lake Storage Gen1 e consentire l'accesso. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- Un endpoint servizio di rete virtuale fornisce l'identità della rete virtuale al servizio di Azure. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- Il traffico del servizio di Azure da una rete virtuale usa attualmente indirizzi IP pubblici come indirizzi IP di origine. Con gli endpoint di servizio, il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale. Questa opzione consente di accedere ai servizi senza che siano necessari gli indirizzi IP pubblici riservati usati nei firewall IP.

   >[!NOTE]
   > Con gli endpoint di servizio, gli indirizzi IP di origine delle macchine virtuali nella subnet per il traffico del servizio passano da indirizzi IPv4 pubblici a indirizzi IPv4 privati. Dopo questo passaggio, le regole del firewall dei servizi di Azure esistenti che usano indirizzi IP pubblici di Azure smettono di funzionare. Prima di configurare endpoint di servizio, assicurarsi che le regole del firewall dei servizi di Azure consentano questo passaggio. Durante la configurazione degli endpoint di servizio, può anche verificarsi un'interruzione temporanea del traffico dei servizi da questa subnet. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. Se si vuole consentire il traffico dall'ambiente locale, è necessario autorizzare anche gli indirizzi IP pubblici (generalmente NAT) dall'ambiente locale o da ExpressRoute. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Associazione di servizi di Azure a reti virtuali](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configurazione

- Configure service endpoints on a subnet in a virtual network. Gli endpoint funzionano con qualsiasi tipo di istanza di calcolo in esecuzione in tale subnet.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- Per Database SQL di Azure, le reti virtuali devono trovarsi nella stessa area della risorsa del servizio di Azure. Se si usano account di archiviazione di Azure GRS e RA-GRS, l'account principale deve trovarsi nella stessa area della rete virtuale. For all other services, you can secure Azure service resources to virtual networks in any region. 
- La rete virtuale in cui è configurato l'endpoint può trovarsi nella stessa sottoscrizione della risorsa del servizio di Azure o in una sottoscrizione diversa. Per altre informazioni sulle autorizzazioni necessarie per configurare gli endpoint e proteggere i servizi di Azure, vedere la sezione [Provisioning](#provisioning).
- Per i servizi supportati, è possibile associare risorse nuove o esistenti alle reti virtuali tramite gli endpoint di servizio.

### <a name="considerations"></a>Considerazioni

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. Eventuali connessioni TCP aperte per il servizio vengono chiuse durante questo passaggio. Verificare che non siano in esecuzione attività critiche quando si abilita o disabilita un endpoint di servizio per un servizio in una subnet. Assicurarsi anche che le applicazioni possano connettersi automaticamente ai servizi di Azure dopo il cambio di indirizzo IP.

  Il cambio di indirizzo IP interessa solo il traffico del servizio dalla propria rete virtuale. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. Per i servizi di Azure, se sono presenti regole del firewall che usano indirizzi IP pubblici di Azure, queste regole smetteranno di funzionare con il passaggio agli indirizzi privati della rete virtuale.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Gruppi di sicurezza di rete (NGS) con gli endpoint di servizio:
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. Per altre informazioni, vedere [Tag del servizio di Azure per i gruppi di sicurezza di rete](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenari

- **Reti virtuali con peering, connesse o multiple**: per associare i servizi di Azure a più subnet in una o più reti virtuali, è possibile abilitare gli endpoint di servizio in modo indipendente in ognuna di queste subnet e associare le risorse dei servizi di Azure a tutte queste subnet.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. Sarà quindi possibile applicare gli endpoint di servizio alla subnet in cui è distribuita l'appliance virtuale di rete e associare le risorse del servizio di Azure solo a questa subnet. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. Per altre informazioni, vedere il [traffico in uscita con le appliance virtuali di rete](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. È possibile associare le risorse dei servizi di Azure a subnet di [servizi gestiti](virtual-network-for-azure-services.md), configurando un endpoint di servizio nella subnet del servizio gestito.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registrazione e risoluzione dei problemi

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- Convalida dell'indirizzo IP di origine di qualsiasi richiesta di servizio nella diagnostica del servizio. Tutte le nuove richieste con gli endpoint di servizio mostrano l'indirizzo IP di origine della richiesta come indirizzo IP privato della rete virtuale, assegnato al client che effettua la richiesta dalla rete virtuale. Senza l'endpoint, questo indirizzo sarà un indirizzo IP pubblico di Azure.
- Visualizzazione delle route valide in qualsiasi interfaccia di rete di una subnet. La route per il servizio:
  - Mostra una route predefinita più specifica per l'intervallo dei prefissi di indirizzo di ogni servizio
  - Ha nextHopType con valore *VirtualNetworkServiceEndpoint*
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> La route dell'endpoint di servizio sostituisce qualsiasi route BGP o UDR per la corrispondenza dei prefissi di indirizzo di un servizio di Azure. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Provisioning

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le reti virtuali e le risorse dei servizi di Azure possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). 

## <a name="pricing-and-limits"></a>Prezzi e limiti

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. I criteri degli endpoint di servizio forniscono un controllo granulare dell'accesso per il traffico di rete virtuale verso i servizi di Azure. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Domande frequenti

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Passaggi successivi

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Criteri degli endpoint servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).
- [Modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

