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
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587524"
---
# <a name="virtual-network-service-tags"></a>Tag del servizio di rete virtuale 
<a name="network-service-tags"></a>

Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Consente di ridurre al minimo la complessità degli aggiornamenti frequenti sulle regole di sicurezza di rete. È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) o nel firewall di [Azure](https://docs.microsoft.com/azure/firewall/service-tags). È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, **ApiManagement**) nel campo di *origine* o di *destinazione* appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi. 

## <a name="available-service-tags"></a>Tag di servizio disponibili
La tabella seguente include tutti i tag di servizio disponibili per l'uso nelle regole dei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Le colonne indicano se il tag è:

- Adatto per le regole che coprono il traffico in ingresso o in uscita
- Supportare l'ambito [regionale](https://azure.microsoft.com/regions) 
- Utilizzabile nelle regole del [firewall di Azure](https://docs.microsoft.com/azure/firewall/service-tags)

Per impostazione predefinita, i tag del servizio riflettono gli intervalli per l'intero cloud.  Alcuni tag di servizio consentono anche un controllo più accurato, limitando gli intervalli IP corrispondenti a un'area specificata.  Ad esempio, mentre l' **archiviazione** dei tag del servizio rappresenta l'archiviazione di Azure per l'intero cloud, **storage. westus** restringe tale spazio solo agli intervalli di indirizzi IP di archiviazione dell'area westus.  La descrizione di ogni tag di servizio seguente indica se supporta tale ambito regionale.  



| Tag | Scopo | È possibile usare in ingresso o in uscita? | Può essere regionale? | È possibile usare con il firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Gestione API** | Traffico di gestione per le distribuzioni dedicate gestione API. | Entrambi | No | Sì |
| **AppService**    | Servizio app. Questo tag è consigliato per le regole di sicurezza in uscita per i front-end WebApp. | In uscita | Sì | Sì |
| **AppServiceManagement** | Traffico di gestione per le distribuzioni ambiente del servizio app dedicate. | Entrambi | No | Sì |
| **AzureActiveDirectory** | Servizio Azure Active Directory. | In uscita | No | Sì |
| **AzureActiveDirectoryDomainServices** | Traffico di gestione per le distribuzioni Azure Active Directory Domain Services dedicate. | Entrambi | No | Sì |
| **AzureBackup** |Servizio backup di Azure.<br/><br/>*Nota:* Questo tag presenta una dipendenza sui tag di **archiviazione** e **AzureActiveDirectory** . | In uscita | No | Sì |
| **AzureCloud** | Tutti [gli indirizzi IP pubblici del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). | In uscita | Sì | Sì |
| **AzureConnectors** | Connettori delle app per la logica per le connessioni Probe/back-end. | In ingresso | Sì | Sì |
| **AzureContainerRegistry** | Servizio Container Registry di Azure. | In uscita | Sì | Sì |
| **AzureCosmosDB** | Azure Cosmos database Service. | In uscita | Sì | Sì |
| **AzureDataLake** | Servizio Azure Data Lake. | In uscita | No | Sì |
| **AzureKeyVault** | Servizio dell'insieme di credenziali delle credenziali di Azure.<br/><br/>*Nota:* Questo tag presenta una dipendenza dal tag **AzureActiveDirectory** . | In uscita | Sì | Sì |
| **AzureLoadBalancer** | Servizio di bilanciamento del carico dell'infrastruttura di Azure. Viene convertito nell'[indirizzo IP virtuale dell'host](security-overview.md#azure-platform-considerations) (168.63.129.16) da cui hanno origine i probe di integrità di Azure. Se non si usa Azure Load Balancer, è possibile eseguire l'override di questa regola. | Entrambi | No | No |
| **AzureMachineLearning** | Servizio Azure Machine Learning. | In uscita | No | Sì |
| **AzureMonitor** | Log Analytics, App Insights, AzMon e metriche personalizzate (endpoint GiG).<br/><br/>*Nota:* Per Log Analytics, questo tag presenta una dipendenza dal tag di **archiviazione** . | In uscita | No | Sì |
| **AzurePlatformDNS** | Il servizio DNS dell'infrastruttura di base (impostazione predefinita).<br/><br>È possibile usare questo tag per disabilitare il DNS predefinito. Prestare attenzione nell'uso di questo tag. È consigliabile leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Il test è consigliato prima di usare questo tag. | In uscita | No | No |
| **AzurePlatformIMDS** | IMDS, un servizio di infrastruttura di base.<br/><br/>È possibile usare questo tag per disabilitare il valore predefinito di IMDS.  Prestare attenzione nell'uso di questo tag. È consigliabile leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Il test è consigliato prima di usare questo tag. | In uscita | No | No |
| **AzurePlatformLKM** | Servizio gestione licenze Windows o gestione chiavi.<br/><br/>È possibile utilizzare questo tag per disabilitare le impostazioni predefinite per le licenze. Prestare attenzione nell'uso di questo tag.  È consigliabile leggere le [considerazioni sulla piattaforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Il test è consigliato prima di usare questo tag. | In uscita | No | No |
| **AzureTrafficManaged** | Indirizzi IP di probe di gestione traffico di Azure.<br/><br/>Per altre informazioni sugli IP probe di Gestione traffico, consultare [Domande frequenti su Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | In ingresso | No | Sì |  
| **BatchNodeManagement** | Traffico di gestione per le distribuzioni Azure Batch dedicate. | Entrambi | No | Sì |
| **CognitiveServicesManagement** | Intervalli di indirizzi per il traffico per servizi cognitivi | In uscita | No | No |
| **Dynamics365ForMarketingEmail** | Gli intervalli di indirizzi per il servizio di posta elettronica di marketing di Dynamics 365. | In uscita | Sì | No |
| **EventHub** | Servizio Azure EventHub. | In uscita | Sì | Sì |
| **GatewayManager** | Traffico di gestione per le distribuzioni dedicate di gateway VPN/app. | In ingresso | No | No |
| **Internet** | Lo spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica.<br/><br/>L'intervallo degli indirizzi include lo [spazio degli IP pubblici appartenenti ad Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Entrambi | No | No |
| **MicrosoftContainerRegistry** | Servizio Microsoft Container Registry. | In uscita | Sì | Sì |
| **Bus di servizio** | Servizio del bus di servizio di Azure che usa il livello di servizio Premium. | In uscita | Sì | Sì |
| **ServiceFabric** | Servizio Service Fabric. | In uscita | No | No |
| **SQL** | Database SQL di Azure, database di Azure per MySQL, database di Azure per PostgreSQL e servizi Azure SQL Data Warehouse.<br/><br/>*Nota:* Questo tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL. | In uscita | Sì | Sì |
| **Oggetto SqlManagement** | Traffico di gestione per le distribuzioni SQL dedicate. | Entrambi | No | Sì |
| **Archiviazione** | Servizio di archiviazione di Azure. <br/><br/>*Nota:* Il tag rappresenta il servizio, ma non le istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure. | In uscita | Sì | Sì |
| **VirtualNetwork** | Lo spazio di indirizzi della rete virtuale (tutti gli intervalli di indirizzi IP definiti per la rete virtuale), tutti gli spazi di indirizzi locali connessi, le reti virtuali con [peering](virtual-network-peering-overview.md) o la rete virtuale connessa a un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l'indirizzo [IP virtuale Indirizzo dei](security-overview.md#azure-platform-considerations) prefissi dell'host e degli indirizzi usati nelle [route definite dall'utente](virtual-networks-udr-overview.md). Tenere presente che questo tag può contenere anche route predefinite. | Entrambi | No | No |

>[!NOTE]
>Quando si lavora in un ambiente *classico* (pre-Azure Resource Manager), sono supportati un set di selezione dei tag precedenti.  Che usano un'ortografia alternativa:

| Ortografia classica | Tag Gestione risorse equivalente |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> I tag di servizio per i servizi Azure identificano i prefissi di indirizzo dal cloud specifico in uso. ad esempio, gli intervalli IP sottostanti corrispondenti al valore del tag **SQL** variano tra il cloud pubblico di Azure e il cloud di Azure China.

> [!NOTE]
> Se si implementa un [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) per un servizio come Archiviazione di Azure o Database SQL di Azure, Azure aggiunge una [route](virtual-networks-udr-overview.md#optional-default-routes) a una subnet di rete virtuale per il servizio. I prefissi di indirizzo nella route sono gli stessi prefissi di indirizzo o intervalli CIDR del tag di servizio corrispondente.



## <a name="service-tags-in-on-premises"></a>Tag del servizio in locale  
È possibile ottenere il tag di servizio e le informazioni sull'intervallo correnti da includere come parte delle configurazioni del firewall locale.  Queste informazioni corrispondono all'elenco temporizzato corrente degli intervalli IP corrispondenti a ogni tag di servizio.  Le informazioni possono essere ottenute a livello di codice o tramite il download del file JSON come indicato di seguito.

### <a name="service-tag-discovery-api-public-preview"></a>API di individuazione tag di servizio (anteprima pubblica)
È possibile recuperare a livello di codice l'elenco corrente dei tag del servizio con i dettagli dell'intervallo di indirizzi IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Nell'anteprima pubblica, l'API di individuazione può restituire informazioni non aggiornate come i download JSON (di seguito).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Individuare i tag del servizio usando file JSON scaricabili 
È possibile scaricare i file JSON contenenti l'elenco corrente dei tag del servizio con i dettagli dell'intervallo di indirizzi IP. Questi vengono aggiornati e pubblicati settimanalmente.  Le località per ogni cloud sono:

- [Pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Un subset di queste informazioni è stato pubblicato in precedenza in file XML per [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Cina](https://www.microsoft.com/download/details.aspx?id=42064) e [Azure Germania](https://www.microsoft.com/download/details.aspx?id=54770). Questi download XML saranno deprecati entro il 30 giugno 2020 e non saranno più disponibili dopo tale data. Eseguire la migrazione a usando l'API di individuazione o i download di file JSON come descritto in precedenza.

### <a name="tips"></a>Suggerimenti 
- È possibile rilevare gli aggiornamenti da una pubblicazione a quella successiva tramite valori *ChangeNumber* più elevati all'interno del file JSON. Ogni sottosezione (ad esempio, **storage. westus**) ha un proprio *ChangeNumber* che viene incrementato quando si verificano modifiche.  Il primo livello del *ChangeNumber* del file viene incrementato quando una delle sottosezioni è stata modificata.
- Per esempi di come analizzare le informazioni sui tag di servizio (ad esempio, ottenere tutti gli intervalli di indirizzi per l'archiviazione in Westus), vedere la documentazione di PowerShell relativa all' [API di individuazione dei tag di servizio](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).

