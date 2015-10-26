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
	ms.date="08/29/2015"
	ms.author="jroth"/>

# Sottoscrizione di Azure e limiti dei servizi, quote e vincoli

## Panoramica

Questo documento illustra alcuni dei limiti più comuni di Microsoft Azure. Si noti che nel documento non vengono trattati tutti i servizi di Azure. Nel tempo questi limiti verranno ampliati e aggiornati in modo da coprire un maggior numero di servizi della piattaforma.

> [AZURE.NOTE]Per aumentare il limite oltre il valore **Limite predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili.

## Limiti e Gestione risorse di Azure

È ora possibile combinare più risorse di Azure in un singolo gruppo di risorse di Azure. Quando si usano i gruppi di risorse, i limiti in precedenza globali vengono gestiti a livello di area con Gestione risorse di Azure. Per altre informazioni sui gruppi di risorse di Azure, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](resource-group-portal.md).

Nei limiti indicati di seguito è stata aggiunta una nuova tabella che indica eventuali differenze applicate quando si usa Gestione risorse di Azure. Sono ad esempio presenti una tabella **Limiti relativi alle sottoscrizioni** e una tabella **Limiti relativi alle sottoscrizioni - Gestione risorse di Azure**. Quando un limite si applica a entrambi gli scenari, viene indicato solo nella prima tabella. Se non diversamente indicato, i limiti sono globali in tutte le aree.

> [AZURE.NOTE] È importante sottolineare che le quote per le risorse nei gruppi di risorse di Azure sono da intendersi per ogni area accessibile dalla sottoscrizione e non per ogni sottoscrizione, come nel caso delle quote di gestione del servizio. Si considerino, ad esempio. le quote relative ai core. Se è necessario richiedere un aumento della quota con supporto per i core, è necessario stabilire quanti core si desidera usare e in quali aree e quindi effettuare una richiesta specifica per le quote di core del gruppo di risorse di Azure per le quantità e le aree desiderate. Pertanto, se è necessario usare 30 core in Europa occidentale per eseguire l'applicazione, è necessario richiedere in modo specifico 30 core in Europa occidentale. La quota di core per le altre aree non verrà tuttavia aumentata, ma sarà disponibile una quota di 30 core solo in Europa occidentale.
<!-- -->
Di conseguenza, può risultare utile stabilire le quote per il gruppo di risorse di Azure necessarie per il carico di lavoro in ogni area e richiedere tale quantità in ogni area in cui si prevede di eseguire la distribuzione. Per altre informazioni su come individuare le quote correnti per aree specifiche, vedere l'argomento relativo alla [risoluzione dei problemi di distribuzione](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues).

## Limiti specifici del servizio

- [Active Directory](#active-directory-limits)
- [Gestione API](#api-management-limits)
- [Servizio app](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Cache Redis di Azure](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Backup](#backup-limits)
- [Batch](#batch-limits)
- [Servizi BizTalk](#biztalk-services-limits)
- [RETE CDN](#cdn-limits)
- [Servizi cloud](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
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


### Limiti relativi ai gruppi di risorse

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Limiti relativi alle macchine virtuali
#### Limiti relativi alle macchine virtuali
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Limiti relativi alle macchine virtuali - Gestione risorse di Azure

I limiti seguenti si applicano quando si usano Gestione risorse di Azure e i gruppi di risorse di Azure. I limiti che non cambiano in caso di uso di Gestione risorse di Azure non sono elencati di seguito. Per tali limiti, fare riferimento alla tabella precedente.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### Limiti relativi alle reti

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### Limiti relativi alle reti
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Limiti di gestione traffico

[AZURE.INCLUDE [limiti di gestione traffico](../includes/traffic-manager-limits.md)]

#### Limiti DNS

[AZURE.INCLUDE [limiti di dns](../includes/dns-limits.md)]

### Limiti relativi all'archiviazione

#### Limiti relativi al servizio di archiviazione Standard

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md).


#### Limiti relativi al servizio di archiviazione Premium

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../includes/azure-storage-limits-premium-storage.md)]


#### Limiti relativi al servizio di archiviazione - Gestione risorse di Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Limiti relativi ai servizi cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### Limiti del servizio App
I seguenti limiti del servizio App includono limiti per le App Web, App mobili, App API e App per la logica.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Limiti dell'utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Limiti relativi ai batch

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###Limiti dei servizi BizTalk
La tabella seguente mostra i limiti per i servizi Biztalk di Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### Limiti relativi a DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Limiti relativi a Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Limiti relativi alla ricerca

[AZURE.INCLUDE [azure-search-limits](../includes/azure-search-limits.md)]

Per altre informazioni sui limiti di Ricerca di Azure, vedere [Limiti e Vincoli](https://msdn.microsoft.com/library/azure/dn798934.aspx).

### Limiti relativi ai servizi multimediali

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### Limiti della rete CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Limiti relativi a Servizi mobili

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Limiti relativi all'Hub di notifica

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Limiti relativi al bus di servizio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### Limiti di Data factory

[AZURE.INCLUDE [limiti di data factory Azure](../includes/azure-data-factory-limits.md)]


### Limiti relativi ad Analisi dei flussi

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Limiti relativi ad Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Limiti relativi a RemoteApp di Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### Limiti relativi al sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Limiti di Operational Insights

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Limiti relativi al backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Limiti relativi a Ripristino sito

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Limiti di Application Insights

[AZURE.INCLUDE [limiti di application-insights](../includes/application-insights-limits.md)]

### Limiti relativi a Gestione API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Limiti relativi a Cache Redis di Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Limiti relativi a Insieme di credenziali chiave

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Autenticazione a più fattori
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### Limiti relativi al database SQL

Per i limiti di Database SQL, vedere [Limiti delle risorse dei Database SQL](sql-database/sql-database-resource-limits.md).

## Vedere anche

[Informazioni sui limiti di Azure e su come aumentarli](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=Oct15_HO3-->