---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Questo articolo include informazioni su come aumentare i limiti e i valori massimi.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 04/19/2019
ms.author: byvinyal
ms.openlocfilehash: cb0203e3413ab525d6885f23c5936c64e1bef189
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233697"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi
In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote. Al momento nel documento non vengono trattati tutti i servizi di Azure. Nel corso del tempo, l'elenco verrà espanso e aggiornato per illustrare altri servizi.

Per altre informazioni sui prezzi di Azure, vedere [panoramica dei prezzi Azure](https://azure.microsoft.com/pricing/). Non esiste, è possibile stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/). È anche possibile passare alla pagina di dettagli sui prezzi per un determinato servizio, ad esempio, [macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](billing/billing-getting-started.md).

> [!NOTE]
> Se si desidera aumentare il limite o quota oltre il limite predefinito, [aprire una richiesta di assistenza clienti online senza alcun addebito](azure-resource-manager/resource-manager-quota-errors.md). I limiti non possono essere aumentati oltre il valore di limite massimo indicato nelle tabelle seguenti. Se è presente nessuna colonna limite massimo consentito, la risorsa non è disponibili limiti regolabili.
>
> [Sottoscrizioni di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non sono idonee per aumenti di limite o quota. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per altre informazioni, vedere [aggiornare la sottoscrizione della versione di valutazione gratuita di Azure a una sottoscrizione con pagamento a consumo](billing/billing-upgrade-azure-subscription.md) e il [domande frequenti sulla sottoscrizione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limiti e Azure Resource Manager
È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse Azure. Quando si usano i gruppi di risorse, i limiti in precedenza globali vengono gestiti a livello di area con Azure Resource Manager. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Nell'elenco seguente di limiti, una nuova tabella riflette eventuali differenze applicate quando si usa Azure Resource Manager. Ad esempio, è presente una **i limiti della sottoscrizione** tabella e una **limiti delle sottoscrizioni - Azure Resource Manager** tabella. Quando un limite si applica a entrambi gli scenari, viene visualizzato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [!NOTE]
> Le quote per le risorse nei gruppi di risorse di Azure sono per ogni area accessibile dalla sottoscrizione, non per ogni sottoscrizione come le quote di gestione del servizio. Si considerino, ad esempio, le quote di vCPU. Per richiedere un aumento della quota con supporto per le Vcpu, è necessario stabilire quante Vcpu si vuole usare e in quali aree. Quindi possibile effettuare una richiesta specifica per le quote di vCPU gruppo di risorse di Azure per le quantità e le aree desiderate. Se è necessario usare 30 Vcpu in Europa occidentale per eseguire l'applicazione, richiesta in modo specifico 30 Vcpu in Europa occidentale. La quota di vCPU non viene aumentata in un'altra area, solo Europa occidentale abbia la quota di 30 vCPU.
> <!-- -->
> Di conseguenza, decidere quali devono essere le quote di gruppo di risorse di Azure per il carico di lavoro in ogni area. È quindi richiedere tale quantità in ogni area in cui si desidera distribuire. Per informazioni su come determinare le quote correnti per aree specifiche, vedere [risolvere i problemi di distribuzione](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limiti specifici del servizio
* [Active Directory](#active-directory-limits)
* [Gestione API](#api-management-limits)
* [Servizio app](#app-service-limits)
* [Gateway applicazione](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automazione](#automation-limits)
* [Cache Redis di Azure](#azure-cache-for-redis-limits)
* [Servizi cloud di Azure](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Database di Azure per MySQL](#azure-database-for-mysql)
* [Database di Azure per PostgreSQL](#azure-database-for-postgresql)
* [DNS di Azure](#azure-dns-limits)
* [Firewall di Azure](#azure-firewall-limits)
* [Servizio Azure Kubernetes](#azure-kubernetes-service-limits)
* [Mappe di Azure](#azure-maps-limits)
* [Monitoraggio di Azure](#monitor-limits)
* [Criteri di Azure](#azure-policy-limits)
* [Ricerca di Azure](#azure-search-limits)
* [Servizio Azure SignalR](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Servizi BizTalk](#biztalk-services-limits)
* [Istanze di Container](#container-instances-limits)
* [Registro Container](#container-registry-limits)
* [Rete CDN](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Analisi Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Servizio Migrazione del database di Azure](#database-migration-service-limits)
* [Griglia di eventi](#event-grid-limits)
* [Hub eventi](#event-hubs-limits)
* [Servizio di ingresso principale](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [Hub IoT](#iot-hub-limits)
* [Servizio Device Provisioning in hub IoT](#iot-hub-device-provisioning-service-limits)
* [Insieme di credenziali di chiave](#key-vault-limits)
* [Bilanciamento del carico](#load-balancer)
* [Log Analytics](#log-analytics-limits)
* [Servizi multimediali](#media-services-limits)
* [Servizi mobili](#mobile-services-limits)
* [Autenticazione a più fattori](#multi-factor-authentication-limits)
* [Rete](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Hub di notifica di Azure](#notification-hubs-limits)
* [Indirizzo IP pubblico](#publicip-address)
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
* [Gestione traffico](#traffic-manager-limits)
* [Macchine virtuali](#virtual-machines-limits)
* [Set di scalabilità di macchine virtuali](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limiti delle sottoscrizioni - Azure Service Management (modello di distribuzione classica)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti delle sottoscrizioni - Azure Resource Manager
I limiti seguenti si applicano quando si usa Azure Resource Manager e i gruppi di risorse di Azure. Non sono presenti limiti che non sono stati modificati con Azure Resource Manager. Vedere la tabella precedente per tali limiti.

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti delle risorse del gruppo
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
#### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure
I limiti seguenti si applicano quando si usa Azure Resource Manager e i gruppi di risorse di Azure. Non sono presenti limiti che non sono stati modificati con Azure Resource Manager. Vedere la tabella precedente per tali limiti.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>I limiti di raccolta di immagini condivise

Sono previsti limiti, per ogni sottoscrizione, per la distribuzione di risorse usando raccolte di immagini condivise:
- 100 raccolte di immagini condivise, per ogni sottoscrizione per area
- 1.000 definizioni di immagini, per ogni sottoscrizione per area
- 10.000 versioni dell'immagine, per ogni sottoscrizione per area

### <a name="virtual-machine-scale-sets-limits"></a>Limiti dei set di scalabilità di macchine virtuali
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limiti per Istanze di Container
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limiti per Registro contenitori
La tabella seguente illustra le funzionalità e i limiti dei [livelli di servizio](./container-registry/container-registry-skus.md) Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limiti relativi ad Azure Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limiti di ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Limiti del gateway applicazione

Se non diversamente specificato, la tabella è valida per gli SKU v1, v2, Standard e WAF.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limiti relativi a Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Limiti relativi a DNS Azure
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Per altre informazioni sui limiti di account di archiviazione, vedere [obiettivi di scalabilità e prestazioni di archiviazione di Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limiti relativi a File di Azure
Per altre informazioni sui limiti di file di Azure, vedere [obiettivi di scalabilità e prestazioni di file di Azure](storage/files/storage-files-scale-targets.md).

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

Per altre informazioni, vedere [dimensioni delle macchine virtuali](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limiti dei servizi Cloud di Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limiti relativi a Servizio app
I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limiti relativi all'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti relativi a Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limiti relativi a Servizi BizTalk
Nella tabella seguente illustra i limiti per i servizi BizTalk di Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB
Azure Cosmos DB è un database con scalabilità globale in cui la velocità effettiva e lo spazio di archiviazione possono essere ridimensionati in modo da gestire qualsiasi requisito dell'applicazione. Se si hanno domande sulla scalabilità offerta da Azure Cosmos DB, inviare posta elettronica a askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Database di Azure per MySQL
Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL
Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Limiti di ricerca di Azure
I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* **Gratuito** servizio multi-tenant, condiviso con altri sottoscrittori di Azure, è progettato per la valutazione e progetti di sviluppo di piccole dimensioni.
* **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* **Standard**, che include l'edizione S1, S2 e S3 e S3 ad alta densità, sia per i carichi di lavoro più grandi. All'interno del livello Standard esistono più livelli in modo che sia possibile scegliere una configurazione di risorsa che corrisponde maggiormente il profilo di carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Per informazioni più dettagliati sui limiti, ad esempio dimensioni dei documenti, query al secondo, chiavi, richieste e risposte, vedere [Limiti dei servizi in Ricerca di Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limiti di rete CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Limiti relativi al monitoraggio
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Limiti di hub di notifica
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limiti relativi al bus di servizio
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

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

### <a name="event-grid-limits"></a>Limiti relativi a griglia di eventi
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limiti di Mappe di Azure
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limiti di Criteri di Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limiti relativi a Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limiti relativi a Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limiti di SignalR Service di Azure
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limiti relativi ad Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limiti relativi a Gestione API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limiti dell'autenticazione a più fattori
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limiti di Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limiti relativi al controllo degli accessi in base al ruolo
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limiti relativi a database SQL
Per i limiti di Database SQL, vedere [limiti delle risorse del Database SQL per database singoli](sql-database/sql-database-vcore-resource-limits-single-databases.md), [limiti delle risorse del Database SQL per i pool elastici e i database in pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), e [limiti delle risorse del Database SQL per le istanze gestite](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limiti relativi a SQL Data Warehouse
Per i limiti di SQL Data Warehouse, vedere [i limiti delle risorse di SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Vedere anche 
- [Comprendere gli aumenti e limiti di Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Dimensioni dei servizi cloud e macchine virtuali di Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Dimensioni dei servizi Cloud di Azure](cloud-services/cloud-services-sizes-specs.md)
