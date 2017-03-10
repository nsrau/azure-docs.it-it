---
title: Limiti e quote della sottoscrizione di Azure | Documentazione Microsoft
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Sono incluse informazioni su come aumentare i limiti e i valori massimi.
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 4e37d976ab7616674ff4b41111bfe7000444ca54
ms.openlocfilehash: 2ff41f4a93b0cc7f104fe4703f5b031cfb3252e1
ms.lasthandoff: 02/21/2017


---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi
In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote. Al momento nel documento non vengono trattati tutti i servizi di Azure. Nel corso del tempo l'elenco verrà ampliato e aggiornato in modo da coprire un maggior numero di servizi della piattaforma.

Vedere [Prezzi di Azure](https://azure.microsoft.com/pricing/) per altre informazioni sui prezzi di Azure. Nella pagina è possibile stimare i costi usando il [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) o visitando la pagina dei dettagli dei prezzi per un servizio, ad esempio [Macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).

> [!NOTE]
> Per aumentare il limite o la quota oltre il valore **Limite predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili. 
> 
> Le sottoscrizioni per le versioni di valutazione gratuite non sono idonee ad aumenti di limite o di quota. Se è disponibile una versione di valutazione gratuita, è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) . Per altre informazioni, vedere [Aggiornare la versione di valutazione gratuita di Azure all'offerta con pagamento in base al consumo](billing-upgrade-azure-subscription.md).
> 

## <a name="limits-and-the-azure-resource-manager"></a>Limiti e Azure Resource Manager
È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse di Azure. Quando si usano i gruppi di risorse, i limiti in precedenza globali vengono gestiti a livello di area con Gestione risorse di Azure. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Nei limiti indicati di seguito è stata aggiunta una nuova tabella che indica eventuali differenze applicate quando si usa Gestione risorse di Azure. Sono ad esempio presenti una tabella **Limiti delle sottoscrizioni** e una tabella **Limiti delle sottoscrizioni - Azure Resource Manager**. Quando un limite si applica a entrambi gli scenari, viene indicato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [!NOTE]
> È importante sottolineare che le quote per le risorse nei gruppi di risorse di Azure sono da intendersi per ogni area accessibile dalla sottoscrizione e non per ogni sottoscrizione, come nel caso delle quote di gestione del servizio. Si considerino, ad esempio. le quote relative ai core. Se è necessario richiedere un aumento della quota con supporto per i core, è necessario stabilire quanti core si desidera usare e in quali aree e quindi effettuare una richiesta specifica per le quote di core del gruppo di risorse di Azure per le quantità e le aree desiderate. Pertanto, se è necessario usare 30 core in Europa occidentale per eseguire l'applicazione, è necessario richiedere in modo specifico 30 core in Europa occidentale. La quota di core per le altre aree non verrà tuttavia aumentata, ma sarà disponibile una quota di 30 core solo in Europa occidentale.
> <!-- -->
> Di conseguenza, può risultare utile stabilire le quote per il gruppo di risorse di Azure necessarie per il carico di lavoro in ogni area e richiedere tale quantità in ogni area in cui si prevede di eseguire la distribuzione. Per altre informazioni su come individuare le quote correnti per aree specifiche, vedere l'argomento relativo alla [risoluzione dei problemi di distribuzione](resource-manager-common-deployment-errors.md) .
> 
> 

## <a name="service-specific-limits"></a>Limiti specifici del servizio
* [Active Directory](#active-directory-limits)
* [Gestione API](#api-management-limits)
* [Servizio app](#app-service-limits)
* [Gateway applicazione](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automazione](#automation-limits)
* [Cache Redis di Azure](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Servizi BizTalk](#biztalk-services-limits)
* [RETE CDN](#cdn-limits)
* [Servizi cloud](#cloud-services-limits)
* [Data Factory](#data-factory-limits)
* [Analisi Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [Hub eventi](#event-hubs-limits)
* [Hub IoT](#iot-hub-limits)
* [Insieme di credenziali di chiave](#key-vault-limits)
* [Log Analytics/Operational Insights](#log-analytics-limits)
* [Servizi multimediali](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Servizi mobili](#mobile-services-limits)
* [Monitoraggio](#monitoring-limits)
* [Autenticazione a più fattori](#multi-factor-authentication)
* [Rete](#networking-limits)
* [Servizio di Hub di notifica](#notification-hub-service-limits)
* [Gruppo di risorse](#resource-group-limits)
* [Utilità di pianificazione](#scheduler-limits)
* [Search](#search-limits)
* [Bus di servizio](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Database SQL](#sql-database-limits)
* [Archiviazione](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Analisi dei flussi](#stream-analytics-limits)
* [Sottoscrizione](#subscription-limits)
* [Gestione traffico](#traffic-manager-limits)
* [Macchine virtuali](#virtual-machines-limits)
* [Set di scalabilità di macchine virtuali](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni
#### <a name="subscription-limits"></a>Limiti delle sottoscrizioni
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limiti delle sottoscrizioni - Azure Resource Manager
I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

Per informazioni sulla gestione dei limiti nelle richieste di Resource Manager, vedere [Throttling Resource Manager requests](resource-manager-request-limits.md) (Limitazione delle richieste di Resource Manager).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti relativi a Gruppo di risorse
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali
#### <a name="virtual-machine-limits"></a>Limiti relativi alla macchina virtuale
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure
I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limiti dei set di scalabilità delle macchine virtuali
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limiti relativi alla rete
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limiti del gateway applicazione
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limiti relativi a DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limiti relativi ad Archiviazione
Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage/storage-scalability-targets.md).
<!--like # storage accts --> 
#### <a name="storage-service-limits"></a>Limiti relativi al servizio di archiviazione
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limiti relativi ai dischi della macchina virtuale 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Vedere [Dimensioni della macchina virtuale](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per altri dettagli.

#### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider di risorse di archiviazione
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>Limiti relativi a Servizi cloud
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limiti relativi a Servizio app
I seguenti limiti del servizio App includono limiti per le App Web, App mobili, App API e App per la logica.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limiti relativi all'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limiti relativi a Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limiti relativi a Servizi BizTalk
La tabella seguente mostra i limiti per i servizi Biztalk di Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>Limiti relativi a DocumentDB
DocumentDB è un database con scalabilità globale in cui la velocità effettiva e lo spazio di archiviazione possono essere ridimensionati in modo da gestire qualsiasi requisito dell'applicazione. Per eventuali domande sulla scalabilità offerta da DocumentDB, inviare un messaggio di posta elettronica all'indirizzo askdocdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Limiti relativi a Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Limiti relativi a Ricerca
I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* *Gratuito* , offre un servizio multi-tenant, condiviso con altri sottoscrittori di Azure, progettato per la valutazione e progetti di sviluppo di piccole dimensioni.
* *Basic* fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di&3; repliche per i carichi di lavoro di query a disponibilità elevata.
* *Standard (S1, S2, S3, S3 ad alta densità)* è per i carichi di lavoro di produzione più consistenti. All'interno del livello standard esistono più livelli per consentire di scegliere una configurazione delle risorse ottimale per il profilo di carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Per informazioni più dettagliati sui limiti, ad esempio dimensioni dei documenti, query al secondo, chiavi, richieste e risposte, vedere [Limiti dei servizi in Ricerca di Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limiti relativi alla rete CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limiti relativi al monitoraggio
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limiti relativi al servizio di Hub di notifica
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limiti relativi al bus di servizio
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limiti relativi all'hub IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limiti relativi a Data factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limiti di Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limiti di Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="stream-analytics-limits"></a>Limiti relativi ad analisi di flusso
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limiti relativi ad Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Limiti relativi a RemoteApp di Azure
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limiti relativi a Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limiti relativi a Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limiti relativi ad Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limiti relativi a Gestione API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limiti relativi a Cache Redis di Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limiti di automazione
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limiti relativi a database SQL
Per i limiti di Database SQL, vedere [Limiti delle risorse dei Database SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Vedere anche
[Informazioni sui limiti di Azure e su come aumentarli](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Dimensioni per i servizi cloud](cloud-services/cloud-services-sizes-specs.md)


