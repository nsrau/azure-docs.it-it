<properties
	pageTitle="Limiti quote e vincoli delle sottoscrizioni e dei servizi di Azure"
	description="Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Sono incluse informazioni su come aumentare i limiti e i valori massimi."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="jroth"/>

# Sottoscrizione di Azure e limiti, quote e vincoli dei servizi

## Panoramica

Questo documento illustra alcuni dei limiti più comuni di Microsoft Azure. Si noti che nel documento non vengono trattati tutti i servizi di Azure. Nel tempo questi limiti verranno ampliati e aggiornati in modo da coprire un maggior numero di servizi della piattaforma.

> [AZURE.NOTE] Per aumentare il limite oltre il valore **Limite predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili.

## Limiti e Gestione risorse di Azure

È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse di Azure. Quando si usano i gruppi di risorse, i limiti in precedenza globali vengono gestiti a livello di area con Gestione risorse di Azure. Per altre informazioni sui gruppi di risorse di Azure, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](azure-portal/resource-group-portal.md).

Nei limiti indicati di seguito è stata aggiunta una nuova tabella che indica eventuali differenze applicate quando si usa Gestione risorse di Azure. Sono ad esempio presenti una tabella **Limiti relativi alle sottoscrizioni** e una tabella **Limiti relativi alle sottoscrizioni - Gestione risorse di Azure**. Quando un limite si applica a entrambi gli scenari, viene indicato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [AZURE.NOTE] È importante sottolineare che le quote per le risorse nei gruppi di risorse di Azure sono da intendersi per ogni area accessibile dalla sottoscrizione e non per ogni sottoscrizione, come nel caso delle quote di gestione del servizio. Si considerino, ad esempio. le quote relative ai core. Se è necessario richiedere un aumento della quota con supporto per i core, è necessario stabilire quanti core si desidera usare e in quali aree e quindi effettuare una richiesta specifica per le quote di core del gruppo di risorse di Azure per le quantità e le aree desiderate. Pertanto, se è necessario usare 30 core in Europa occidentale per eseguire l'applicazione, è necessario richiedere in modo specifico 30 core in Europa occidentale. La quota di core per le altre aree non verrà tuttavia aumentata, ma sarà disponibile una quota di 30 core solo in Europa occidentale.
<!-- -->
Di conseguenza, può risultare utile stabilire le quote per il gruppo di risorse di Azure necessarie per il carico di lavoro in ogni area e richiedere tale quantità in ogni area in cui si prevede di eseguire la distribuzione. Per altre informazioni su come individuare le quote correnti per aree specifiche, vedere l'argomento relativo alla [risoluzione dei problemi di distribuzione](./resource-manager-common-deployment-errors.md).


## Limiti specifici del servizio

- [Active Directory](#active-directory-limits)
- [Gestione API](#api-management-limits)
- [Servizio app](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Automazione](#automation-limits)
- [Cache Redis di Azure](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Backup](#backup-limits)
- [Batch](#batch-limits)
- [Servizi BizTalk](#biztalk-services-limits)
- [RETE CDN](#cdn-limits)
- [Servizi cloud](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [Analisi Data Lake](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Hub IoT](#iot-hub-limits)
- [Insieme di credenziali di chiave](#key-vault-limits)
- [Servizi multimediali](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [Servizi mobili](#mobile-services-limits)
- [Autenticazione a più fattori](#multi-factor-authentication)
- [Rete](#networking-limits)
- [Servizio di Hub di notifica](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [Gruppo di risorse](#resource-group-limits)
- [Utilità di pianificazione](#scheduler-limits)
- [Search](#search-limits)
- [Bus di servizio](#service-bus-limits)
- [Site Recovery](#site-recovery-limits)
- [Database SQL](#sql-database-limits)
- [Archiviazione](#storage-limits)
- [Sistema StorSimple](#storsimple-system-limits)
- [Analisi dei flussi](#stream-analytics-limits)
- [Sottoscrizione](#subscription-limits)
- [Gestione traffico](#traffic-manager-limits)
- [Macchine virtuali](#virtual-machines-limits)


### Limiti relativi alle sottoscrizioni
#### Limiti relativi alle sottoscrizioni
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### Limiti relativi alle sottoscrizioni - Gestione risorse di Azure

I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### Limiti relativi a Gruppo di risorse

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Limiti relativi a Macchine virtuali
#### Limiti relativi alla macchina virtuale
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Limiti relativi a Macchine virtuali - Gestione risorse di Azure

I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### Limiti relativi alla rete

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### Limiti relativi alla rete
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Limiti relativi a Gestione traffico

[AZURE.INCLUDE [limiti di gestione traffico](../includes/traffic-manager-limits.md)]

#### Limiti relativi a DNS

[AZURE.INCLUDE [limiti di dns](../includes/dns-limits.md)]

### Limiti relativi ad Archiviazione

Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md).

#### Limiti relativi al servizio di archiviazione

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### Limiti relativi ai dischi della macchina virtuale

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Vedere [Dimensioni della macchina virtuale](../articles/virtual-machines/virtual-machines-linux-sizes.md) per altri dettagli.

**Account di archiviazione standard**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Account di archiviazione premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### Limiti relativi al provider di risorse di archiviazione

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Limiti relativi a Servizi cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### Limiti relativi a Servizio app
I seguenti limiti del servizio App includono limiti per le App Web, App mobili, App API e App per la logica.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Limiti relativi all'utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Limiti relativi a Batch

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###Limiti relativi a Servizi BizTalk
La tabella seguente mostra i limiti per i servizi Biztalk di Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### Limiti relativi a DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Limiti relativi a Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Limiti relativi a Ricerca

I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

- **Gratuito**, che offre un servizio multi-tenant, condiviso con altri sottoscrittori di Azure, progettato per la valutazione e progetti di sviluppo di piccole dimensioni.
- **Basic (anteprima)** che offre risorse di elaborazione dedicate per i carichi di lavoro di produzione su scala ridotta. Questo livello, attualmente in anteprima, viene offerto a una tariffa ridotta.
- **Standard (S1 e S2)**, per i carichi di lavoro di produzione. Una versione di capacità più grande (**S2**) è disponibile su richiesta (inviare un messaggio di posta elettronica a azuresearch_contact@microsoft.com).

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

Per informazioni su altri limiti, quali le dimensioni dei documenti, le chiavi, le richieste e le risposte, vedere [Limiti dei servizi in Ricerca di Azure](search/search-limits-quotas-capacity.md).

### Limiti relativi a Servizi multimediali

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### Limiti relativi alla rete CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Limiti relativi a Servizi mobili

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Limiti relativi al servizio di Hub di notifica

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Limiti relativi al bus di servizio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### Limiti relativi all'hub IoT

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Limiti relativi a Data factory

[AZURE.INCLUDE [limiti di data factory Azure](../includes/azure-data-factory-limits.md)]

### Limiti di Analisi Data Lake
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### Limiti relativi ad analisi di flusso

| Identificatore limite | Limite | Commenti |
|----------------- | ------------|--------- |
| Numero massimo di unità di streaming per sottoscrizione per area | 50 | Per richiedere un aumento delle unità di streaming per la sottoscrizione a oltre 50 è possibile contattare il [supporto Microsoft](https://support.microsoft.com/it-IT). |
| Velocità effettiva massima di un'unità di streaming | 1 MB/s* | La velocità effettiva massima per unità di streaming dipende dallo scenario. La velocità effettiva potrebbe essere inferiore e dipende dalla complessità della query e dal partizionamento. Ulteriori dettagli sono disponibili nell’articolo [Scalabilità dei processi di Analisi di flusso di Azure per aumentare la velocità effettiva](../articles/stream-analytics/stream-analytics-scale-jobs.md). |

### Limiti relativi ad Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Limiti relativi a RemoteApp di Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### Limiti relativi al sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Limiti relativi a Operational Insights

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Limiti relativi a Backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Limiti relativi a Site Recovery

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Limiti relativi ad Application Insights

[AZURE.INCLUDE [limiti di application-insights](../includes/application-insights-limits.md)]

### Limiti relativi a Gestione API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Limiti relativi a Cache Redis di Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Limiti relativi all'insieme di credenziali delle chiavi

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Autenticazione a più fattori
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### Limiti di automazione
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### Limiti relativi a database SQL

Per i limiti di Database SQL, vedere [Limiti delle risorse dei Database SQL](sql-database/sql-database-resource-limits.md).

## Vedere anche

[Informazioni sui limiti di Azure e su come aumentarli](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Dimensioni per i servizi cloud](cloud-services/cloud-services-sizes-specs.md)

<!----HONumber=AcomDC_0525_2016-->