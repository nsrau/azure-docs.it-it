---
title: Azure service tags overview
titlesuffix: Azure Virtual Network
description: Learn about service tags. Service tags help minimize the complexity of security rule creation.
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
ms.openlocfilehash: 8d5377f7ec8de14f3d7d55bc109f6be731991051
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775256"
---
# <a name="virtual-network-service-tags"></a>Virtual network service tags 
<a name="network-service-tags"></a>

A service tag represents a group of IP address prefixes from a given Azure service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change, minimizing the complexity of frequent updates to network security rules. 

You can use service tags to define network access controls on [network security groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). Use service tags in place of specific IP addresses when you create security rules. By specifying the service tag name (for example, **ApiManagement**) in the appropriate *source* or *destination* field of a rule, you can allow or deny the traffic for the corresponding service. 

You can use service tags to achieve network isolation and protect your Azure resources from the general Internet while accessing Azure services that have public endpoints. Create inbound/outbound network security group rules to deny traffic to/from **Internet** and allow traffic to/from **AzureCloud** or other [available service tags](#available-service-tags) of specific Azure services. 

## <a name="available-service-tags"></a>Available service tags
The following table includes all the service tags available for use in [network security group](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) rules.

The columns indicate whether the tag:

- Is suitable for rules that cover inbound or outbound traffic.
- Supports [regional](https://azure.microsoft.com/regions) scope.
- Is usable in [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) rules.

By default, service tags reflect the ranges for the entire cloud. Some service tags also allow more granular control by restricting the corresponding IP ranges to a specified region. For example, the service tag **Storage** represents Azure Storage for the entire cloud, but **Storage.WestUS** narrows the range to only the storage IP address ranges from the WestUS region. The following table indicates whether each service tag supports such regional scope.  

| Tag | Finalità | Can use inbound or outbound? | Can be regional? | Can use with Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Gestione API** | Management traffic for Azure API Management-dedicated deployments. | Entrambi | No | Sì |
| **ApplicationInsightsAvailability** | Application Insights Availability. | Entrambi | No | No |
| **AppService**    | Servizio app di Azure. This tag is recommended for outbound security rules to web app front ends. | In uscita | Sì | Sì |
| **AppServiceManagement** | Management traffic for deployments dedicated to App Service Environment. | Entrambi | No | Sì |
| **AzureActiveDirectory** | Azure Active Directory. | In uscita | No | Sì |
| **AzureActiveDirectoryDomainServices** | Management traffic for deployments dedicated to Azure Active Directory Domain Services. | Entrambi | No | Sì |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | In uscita | No | No |
| **AzureBackup** |Azure Backup.<br/><br/>*Note:* This tag has a dependency on the **Storage** and **AzureActiveDirectory** tags. | In uscita | No | Sì |
| **AzureBotService** | Azure Bot Service. | In uscita | No | No |
| **AzureCloud** | All [datacenter public IP addresses](https://www.microsoft.com/download/details.aspx?id=56519). | In uscita | Sì | Sì |
| **AzureCognitiveSearch** | Azure Cognitive Search (if using indexers with a skillset). | Entrambi | No | No |
| **AzureConnectors** | Azure Logic Apps connectors for probe/back-end connections. | In ingresso | Sì | Sì |
| **AzureContainerRegistry** | Azure Container Registry. | In uscita | Sì | Sì |
| **AzureCosmosDB** | Azure Cosmos DB. | In uscita | Sì | Sì |
| **AzureDatabricks** | Azure Databricks. | Entrambi | No | No |
| **AzureDataExplorerManagement** | Azure Data Explorer Management. | In ingresso | No | No |
| **AzureDataLake** | Azure Data Lake. | In uscita | No | Sì |
| **AzureEventGrid** | Griglia di eventi di Azure. <br/><br/>*Note:* This tag covers Azure Event Grid endpoints in US South Central, US East, US East 2, US West 2, and US Central only. | Entrambi | No | No |
| **AzureFrontDoor** | Azure Front Door. | Entrambi | No | No |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Note:* This tag has a dependency on the **AzureActiveDirectory** and **AzureFrontDoor.Frontend** tags. Please also whitelist following IPs (this dependency will be removed soon): 13.107.6.181 & 13.107.9.181. | In uscita | No | No |
| **AzureIoTHub** | Azure IoT Hub. | In uscita | No | No |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Nota:* Questo tag presenta una dipendenza dal tag **AzureActiveDirectory** . | In uscita | Sì | Sì |
| **AzureLoadBalancer** | Il servizio di bilanciamento del carico dell'infrastruttura di Azure. Il tag viene convertito nell' [indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations) (168.63.129.16) in cui hanno origine i probe di integrità di Azure. Questo non include il traffico verso la risorsa Azure Load Balancer. Se non si usa Azure Load Balancer, è possibile eseguire l'override di questa regola. | Entrambi | No | No |
| **AzureMachineLearning** | Azure Machine Learning | Entrambi | No | Sì |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e metriche personalizzate (endpoint GiG).<br/><br/>*Nota:* Per Log Analytics, questo tag presenta una dipendenza dal tag di **archiviazione** . | In uscita | No | Sì |
| **AzurePlatformDNS** | Il servizio DNS dell'infrastruttura di base (impostazione predefinita).<br/><br>È possibile usare questo tag per disabilitare il DNS predefinito. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzurePlatformIMDS** | Servizio metadati dell'istanza di Azure (IMDS), un servizio di infrastruttura di base.<br/><br/>È possibile usare questo tag per disabilitare il valore predefinito di IMDS. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzurePlatformLKM** | Servizio gestione licenze Windows o gestione chiavi.<br/><br/>È possibile utilizzare questo tag per disabilitare le impostazioni predefinite per le licenze. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzureResourceManager** | Azure Resource Manager. | In uscita | No | No |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Nota:* Questo tag presenta una dipendenza dai tag **storage**, **AzureActiveDirectory**e **EventHub** . | In uscita | No | No |
| **AzureTrafficManager** | Indirizzi IP di probe di gestione traffico di Azure.<br/><br/>Per altre informazioni sugli indirizzi IP di probe di gestione traffico, vedere [domande frequenti su Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | In ingresso | No | Sì |  
| **BatchNodeManagement** | Traffico di gestione per le distribuzioni dedicate al Azure Batch. | Entrambi | No | Sì |
| **CognitiveServicesManagement** | Intervalli di indirizzi per il traffico per servizi cognitivi di Azure. | In uscita | No | No |
| **Dynamics365ForMarketingEmail** | Gli intervalli di indirizzi per il servizio di posta elettronica di marketing di Dynamics 365. | In uscita | Sì | No |
| **ElasticAFD** | Sportello anteriore di Azure elastico. | Entrambi | No | No |
| **EventHub** | Hub eventi di Azure. | In uscita | Sì | Sì |
| **GatewayManager** | Traffico di gestione per le distribuzioni dedicate al gateway VPN di Azure e al gateway applicazione. | In ingresso | No | No |
| **GuestAndHybridManagement** | Automazione di Azure e configurazione Guest. | In uscita | No | Sì |
| **HDInsight** | Azure HDInsight. | In ingresso | Sì | No |
| **Internet** | Lo spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica.<br/><br/>L'intervallo di indirizzi include lo [spazio degli indirizzi IP pubblici di proprietà di Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Entrambi | No | No |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | In uscita | No | No |
| **MicrosoftContainerRegistry** | Registro contenitori per le immagini del contenitore Microsoft. <br/><br/>*Nota:* Inserire anche l'indirizzo IP seguente (la dipendenza verrà rimossa a breve): 204.79.197.219. | In uscita | Sì | Sì |
| **Bus di servizio** | Traffico del bus di servizio di Azure che usa il livello di servizio Premium. | In uscita | Sì | Sì |
| **ServiceFabric** | Service Fabric di Azure. | In uscita | No | No |
| **Sql** | Database SQL di Azure, database di Azure per MySQL, database di Azure per PostgreSQL e Azure SQL Data Warehouse.<br/><br/>*Nota:* Questo tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL. | In uscita | Sì | Sì |
| **Oggetto SqlManagement** | Traffico di gestione per le distribuzioni dedicate a SQL. | Entrambi | No | Sì |
| **Storage** | Archiviazione di Azure. <br/><br/>*Nota:* Questo tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure. | In uscita | Sì | Sì |
| **VirtualNetwork** | Lo spazio di indirizzi della rete virtuale (tutti gli intervalli di indirizzi IP definiti per la rete virtuale), tutti gli spazi di indirizzi locali connessi, le reti virtuali con [peering](virtual-network-peering-overview.md) , le reti virtuali connesse a un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l' [indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations)e i prefissi degli indirizzi usati nelle [route definite dall'utente](virtual-networks-udr-overview.md). Questo tag potrebbe contenere anche route predefinite. | Entrambi | No | No |

>[!NOTE]
>Nel modello di distribuzione classica (prima Azure Resource Manager), sono supportati un subset dei tag elencati nella tabella precedente. Questi tag sono digitati in modo diverso:
>
>| Ortografia classica | Tag Gestione risorse equivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> I tag di servizio dei servizi di Azure denotano i prefissi degli indirizzi dal cloud specifico usato. Ad esempio, gli intervalli di indirizzi IP sottostanti che corrispondono al valore del tag **SQL** nel cloud pubblico di Azure saranno diversi dagli intervalli sottostanti nel cloud di Azure China.

> [!NOTE]
> Se si implementa un [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) per un servizio come Archiviazione di Azure o Database SQL di Azure, Azure aggiunge una [route](virtual-networks-udr-overview.md#optional-default-routes) a una subnet di rete virtuale per il servizio. I prefissi di indirizzo nella route sono gli stessi prefissi di indirizzo o intervalli CIDR come quelli del tag di servizio corrispondente.

## <a name="service-tags-on-premises"></a>Tag del servizio in locale  
È possibile ottenere il tag di servizio e le informazioni sull'intervallo correnti da includere come parte delle configurazioni del firewall locale. Queste informazioni corrispondono all'elenco temporizzato corrente degli intervalli IP che corrispondono a ogni tag di servizio. È possibile ottenere le informazioni a livello di codice o tramite un download di file JSON, come descritto nelle sezioni seguenti.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Usare l'API di individuazione dei tag di servizio (anteprima pubblica)
È possibile recuperare l'elenco corrente dei tag di servizio a livello di codice insieme ai dettagli dell'intervallo di indirizzi IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Mentre è in anteprima pubblica, l'API di individuazione può restituire informazioni meno aggiornate rispetto alle informazioni restituite dai download JSON. Vedere la sezione successiva.


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Individuare i tag del servizio usando file JSON scaricabili 
È possibile scaricare i file JSON che contengono l'elenco corrente dei tag di servizio insieme ai dettagli dell'intervallo di indirizzi IP. Questi elenchi vengono aggiornati e pubblicati settimanalmente. Le località per ogni cloud sono:

- [Pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Un subset di queste informazioni è stato pubblicato in file XML per Azure [public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Cina](https://www.microsoft.com/download/details.aspx?id=42064)e [Azure Germania](https://www.microsoft.com/download/details.aspx?id=54770). Questi download XML saranno deprecati entro il 30 giugno 2020 e non saranno più disponibili dopo tale data. È consigliabile eseguire la migrazione a usando l'API di individuazione o i download di file JSON come descritto nelle sezioni precedenti.

### <a name="tips"></a>Suggerimenti 
- È possibile rilevare gli aggiornamenti da una pubblicazione alla successiva annotando i valori di *ChangeNumber* più elevati nel file JSON. Ogni sottosezione (ad esempio, **storage. westus**) ha un proprio *ChangeNumber* che viene incrementato quando si verificano modifiche. Il primo livello del *ChangeNumber* del file viene incrementato quando una delle sottosezioni viene modificata.
- Per esempi di come analizzare le informazioni sui tag di servizio (ad esempio, ottenere tutti gli intervalli di indirizzi per l'archiviazione in Westus), vedere la documentazione di PowerShell per l' [API di individuazione dei tag di servizio](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).
