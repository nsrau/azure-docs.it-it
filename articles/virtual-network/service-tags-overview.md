---
title: Panoramica sui tag del servizio di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sui tag di servizio. I tag di servizio consentono di ridurre al minimo la complessità della creazione delle regole di sicurezza.
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
ms.openlocfilehash: 7f5f75385374b6949ee3ef5e5ef298b8d54cc36e
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163251"
---
# <a name="virtual-network-service-tags"></a>Tag del servizio di rete virtuale
<a name="network-service-tags"></a>

Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi, riducendo al minimo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete.

È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) o nel firewall di [Azure](https://docs.microsoft.com/azure/firewall/service-tags). Usare i tag del servizio al posto di indirizzi IP specifici quando si creano le regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, **ApiManagement**) nel campo di di *origine* o di *destinazione* di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

È possibile usare i tag di servizio per ottenere l'isolamento rete e proteggere le risorse di Azure da Internet generale durante l'accesso ai servizi di Azure con endpoint pubblici. Creare regole del gruppo di sicurezza di rete in ingresso/in uscita per negare il traffico da e verso **Internet** e consentire il traffico verso/da **AzureCloud** o altri [tag di servizio disponibili](#available-service-tags) di servizi di Azure specifici.

## <a name="available-service-tags"></a>Tag di servizio disponibili
La tabella seguente include tutti i tag di servizio disponibili per l'uso nelle regole del [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Le colonne indicano se il tag:

- È adatto per le regole che coprono il traffico in ingresso o in uscita.
- Supporta l'ambito [regionale](https://azure.microsoft.com/regions) .
- È utilizzabile nelle regole del [firewall di Azure](https://docs.microsoft.com/azure/firewall/service-tags) .

Per impostazione predefinita, i tag del servizio riflettono gli intervalli per l'intero cloud. Alcuni tag del servizio consentono un controllo più granulare limitando gli intervalli IP corrispondenti a un'area specificata. Ad esempio, **l'archiviazione dei tag del** servizio rappresenta l'archiviazione di Azure per l'intero cloud, ma **storage. westus** restringe l'intervallo solo agli intervalli di indirizzi IP di archiviazione dell'area westus. La tabella seguente indica se ogni tag di servizio supporta tale ambito regionale.  

| Tag | Scopo | È possibile usare in ingresso o in uscita? | Può essere regionale? | È possibile usare con il firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Gestione API** | Traffico di gestione per gestione API di Azure-distribuzioni dedicate. <br/><br/>*Nota:* Questo tag rappresenta l'endpoint del servizio gestione API di Azure per il piano di controllo per area. Ciò consente ai clienti di eseguire operazioni di gestione su API, operazioni, criteri, NamedValues configurate nel servizio gestione API.  | In ingresso | Sì | Sì |
| **ApplicationInsightsAvailability** | Disponibilità Application Insights. | In ingresso | No | No |
| **AppService**    | Servizio app di Azure. Questo tag è consigliato per le regole di sicurezza in uscita per i front-end dell'app Web. | In uscita | Sì | Sì |
| **AppServiceManagement** | Traffico di gestione per le distribuzioni dedicate al ambiente del servizio app. | Entrambe | No | Sì |
| **AzureActiveDirectory** | Azure Active Directory. | In uscita | No | Sì |
| **AzureActiveDirectoryDomainServices** | Traffico di gestione per le distribuzioni dedicate al Azure Active Directory Domain Services. | Entrambe | No | Sì |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | In uscita | No | No |
| **AzureBackup** |Backup di Azure.<br/><br/>*Nota:* Questo tag presenta una dipendenza sui tag di **archiviazione** e **AzureActiveDirectory** . | In uscita | No | Sì |
| **AzureBotService** | Servizio Azure bot. | In uscita | No | No |
| **AzureCloud** | Tutti [gli indirizzi IP pubblici del Data Center](https://www.microsoft.com/download/details.aspx?id=56519). | In uscita | Sì | Sì |
| **AzureCognitiveSearch** | Ricerca cognitiva di Azure. <br/><br/>Questo tag o gli indirizzi IP trattati da questo tag possono essere utilizzati per concedere agli indicizzatori l'accesso sicuro alle origini dati. Per altri dettagli, vedere la [documentazione sulla connessione dell'indicizzatore](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) . | In ingresso | No | No |
| **AzureConnectors** | Connettori delle app per la logica di Azure per le connessioni Probe/back-end. | In ingresso | Sì | Sì |
| **AzureContainerRegistry** | Container Registry di Azure. | In uscita | Sì | Sì |
| **AzureCosmosDB** | Azure Cosmos DB. | In uscita | Sì | Sì |
| **AzureDatabricks** | Azure Databricks. | Entrambe | No | No |
| **AzureDataExplorerManagement** | Gestione Esplora dati di Azure. | In ingresso | No | No |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | In uscita | No | Sì |
| **AzureEventGrid** | Griglia di eventi di Azure. <br/><br/>*Nota:* Questo tag riguarda gli endpoint di griglia di eventi di Azure negli Stati Uniti centro-meridionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali 2 e Stati Uniti centrali. | Entrambe | No | No |
| **AzureFrontDoor** | Sportello anteriore di Azure. | Entrambe | No | No |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Nota:* Questo tag presenta una dipendenza dai tag **AzureActiveDirectory** e **AzureFrontDoor. frontend** . Inserire anche gli indirizzi IP seguenti (la dipendenza verrà rimossa a breve): 13.107.6.181 & 13.107.9.181. | In uscita | No | No |
| **AzureIoTHub** | Hub Azure. | In uscita | No | No |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Nota:* Questo tag presenta una dipendenza dal tag **AzureActiveDirectory** . | In uscita | Sì | Sì |
| **AzureLoadBalancer** | Il servizio di bilanciamento del carico dell'infrastruttura di Azure. Il tag viene convertito nell' [indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations) (168.63.129.16) in cui hanno origine i probe di integrità di Azure. Questo non include il traffico verso la risorsa Azure Load Balancer. Se non si usa Azure Load Balancer, è possibile eseguire l'override di questa regola. | Entrambe | No | No |
| **AzureMachineLearning** | Azure Machine Learning | Entrambe | No | Sì |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e metriche personalizzate (endpoint GiG).<br/><br/>*Nota:* Per Log Analytics, questo tag presenta una dipendenza dal tag di **archiviazione** . | In uscita | No | Sì |
| **AzurePlatformDNS** | Il servizio DNS dell'infrastruttura di base (impostazione predefinita).<br/><br>È possibile usare questo tag per disabilitare il DNS predefinito. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzurePlatformIMDS** | Servizio metadati dell'istanza di Azure (IMDS), un servizio di infrastruttura di base.<br/><br/>È possibile usare questo tag per disabilitare il valore predefinito di IMDS. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzurePlatformLKM** | Servizio gestione licenze Windows o gestione chiavi.<br/><br/>È possibile utilizzare questo tag per disabilitare le impostazioni predefinite per le licenze. Prestare attenzione quando si usa questo tag. Si consiglia di leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Si consiglia inoltre di eseguire i test prima di utilizzare questo tag. | In uscita | No | No |
| **AzureResourceManager** | Azure Resource Manager. | In uscita | No | No |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Nota:* Questo tag presenta una dipendenza dai tag **storage**, **AzureActiveDirectory**e **EventHub** . | In uscita | No | No |
| **AzureTrafficManager** | Indirizzi IP di probe di gestione traffico di Azure.<br/><br/>Per altre informazioni sugli indirizzi IP di probe di gestione traffico, vedere [domande frequenti su Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | In ingresso | No | Sì |  
| **BatchNodeManagement** | Traffico di gestione per le distribuzioni dedicate al Azure Batch. | Entrambe | No | Sì |
| **CognitiveServicesManagement** | Intervalli di indirizzi per il traffico per servizi cognitivi di Azure. | In uscita | No | No |
| **Dynamics365ForMarketingEmail** | Gli intervalli di indirizzi per il servizio di posta elettronica di marketing di Dynamics 365. | In uscita | Sì | No |
| **ElasticAFD** | Sportello anteriore di Azure elastico. | Entrambe | No | No |
| **EventHub** | Hub eventi di Azure. | In uscita | Sì | Sì |
| **GatewayManager** | Traffico di gestione per le distribuzioni dedicate al gateway VPN di Azure e al gateway applicazione. | In ingresso | No | No |
| **GuestAndHybridManagement** | Automazione di Azure e configurazione Guest. | In uscita | No | Sì |
| **HDInsight** | Azure HDInsight. | In ingresso | Sì | No |
| **Internet** | Lo spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica.<br/><br/>L'intervallo di indirizzi include lo [spazio degli indirizzi IP pubblici di proprietà di Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Entrambe | No | No |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | In uscita | No | No |
| **MicrosoftContainerRegistry** | Registro contenitori per le immagini del contenitore Microsoft. <br/><br/>*Nota:* Inserire anche l'indirizzo IP seguente (la dipendenza verrà rimossa a breve): 204.79.197.219. | In uscita | Sì | Sì |
| **Bus di servizio** | Traffico del bus di servizio di Azure che usa il livello di servizio Premium. | In uscita | Sì | Sì |
| **ServiceFabric** | Service Fabric di Azure.<br/><br/>*Nota:* Questo tag rappresenta l'endpoint del servizio Service Fabric per il piano di controllo per area. Ciò consente ai clienti di eseguire operazioni di gestione per i cluster Service Fabric dai rispettivi VNET (endpoint, ad esempio. https://westus.servicefabric.azure.com) | Entrambe | No | No |
| **Sql** | Database SQL di Azure, database di Azure per MySQL, database di Azure per PostgreSQL e Azure SQL Data Warehouse.<br/><br/>*Nota:* Questo tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL. | In uscita | Sì | Sì |
| **Oggetto SqlManagement** | Traffico di gestione per le distribuzioni dedicate a SQL. | Entrambe | No | Sì |
| **Storage** | Archiviazione di Azure. <br/><br/>*Nota:* Questo tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure. | In uscita | Sì | Sì |
| **VirtualNetwork** | Lo spazio di indirizzi della rete virtuale (tutti gli intervalli di indirizzi IP definiti per la rete virtuale), tutti gli spazi di indirizzi locali connessi, le reti virtuali con [peering](virtual-network-peering-overview.md) , le reti virtuali connesse a un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l' [indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations)e i prefissi degli indirizzi usati nelle [route definite dall'utente](virtual-networks-udr-overview.md). Questo tag potrebbe contenere anche route predefinite. | Entrambe | No | No |

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
> Mentre è in anteprima pubblica, l'API di individuazione può restituire informazioni meno aggiornate rispetto alle informazioni restituite dai download JSON. (vedere la sezione successiva).


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
