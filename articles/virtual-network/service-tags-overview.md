---
title: Azure service tags overview
titlesuffix: Azure Virtual Network
description: Learn about service tags. Service tags help minimize complexity of security rule creation.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384153"
---
# <a name="virtual-network-service-tags"></a>Virtual network service tags 
<a name="network-service-tags"></a>

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize complexity of frequent updates on network security rules. You can use service tags to define network access controls on [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. By specifying the service tag name (e.g., **ApiManagement**) in the appropriate *source* or *destination* field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. 

## <a name="available-service-tags"></a>Available service tags
The following table includes all of the service tags available to be used in [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) rules.

The columns indicate whether the tag is:

- Suitable for rules covering inbound or outbound traffic
- Support [regional](https://azure.microsoft.com/regions) scope 
- Usable in [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) rules

By default, service tags reflect the ranges for the entire cloud.  Some service tags also allow more finer-grain control by restricting the corresponding IP ranges to a specified region.  For example while the service tag **Storage** represents Azure Storage for the entire cloud,  **Storage.WestUS** narrows that to only the storage IP address ranges from the WestUS region.  The descriptions of each Service tag below indicates whether they support such regional scope.  



| Tag | Finalità | Can use Inbound or Outbound? | Can be Regional? | Can use with Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Gestione API** | Management traffic for APIM dedicated deployments. | Entrambi | No | SÌ |
| **AppService**    | App Service service. This tag is recommended for outbound security rules to WebApp frontends. | In uscita | SÌ | SÌ |
| **AppServiceManagement** | Management traffic for App Service Environment dedicated deployments. | Entrambi | No | SÌ |
| **AzureActiveDirectory** | Azure Active Directory service. | In uscita | No | SÌ |
| **AzureActiveDirectoryDomainServices** | Management traffic for Azure Active Directory Domain Services dedicated deployments. | Entrambi | No | SÌ |
| **AzureBackup** |Azure Backup service.<br/><br/>*Note:* This tag has a dependency on the **Storage** and **AzureActiveDirectory** tags. | In uscita | No | SÌ |
| **AzureCloud** | All [datacenter public IP addresses](https://www.microsoft.com/download/details.aspx?id=41653). | In uscita | SÌ | SÌ |
| **AzureConnectors** | Logic Apps connectors for probe/backend connections. | In ingresso | SÌ | SÌ |
| **AzureContainerRegistry** | Azure Container Registry service. | In uscita | SÌ | SÌ |
| **AzureCosmosDB** | Azure Cosmos Database service. | In uscita | SÌ | SÌ |
| **AzureDataLake** | Azure Data Lake service. | In uscita | No | SÌ |
| **AzureIoTHub** | Azure IoT Hub service. | In uscita | No | No |
| **AzureKeyVault** | Azure KeyVault service.<br/><br/>*Note:* This tag has a dependency on the **AzureActiveDirectory** tag. | In uscita | SÌ | SÌ |
| **AzureLoadBalancer** | Azure's infrastructure load balancer. Viene convertito nell'[indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations) (168.63.129.16) da cui hanno origine i probe di integrità di Azure. Se non si usa Azure Load Balancer, è possibile eseguire l'override di questa regola. | Entrambi | No | No |
| **AzureMachineLearning** | Azure Machine Learning service. | In uscita | No | SÌ |
| **AzureMonitor** | Log Analytics, App Insights, AzMon, and custom metrics (GiG endpoints).<br/><br/>*Note:* For Log Analytics, this tag has dependency on the **Storage** tag. | In uscita | No | SÌ |
| **AzurePlatformDNS** | The basic infrastructure (default) DNS service.<br/><br>You can use this tag to disable the default DNS. Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | In uscita | No | No |
| **AzurePlatformIMDS** | IMDS, which is a basic infrastructure service.<br/><br/>You can use this tag to disable the default IMDS.  Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | In uscita | No | No |
| **AzurePlatformLKM** | Windows licensing or key management service.<br/><br/>You can use this tag to disable the defaults for licensing. Please exercise caution in using this tag.  Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | In uscita | No | No |
| **AzureTrafficManaged** | Azure Traffic Manager probe IP addresses.<br/><br/>Per altre informazioni sugli IP probe di Gestione traffico, consultare [Domande frequenti su Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | In ingresso | No | SÌ |  
| **BatchNodeManagement** | Management traffic for Azure Batch dedicated deployments. | Entrambi | No | SÌ |
| **CognitiveServicesManagement** | The address ranges for traffic for Cognitive Services | In uscita | No | No |
| **Dynamics365ForMarketingEmail** | The address ranges for the marketing email service of Dynamics 365. | In uscita | SÌ | No |
| **EventHub** | Azure EventHub service. | In uscita | SÌ | SÌ |
| **GatewayManager** | Management traffic for VPN/App Gateways dedicated deployments. | In ingresso | No | No |
| **Internet** | The IP address space that is outside the virtual network and reachable by the public Internet.<br/><br/>L'intervallo degli indirizzi include lo [spazio degli IP pubblici appartenenti ad Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Entrambi | No | No |
| **MicrosoftContainerRegistry** | Microsoft Container Registry service. | In uscita | SÌ | SÌ |
| **Bus di servizio** | Azure Service Bus service using the Premium service tier. | In uscita | SÌ | SÌ |
| **ServiceFabric** | Service Fabric service. | In uscita | No | No |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and Azure SQL Data Warehouse services.<br/><br/>*Note:* This tag represents the service, but not specific instances of the service. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL. | In uscita | SÌ | SÌ |
| **SqlManagement** | Management traffic for SQL dedicated deployments. | Entrambi | No | SÌ |
| **Archiviazione** | Azure Storage service. <br/><br/>*Note:* The tag represents the service, but not specific instances of the service. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure. | In uscita | SÌ | SÌ |
| **VirtualNetwork** | The virtual network address space (all IP address ranges defined for the virtual network), all connected on-premises address spaces, [peered](virtual-network-peering-overview.md) virtual networks, or virtual network connected to a [virtual network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), the [virtual IP address of the host](security-overview.md#azure-platform-considerations) and address prefixes used on [user-defined routes](virtual-networks-udr-overview.md). Be aware that this tag may also contain default routes. | Entrambi | No | No |

>[!NOTE]
>When working in the *Classic* (pre-Azure Resource Manager) environment, a select set of the above tags are supported.  These use an alternative spelling:

| Classic spelling | Equivalent Resource Manager tag |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> I tag di servizio per i servizi Azure identificano i prefissi di indirizzo dal cloud specifico in uso. e.g. the underlying IP ranges corresponding to the **Sql** tag value will differ between the Azure Public cloud and the Azure China cloud.

> [!NOTE]
> Se si implementa un [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) per un servizio come Archiviazione di Azure o Database SQL di Azure, Azure aggiunge una [route](virtual-networks-udr-overview.md#optional-default-routes) a una subnet di rete virtuale per il servizio. I prefissi di indirizzo nella route sono gli stessi prefissi di indirizzo o intervalli CIDR del tag di servizio corrispondente.



## <a name="service-tags-in-on-premises"></a>Service tags in on-premises  
You can obtain the current service tag and range information to include as part of your on-premises firewall configurations.  This information is the current point-in-time list of the IP ranges corresponding to each service tag.  The information can be obtained programmatically or via JSON file download as follows.

### <a name="service-tag-discovery-api-public-preview"></a>Service tag Discovery API (Public Preview)
You can programmatically retrieve the current list of service tags with IP address range details:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> While in Public Preview, the Discovery API may return information that is not as current as the JSON downloads (below).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Discover service tags using downloadable JSON files 
You can download JSON files containing the current list of service tags with IP address range details. These are updated and published weekly.  Locations for each cloud are:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>A subset of this information has previously been published in XML files for [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) and [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). These XML downloads will be deprecated by June 30, 2020 and will no longer be available after that date. Please migrate to using the Discovery API or JSON file downloads as described above.

### <a name="tips"></a>Suggerimenti 
- You can detect updates from one publishing to the next via increased *changeNumber* values within the JSON file. Each subsection (e.g. **Storage.WestUS**) has its own *changeNumber* that is incremented as changes occur.  The top level of the file's *changeNumber* is incremented when any of the subsections has changed.
- For examples of how to parse the service tag information (e.g. get all address ranges for Storage in WestUS), please refer to the [Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) documentation.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).

