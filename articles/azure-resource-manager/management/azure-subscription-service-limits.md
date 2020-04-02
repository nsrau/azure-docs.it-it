---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Questo articolo include informazioni su come aumentare i limiti insieme ai valori massimi.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 397d6a719f64a49208e651469fab9cf841ccd4ad
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521360"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi

In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote.

Per altre informazioni sui prezzi di Azure, vedere [Panoramica dei prezzi di Azure.To](https://azure.microsoft.com/pricing/)learn more about Azure pricing, see Azure pricing overview. Lì, è possibile stimare i costi utilizzando il [calcolatore](https://azure.microsoft.com/pricing/calculator/)dei prezzi . È anche possibile passare alla pagina dei dettagli dei prezzi per un determinato servizio, ad esempio [macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](../../billing/billing-getting-started.md).

## <a name="managing-limits"></a>Gestione dei limiti

> [!NOTE]
> Alcuni servizi hanno limiti regolabili.
>
> Quando un servizio non dispone di limiti regolabili, nelle tabelle seguenti viene utilizzata l'intestazione **Limit**. In questi casi, i limiti predefiniti e massimi sono gli stessi.
>
> Quando il limite può essere regolato, le tabelle includono **Limite predefinito** e Intestazioni **limite massimo.** Il limite può essere superato al di sopra del limite predefinito, ma non al di sopra del limite massimo.
>
> Se si desidera aumentare il limite o la quota al di sopra del limite predefinito, aprire una richiesta di [assistenza clienti online senza alcun costo aggiuntivo.](../templates/error-resource-quota.md)

[Le sottoscrizioni di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non sono idonee per gli aumenti di limiti o quote. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per altre informazioni, vedere [Aggiornare la sottoscrizione](../../billing/billing-upgrade-azure-subscription.md) di valutazione gratuita di Azure a una sottoscrizione con pagamento in base al popup e Domande frequenti sulla sottoscrizione di [valutazione gratuita.](https://azure.microsoft.com/free/free-account-faq)

Alcuni limiti sono gestiti a livello regionale.

Si considerino, ad esempio, le quote di vCPU. Per richiedere un aumento della quota con il supporto per vCPU, è necessario decidere quante vCPU si desidera utilizzare in quali aree. Si effettua quindi una richiesta specifica per le quote vCPU del gruppo di risorse di Azure per gli importi e le aree desiderate. Se è necessario utilizzare 30 vCPU nell'Europa occidentale per eseguire l'applicazione, è necessario richiedere in modo specifico 30 vCPU in Europa occidentale. La quota vCPU non è aumentata in nessun'altra regione: solo l'Europa occidentale ha la quota di 30 vCPU.

Di conseguenza, decidere quali devono essere le quote del gruppo di risorse di Azure per il carico di lavoro in qualsiasi area. Richiedere quindi tale importo in ogni area in cui si desidera eseguire la distribuzione. Per informazioni su come determinare le quote correnti per aree specifiche, vedere Risolvere gli [errori per le quote di risorse.](../templates/error-resource-quota.md)

## <a name="general-limits"></a>Limiti generali

Per i limiti relativi ai nomi delle risorse, vedere [Regole di denominazione e restrizioni per le risorse](resource-name-rules.md)di Azure.For limits on resource names, see Naming rules and restrictions for Azure resources .

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limiti del gruppo di gestione

I seguenti limiti si applicano ai gruppi di [gestione](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni

I limiti seguenti si applicano quando si usano i gruppi di risorse di Azure Resource Manager e Azure.The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti dei gruppi di risorse

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limiti relativi ad Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limiti relativi a Gestione API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limiti relativi a Servizio app

I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limiti di automazione

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limiti dei servizi cloud di AzureAzure Cloud Services limits

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limiti di Ricerca cognitiva di AzureAzure Cognitive Search limits

I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* Il servizio multi-tenant **gratuito,** condiviso con altri sottoscrittori di Azure, è destinato alla valutazione e ai piccoli progetti di sviluppo.
* **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* **Standard**, che include S1, S2, S3 e S3 High Density, è per carichi di lavoro di produzione più grandi. All'interno del livello Standard sono presenti più livelli in modo da poter scegliere una configurazione di risorse che meglio corrisponde al profilo del carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Per altre informazioni sui limiti a un livello più granulare, ad esempio dimensioni del documento, query al secondo, chiavi, richieste e risposte, vedere [Limiti del servizio in Ricerca cognitiva](../../search/search-limits-quotas-capacity.md)di Azure.To learn more about limits on a more granular level, such as document size, queries per second, keys, requests, and responses, see Service limits in Azure Cognitive Search .

## <a name="azure-cognitive-services-limits"></a>Limiti di Servizi cognitivi di AzureAzure Cognitive Services limits

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB

Per i limiti di Azure Cosmos DB, vedere [Limiti in Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limiti di Azure Data ExplorerAzure Data Explorer limits

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Database di Azure per MySQL

Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL

Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limiti di Funzioni di AzureAzure Functions limits

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Limiti del servizio Azure KubernetesAzure Kubernetes Service limits

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limiti di Azure Machine LearningAzure Machine Learning limits

I valori più recenti per le quote di Calcolo di Azure Machine Learning sono disponibili nella pagina delle quote di [Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limiti di Mappe di Azure

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limiti di Monitoraggio di AzureAzure Monitor limits

### <a name="alerts"></a>Avvisi

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Gruppi di azioni

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Query di log e lingua

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limiti di Criteri di Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Limiti del servizio SignalR di AzureAzure SignalR Service limits

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limiti relativi a Backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limiti relativi a Batch

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limiti del modello di distribuzione classica

Se si usa il modello di distribuzione classica anziché il modello di distribuzione di Azure Resource Manager, si applicano i limiti seguenti.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limiti per Istanze di Container

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limiti per Registro contenitori

Nella tabella seguente vengono descritte in dettaglio le funzionalità e i limiti dei livelli di [servizio](../../container-registry/container-registry-skus.md)Basic, Standard e Premium .

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limiti della rete per la distribuzione di contenuti

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limiti relativi a Data factory

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limiti di Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limiti di Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limiti di condivisione dati

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limiti relativi al Servizio Migrazione del database di Azure

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Limiti della griglia di eventi

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Limiti di Identity Manager

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Limiti di IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limiti relativi all'hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limiti relativi al servizio Device Provisioning in hub IoT

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Servizi multimediali v2 (legacy)

Per i limiti specifici di Servizi multimediali v2 (legacy), vedere [Servizi multimediali v2 (legacy)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limiti di Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limiti relativi alla rete

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limiti di ExpressRouteExpressRoute limits

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Limiti della rete WAN virtuale

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limiti del gateway applicazione

Se non diversamente specificato, la tabella è valida per gli SKU v1, v2, Standard e WAF.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limiti relativi a Network Watcher

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limiti del collegamento privato

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limiti del Bastione di AzureAzure Bastion limits

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limiti DNS di AzureAzure DNS limits

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limiti degli hub di notifica

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Limiti relativi al controllo degli accessi in base al ruolo

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Limiti relativi al bus di servizio

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limiti relativi a database SQL

Per i limiti del database SQL, vedere [Limiti delle risorse del database SQL per singoli database](../../sql-database/sql-database-vcore-resource-limits-single-databases.md), Limiti delle risorse del database SQL per pool [elastici e database in pool](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)e Limiti delle risorse del database SQL per le [istanze gestite.](../../sql-database/sql-database-managed-instance-resource-limits.md)

## <a name="sql-data-warehouse-limits"></a>Limiti relativi a SQL Data Warehouse

Per i limiti di SQL Data Warehouse, vedere [Limiti delle risorse di SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Per altre informazioni sui limiti per gli account di archiviazione standard, vedere Obiettivi di [scalabilità per gli account di archiviazione standard.](../../storage/common/scalability-targets-standard-account.md)

### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Limiti relativi a File di Azure

Per altre informazioni sui limiti di File di Azure, vedere Obiettivi di scalabilità e prestazioni di File di Azure.For more information on Azure Files limits, see [Azure Files scalability and performance targets.](../../storage/files/storage-files-scale-targets.md)

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limiti relativi a Sincronizzazione file di Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limiti relativi ad Archiviazione code di Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limiti relativi ad Archiviazione tabelle di Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limiti relativi ai dischi della macchina virtuale

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Per ulteriori informazioni, vedere [Dimensioni delle macchine virtuali](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limiti relativi ad analisi di flusso

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure

I limiti seguenti si applicano quando si usano i gruppi di risorse di Azure Resource Manager e Azure.The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limiti della raccolta immagini condivise

Esistono limiti, per ogni sottoscrizione, per la distribuzione delle risorse tramite le raccolte di immagini condivise:There are limits, per subscription, for deploying resources using Shared Image Galleries:

- 100 gallerie di immagini condivise, per sottoscrizione, per area geografica
- 1.000 definizioni di immagini, per sottoscrizione, per area
- 10.000 versioni di immagini, per sottoscrizione, per area

## <a name="virtual-machine-scale-sets-limits"></a>La scalabilità delle macchine virtuali limita i limiti

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Vedere anche

* [Comprendere i limiti e gli aumenti di AzureUnderstand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Dimensioni delle macchine virtuali e dei servizi cloud per AzureVirtual machine and cloud service sizes for Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Dimensioni per i servizi cloud di AzureSizes for Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Regole di denominazione e restrizioni per le risorse di Azure](resource-name-rules.md)
