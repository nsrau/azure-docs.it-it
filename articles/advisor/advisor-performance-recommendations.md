---
title: Consigli di Azure Advisor sulle prestazioni | Microsoft Docs
description: Usare Advisor per ottimizzare le prestazioni delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 3caf838fec3a5c0ab847ded85b269df7a66859e0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266746"
---
# <a name="advisor-performance-recommendations"></a>Consigli di Advisor sulle prestazioni

Grazie ai consigli di Azure Advisor sulle prestazioni, è possibile migliorare e aumentare la velocità e la reattività delle applicazioni aziendali critiche. È possibile ottenere da Advisor consigli sulle prestazioni nella scheda **Prestazioni** scheda del dashboard di Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Ridurre la durata DNS nel profilo di Gestione traffico per eseguire più rapidamente il failover a endpoint integri

Le [Impostazioni della durata (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) nel profilo di Gestione traffico consentono di specificare la velocità con cui viene cambiato endpoint, nel caso in cui un determinato endpoint smette di rispondere alle query. Ridurre i valori di durata (TTL) significa che i client verranno indirizzati più rapidamente agli endpoint funzionanti.

Azure Advisor identifica i profili di Gestione Traffico con una durata configurata maggiore e consiglia di configurare la durata (TTL) su 20 o 60 secondi a seconda che il profilo sia configurato o no per il [failover rapido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Migliorare le prestazioni del database con Advisor per database SQL

Advisor fornisce una visualizzazione coerente e consolidata dei consigli per tutte le risorse di Azure. Si integra con Advisor per database SQL per offrire consigli su come migliorare le prestazioni del database SQL di Azure. Advisor per database SQL consente di valutare le prestazioni dei database SQL di Azure analizzando la cronologia relativa all'uso. Offre quindi consigli ideali per l'esecuzione del carico di lavoro tipico del database.

> [!NOTE]
> Per ottenere consigli, è necessario che un database sia stato in uso per circa una settimana, nel corso della quale deve essersi verificata attività coerente. Advisor per database SQL può essere più facilmente ottimizzato per modelli di query coerenti anziché picchi irregolari casuali di attività.

Per altre informazioni su Advisor per database SQL, vedere [Advisor per database SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Migliorare affidabilità e prestazioni di Servizi app

Azure Advisor integra consigli sulle procedure consigliate per migliorare l'esperienza di Servizi app e scoprire le funzionalità della piattaforma pertinente. Esempi di consigli per Servizi app sono:
* Rilevamento delle istanze in cui le risorse di CPU o memoria vengono esaurite dai runtime delle app con opzioni di migrazione.
* Rilevamento delle istanze in cui la collocazione delle risorse come App Web e database può aumentare le prestazioni e ridurre il costo.

Per altre informazioni sui consigli per Servizi app, vedere [Procedure consigliate per Servizio app di Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Per evitare la limitazione di I/O dei dischi, usare il servizio Managed Disks

Advisor identificherà le macchine virtuali che appartengono a un account di archiviazione che sta per raggiungere la destinazione di scalabilità. Ciò lo rende soggetto a limitazione delle richieste di I/O. Advisor consiglierà l'uso di Managed Disks per queste macchine virtuali per evitare il peggioramento delle prestazioni.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali usando l'Archiviazione Premium

Advisor identifica le macchine virtuali con dischi standard e con un volume elevato di transazioni nell'account di archiviazione e consiglia l'aggiornamento a dischi Premium. 

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di Archiviazione Premium memorizzano i dati in unità SSD. Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di macchine virtuali che richiedono un numero elevato di operazioni di I/O al secondo.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Rimuovere l'asimmetria dei dati sulla tabella di SQL data warehouse per aumentare le prestazioni delle query

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati inutile o colli di bottiglia nelle risorse. Advisor rileverà un'asimmetria dei dati della distribuzione maggiore del 15% e consiglierà di ridistribuire i dati e rivedere le selezioni della chiave di distribuzione della tabella. Per altre informazioni sull'identificazione e la rimozione dell'asimmetria, vedere [risoluzione dei problemi di asimmetria](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Creare o aggiornare le statistiche della tabella obsolete nella tabella di SQL data warehouse per aumentare le prestazioni delle query

Advisor identifica le tabelle che non hanno [statistiche della tabella](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) aggiornate e consiglia di crearle o di aggiornarle. Query Optimizer di SQL data warehouse usa le statistiche aggiornate per stimare la cardinalità o il numero di righe nel risultato della query al fine di creare un piano di query di qualità elevata per prestazioni ottimali.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Per migliorare le prestazioni delle query, è possibile passare a un livello superiore per ottimizzare l'utilizzo della cache nelle tabelle di SQL Data Warehouse.

Azure Advisor rileva se SQL Data Warehouse ha una percentuale alta di utilizzo della cache e una percentuale bassa di riscontri. Ciò indica una rimozione dalla cache elevata che può influire negativamente sulle prestazioni di SQL Data Warehouse. Advisor suggerisce di aumentare le prestazioni di SQL Data Warehouse per assicurarsi di allocare una capacità sufficiente di cache per il carico di lavoro.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Convertire le tabelle di SQL Data Warehouse in tabelle replicate per aumentare le prestazioni delle query

Advisor identifica le tabelle che non sono tabelle replicate, ma che potrebbero trarre vantaggio dalla conversione e suggerisce di convertire queste tabelle. I consigli si basano sulle dimensioni, sul numero di colonne, sul tipo di distribuzione e sul numero di partizioni della tabella di SQL Data Warehouse. Il consiglio per il contesto potrebbe includere euristiche aggiuntive. Per altre informazioni su come viene determinato questo consiglio, vedere [Consigli di SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Eseguire la migrazione dell'account di archiviazione ad Azure Resource Manager per ottenere tutte le funzionalità di Azure più recenti

Eseguire la migrazione del modello di distribuzione dell'account di archiviazione ad Azure Resource Manager (ARM) per usufruire delle distribuzioni di modelli, di maggiori opzioni di sicurezza e della possibilità di eseguire l'aggiornamento a un account GPv2 per l'utilizzo delle funzionalità più recenti di Archiviazione di Azure. Advisor identifica eventuali account di archiviazione autonomi che usano il modello di distribuzione classica e consiglia di eseguire la migrazione al modello di distribuzione ARM.

> [!NOTE]
> Gli avvisi classici in Monitoraggio di Azure verranno disattivati nel mese di giugno 2019, è pertanto consigliabile aggiornare l'account di archiviazione classico ad ARM per mantenere la funzionalità di avviso con la nuova piattaforma. Per altre informazioni, vedere [Disattivazione avvisi classici](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Come accedere ai consigli sulle prestazioni in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Prestazioni**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
