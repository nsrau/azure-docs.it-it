---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. This article includes information on how to increase limits along with maximum values.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: cost-management-billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 08c459a3c32b44df2d9e5cf783087dd34d660292
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463324"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione ad Azure e limiti, quote e vincoli del servizio
In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote. Al momento nel documento non vengono trattati tutti i servizi di Azure. Over time, the list will be expanded and updated to cover more services.

To learn more about Azure pricing, see [Azure pricing overview](https://azure.microsoft.com/pricing/). There, you can estimate your costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). You also can go to the pricing details page for a particular service, for example, [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](billing/billing-getting-started.md).

> [!NOTE]
> If you want to raise the limit or quota above the default limit, [open an online customer support request at no charge](azure-resource-manager/resource-manager-quota-errors.md). The limits can't be raised above the maximum limit value shown in the following tables. If there's no maximum limit column, the resource doesn't have adjustable limits.
>
> [Free Trial subscriptions](https://azure.microsoft.com/offers/ms-azr-0044p) aren't eligible for limit or quota increases. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). For more information, see [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](billing/billing-upgrade-azure-subscription.md) and the [Free Trial subscription FAQ](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limits and Azure Resource Manager
It's now possible to combine multiple Azure resources into a single Azure resource group. When you use resource groups, limits that once were global become managed at a regional level with Azure Resource Manager. For more information about Azure resource groups, see [Azure Resource Manager overview](azure-resource-manager/resource-group-overview.md).

In the following list of limits, a new table reflects any differences in limits when you use Azure Resource Manager. For example, there's a **Subscription limits** table and a **Subscription limits - Azure Resource Manager** table. When a limit applies to both scenarios, it's only shown in the first table. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [!NOTE]
> Quotas for resources in Azure resource groups are per-region accessible by your subscription, not per-subscription as the service management quotas are. Si considerino, ad esempio, le quote di vCPU. To request a quota increase with support for vCPUs, you must decide how many vCPUs you want to use in which regions. You then make a specific request for Azure resource group vCPU quotas for the amounts and regions that you want. If you need to use 30 vCPUs in West Europe to run your application there, you specifically request 30 vCPUs in West Europe. Your vCPU quota isn't increased in any other region--only West Europe has the 30-vCPU quota.
> <!-- -->
> As a result, decide what your Azure resource group quotas must be for your workload in any one region. Then request that amount in each region into which you want to deploy. For help in how to determine your current quotas for specific regions, see [Troubleshoot deployment issues](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limiti specifici del servizio
* [Active Directory](#active-directory-limits)
* [Gestione API](#api-management-limits)
* [Servizio app](#app-service-limits)
* [Gateway applicazione](#application-gateway-limits)
* [Automazione](#automation-limits)
* [Cache Redis di Azure](#azure-cache-for-redis-limits)
* [Servizi cloud di Azure](#azure-cloud-services-limits)
* [Azure Cognitive Search](#azure-cognitive-search-limits)
* [Servizi cognitivi di Azure](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Database di Azure per MySQL](#azure-database-for-mysql)
* [Database di Azure per PostgreSQL](#azure-database-for-postgresql)
* [DNS di Azure](#azure-dns-limits)
* [Firewall di Azure](#azure-firewall-limits)
* [Funzioni di Azure](#functions-limits)
* [Servizio Azure Kubernetes](#azure-kubernetes-service-limits)
* [Azure Machine Learning](#azure-machine-learning-limits)
* [Mappe di Azure](#azure-maps-limits)
* [Monitoraggio di Azure](#azure-monitor-limits)
* [Criteri di Azure](#azure-policy-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Servizi BizTalk](#biztalk-services-limits)
* [Istanze di Container](#container-instances-limits)
* [Registro Container](#container-registry-limits)
* [Rete per la distribuzione di contenuti](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Analisi Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Servizio Migrazione del database di Azure](#database-migration-service-limits)
* [Griglia di eventi](#event-grid-limits)
* [Hub eventi](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Central](#iot-central-limits)
* [Hub IoT](#iot-hub-limits)
* [Servizio Device Provisioning in hub IoT](#iot-hub-device-provisioning-service-limits)
* [Insieme di credenziali delle chiavi](#key-vault-limits)
* [Servizi multimediali](#media-services-limits)
* [Servizi mobili](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Rete](#networking-limits)
  * [Gateway applicazione](#application-gateway-limits)
  * [Azure Bastion](#azure-bastion-limits)
  * [DNS di Azure](#azure-dns-limits)
  * [Servizio Frontdoor di Azure](#azure-front-door-service-limits)
  * [Firewall di Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Bilanciamento del carico](#load-balancer)
  * [Network Watcher](#network-watcher-limits)
  * [Indirizzo IP pubblico](#publicip-address)
  * [Collegamento privato](#private-link-limits)
  * [Gestione traffico](#traffic-manager-limits)
  * [Rete virtuale](#networking-limits)
  * [Virtual WAN](#virtual-wan-limits)
* [Hub di notifica](#notification-hubs-limits)
* [Gruppo di risorse](#resource-group-limits)
* [Controllo degli accessi in base al ruolo](#role-based-access-control-limits)
* [Utilità di pianificazione](#scheduler-limits)
* [Bus di servizio](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Database SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Archiviazione](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Analisi dei flussi](#stream-analytics-limits)
* [Sottoscrizione](#subscription-limits)
* [Macchine virtuali](#virtual-machines-limits)
* [Set di scalabilità di macchine virtuali](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Subscription limits - Azure Service Management (classic deployment model)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti delle sottoscrizioni - Azure Resource Manager
The following limits apply when you use Azure Resource Manager and Azure resource groups. Limits that haven't changed with Azure Resource Manager aren't listed. See the previous table for those limits.

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resource group limits
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
#### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure
The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Shared Image Gallery limits

There are limits, per subscription, for deploying resources using Shared Image Galleries:
- 100 shared image galleries, per subscription, per region
- 1,000 image definitions, per subscription, per region
- 10,000 image versions, per subscription, per region

### <a name="virtual-machine-scale-sets-limits"></a>Virtual machine scale sets limits
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limiti per Istanze di Container
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limiti per Registro contenitori
La tabella seguente illustra le funzionalità e i limiti dei [livelli di servizio](./container-registry/container-registry-skus.md) Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service limits
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Azure Machine Learning limits
The latest values for Azure Machine Learning Compute quotas can be found in the [Azure Machine Learning quota page](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute limits
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Virtual WAN limits
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Limiti del gateway applicazione

Se non diversamente specificato, la tabella è valida per gli SKU v1, v2, Standard e WAF.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limiti relativi a Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Private Link limits
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Azure Bastion limits
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS limits
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

For more information on storage account limits, see [Azure Storage scalability and performance targets](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limiti relativi a File di Azure
For more information on Azure Files limits, see [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limiti relativi a Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limiti relativi ad Archiviazione code di Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limiti relativi ad Archiviazione tabelle di Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limiti relativi ai dischi della macchina virtuale
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

For more information, see [Virtual machine sizes](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Limiti relativi a Servizio app
I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions limits
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limiti relativi all'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti relativi a Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limiti relativi a Servizi BizTalk
The following table shows the limits for Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB
For Azure Cosmos DB limits, see [Limits in Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Database di Azure per MySQL
Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL
Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search limits
I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* **Free** multitenant service, shared with other Azure subscribers, is intended for evaluation and small development projects.
* **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* **Standard**, which includes S1, S2, S3, and S3 High Density, is for larger production workloads. Multiple levels exist within the Standard tier so that you can choose a resource configuration that best matches your workload profile.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

To learn more about limits on a more granular level, such as document size, queries per second, keys, requests, and responses, see [Service limits in Azure Cognitive Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network limits
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor limits

#### <a name="alerts"></a>Avvisi

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Gruppi di azioni

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Log queries and language

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs limits
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limiti relativi al bus di servizio
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-central-limits"></a>IoT Central limits
[!INCLUDE [iot-central-limits](../includes/iot-central-limits.md)]

### <a name="iot-hub-limits"></a>Limiti relativi all'hub IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limiti relativi al servizio Device Provisioning in hub IoT
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limiti relativi a Data factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limiti di Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limiti di Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limiti relativi al Servizio Migrazione del database di Azure
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limiti relativi ad analisi di flusso
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limiti relativi ad Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid limits
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limiti di Mappe di Azure
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limiti di Criteri di Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Limiti relativi a Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service limits
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Limiti relativi a Gestione API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication limits
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager limits
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limiti relativi al controllo degli accessi in base al ruolo
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limiti relativi a database SQL
For SQL Database limits, see [SQL Database resource limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database resource limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), and [SQL Database resource limits for managed instances](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limiti relativi a SQL Data Warehouse
For SQL Data Warehouse limits, see [SQL Data Warehouse resource limits](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Vedi anche
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtual machine and cloud service sizes for Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Sizes for Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
