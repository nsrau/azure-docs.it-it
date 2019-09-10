---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Questo articolo include informazioni su come aumentare i limiti insieme ai valori massimi.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 7c4c80950e43f374b40085cb4e1c3e026e5f3abd
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "68698262"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi
In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote. Al momento nel documento non vengono trattati tutti i servizi di Azure. Nel corso del tempo, l'elenco verrà espanso e aggiornato per coprire più servizi.

Per altre informazioni sui prezzi di Azure, vedere [Panoramica dei prezzi di Azure](https://azure.microsoft.com/pricing/). Qui è possibile stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/). È anche possibile passare alla pagina dei dettagli prezzi per un particolare servizio, ad esempio [macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](billing/billing-getting-started.md).

> [!NOTE]
> Per aumentare il limite o la quota superiore al limite predefinito, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](azure-resource-manager/resource-manager-quota-errors.md). I limiti non possono essere aumentati oltre il valore limite massimo illustrato nelle tabelle seguenti. Se non è presente alcuna colonna limite massimo, la risorsa non ha limiti regolabili.
>
> Le [sottoscrizioni della versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non sono idonee per aumenti limite o quota Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per altre informazioni, vedere [aggiornare la sottoscrizione della versione di valutazione gratuita di Azure a una sottoscrizione con pagamento in base](billing/billing-upgrade-azure-subscription.md) al consumo e le [domande frequenti sulla sottoscrizione della versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limiti e Azure Resource Manager
È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse di Azure. Quando si usano i gruppi di risorse, i limiti che una volta venivano gestiti globalmente a livello di area con Azure Resource Manager. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Nel seguente elenco di limiti, una nuova tabella riflette eventuali differenze nei limiti quando si usa Azure Resource Manager. Ad esempio, è presente una tabella dei **limiti della sottoscrizione** e una tabella relativa ai **limiti di sottoscrizione Azure Resource Manager** . Quando si applica un limite a entrambi gli scenari, viene visualizzato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [!NOTE]
> Le quote per le risorse nei gruppi di risorse di Azure sono accessibili per ogni area dalla sottoscrizione, non per sottoscrizione come le quote di gestione del servizio. Si considerino, ad esempio, le quote di vCPU. Per richiedere un aumento della quota con supporto per vCPU, è necessario decidere il numero di vCPU che si vuole usare in quali aree. Si crea quindi una richiesta specifica per le quote vCPU del gruppo di risorse di Azure per le quantità e le aree desiderate. Se è necessario usare 30 vCPU in Europa occidentale per eseguire l'applicazione, si richiede in modo specifico 30 vCPU in Europa occidentale. La quota di vCPU non è aumentata in nessun'altra area, ma la quota di 30 vCPU è solo in Europa occidentale.
> <!-- -->
> Di conseguenza, decidere quali quote del gruppo di risorse di Azure devono essere per il carico di lavoro in una qualsiasi area. Quindi richiedere tale quantità in ogni area in cui si vuole eseguire la distribuzione. Per informazioni su come determinare le quote correnti per aree specifiche, vedere risolvere i [problemi di distribuzione](resource-manager-common-deployment-errors.md).
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
* [Servizi cognitivi di Azure](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Database di Azure per MySQL](#azure-database-for-mysql)
* [Database di Azure per PostgreSQL](#azure-database-for-postgresql)
* [DNS di Azure](#azure-dns-limits)
* [Firewall di Azure](#azure-firewall-limits)
* [Funzioni di Azure](#functions-limits)
* [Servizio Azure Kubernetes](#azure-kubernetes-service-limits)
* [Servizio Azure Machine Learning](#azure-machine-learning-service-limits)
* [Mappe di Azure](#azure-maps-limits)
* [Monitoraggio di Azure](#azure-monitor-limits)
* [Criteri di Azure](#azure-policy-limits)
* [Ricerca di Azure](#azure-search-limits)
* [Servizio Azure SignalR](#azure-signalr-service-limits)
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
* [Servizio front door](#azure-front-door-service-limits)
* [Gestione identità](#identity-manager-limits)
* [Hub IoT](#iot-hub-limits)
* [Servizio Device Provisioning in hub IoT](#iot-hub-device-provisioning-service-limits)
* [Insieme di credenziali di chiave](#key-vault-limits)
* [Servizi multimediali](#media-services-limits)
* [Servizi mobili](#mobile-services-limits)
* [Autenticazione a più fattori](#multi-factor-authentication-limits)
* [Rete](#networking-limits)
  * [Gateway applicazione](#application-gateway-limits)
  * [DNS di Azure](#azure-dns-limits)
  * [Servizio Frontdoor di Azure](#azure-front-door-service-limits)
  * [Firewall di Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Bilanciamento del carico](#load-balancer)
  * [Indirizzo IP pubblico](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Gestione traffico](#traffic-manager-limits)
  * [Rete virtuale](#networking-limits)
* [Hub di notifica di Azure](#notification-hubs-limits)
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
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limiti delle sottoscrizioni-gestione dei servizi di Azure (modello di distribuzione classica)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti delle sottoscrizioni - Azure Resource Manager
Quando si usano Azure Resource Manager e i gruppi di risorse di Azure, si applicano i limiti seguenti. I limiti che non sono stati modificati con Azure Resource Manager non sono elencati. Vedere la tabella precedente per questi limiti.

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti del gruppo di risorse
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
#### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure
Quando si usano Azure Resource Manager e i gruppi di risorse di Azure, si applicano i limiti seguenti. I limiti che non sono stati modificati con Azure Resource Manager non sono elencati. Vedere la tabella precedente per questi limiti.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Limiti della raccolta immagini condivise

Per la distribuzione delle risorse tramite le raccolte di immagini condivise sono previsti limiti, per sottoscrizione:
- 100 raccolte di immagini condivise, per sottoscrizione, per area
- 1\.000 definizioni di immagine, per sottoscrizione, per area
- 10.000 versioni dell'immagine, per sottoscrizione, per area

### <a name="virtual-machine-scale-sets-limits"></a>Limiti dei set di scalabilità di macchine virtuali
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limiti per Istanze di Container
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limiti per Registro contenitori
La tabella seguente illustra le funzionalità e i limiti dei [livelli di servizio](./container-registry/container-registry-skus.md) Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limiti del servizio Azure Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Limiti del servizio Azure Machine Learning
I valori più recenti per Azure Machine Learning quote di calcolo sono disponibili nella [pagina Azure Machine Learning quota](../articles/machine-learning/service/how-to-manage-quotas.md)

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

#### <a name="azure-dns-limits"></a>Limiti di DNS di Azure
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Per altre informazioni sui limiti dell'account di archiviazione, vedere [obiettivi di scalabilità e prestazioni per archiviazione di Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limiti relativi a File di Azure
Per altre informazioni sui limiti di File di Azure, vedere [obiettivi di scalabilità e prestazioni di file di Azure](storage/files/storage-files-scale-targets.md).

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

### <a name="azure-cloud-services-limits"></a>Limiti di servizi cloud di Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Limiti dei servizi cognitivi di Azure
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Limiti relativi a Servizio app
I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Limiti delle funzioni
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limiti relativi all'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti relativi a Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limiti relativi a Servizi BizTalk
La tabella seguente illustra i limiti per i servizi BizTalk di Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB
Per Azure Cosmos DB limiti, vedere [limiti nella Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Database di Azure per MySQL
Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL
Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Limiti di ricerca di Azure
I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* Il servizio multi-tenant **gratuito** , condiviso con altri Sottoscrittori di Azure, è destinato a progetti di valutazione e di sviluppo di piccole dimensioni.
* **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* **Standard**, che include S1, S2, S3 e S3 High Density, è destinato a carichi di lavoro di produzione di dimensioni maggiori. All'interno del livello standard esistono più livelli, in modo che sia possibile scegliere una configurazione di risorsa più adatta al proprio profilo di carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Per informazioni più dettagliati sui limiti, ad esempio dimensioni dei documenti, query al secondo, chiavi, richieste e risposte, vedere [Limiti dei servizi in Ricerca di Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limiti della rete per la distribuzione di contenuti
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Limiti di monitoraggio di Azure

#### <a name="alerts"></a>Avvisi

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Gruppi di azioni

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Query e linguaggio di log

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




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

### <a name="event-grid-limits"></a>Limiti di griglia di eventi
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limiti di Mappe di Azure
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limiti di Criteri di Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Limiti relativi a Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limiti del servizio Azure SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Limiti relativi a Gestione API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limiti di Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limiti di Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limiti relativi al controllo degli accessi in base al ruolo
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limiti relativi a database SQL
Per i limiti del database SQL, vedere limiti [delle risorse del database SQL per database singoli](sql-database/sql-database-vcore-resource-limits-single-databases.md), [limiti delle risorse del database SQL per pool elastici e database in pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)e [limiti delle risorse del database SQL per le istanze gestite](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limiti relativi a SQL Data Warehouse
Per SQL Data Warehouse limiti, vedere [limiti delle risorse SQL data warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Vedere anche
- [Informazioni sui limiti e sugli aumenti di Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Dimensioni dei servizi cloud di Azure](cloud-services/cloud-services-sizes-specs.md)
